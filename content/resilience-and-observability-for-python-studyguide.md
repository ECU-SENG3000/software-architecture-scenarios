# Resilience, Observability, and Performance — Python Study Guide

This guide mirrors the .NET/Aspire study guide but focuses on Python stacks (FastAPI/ASGI, aiohttp, requests, Celery, etc.). It covers practical patterns and examples for retries/timeouts, circuit breakers, cache/fallback, queue/messaging, and the three pillars of observability: logs, traces, and metrics — plus performance tips.

---

Quick orientation: prefer async libraries (httpx, aiohttp) for IO-bound services, use structured logging, instrument with OpenTelemetry, and use background workers (Celery, RQ, or asyncio tasks) for durable processing.

---

## Resilience

Resilience patterns protect your application from transient and persistent failures.

- Retry (with timeout)
  - Purpose: survive transient network or service errors.
  - Best practice: limit attempts, use exponential backoff + jitter, combine with per-request timeouts so retries don't block resources.
  - Libraries: `tenacity`, `backoff`, or built-in retry logic in clients.

Example with `httpx` (async) + `tenacity`:

```python
import httpx
from tenacity import AsyncRetrying, stop_after_attempt, wait_exponential, retry_if_exception_type

async def fetch(url):
    async for attempt in AsyncRetrying(stop=stop_after_attempt(3),
                                       wait=wait_exponential(multiplier=1, min=1, max=10),
                                       retry=retry_if_exception_type(httpx.RequestError)):
        with attempt:
            async with httpx.AsyncClient(timeout=10.0) as client:
                resp = await client.get(url)
                resp.raise_for_status()
                return resp.json()
```

- Circuit Breaker
  - Purpose: stop repeatedly calling a downstream that is persistently failing; fail fast and let the system recover.
  - Libraries: `pybreaker` (sync), `aiobreaker` (async) or custom logic.

Example using `aiobreaker`:

```python
from aiobreaker import CircuitBreaker

breaker = CircuitBreaker(fail_max=5, reset_timeout=30)  # 5 failures, 30s open

@breaker
async def call_service(url):
    async with httpx.AsyncClient(timeout=10) as client:
        r = await client.get(url)
        r.raise_for_status()
        return r.json()
```

  - Tune `fail_max` and `reset_timeout` to your traffic and recovery patterns. Combine with a small retry policy inside Closed state.

- Cache / Fallback
  - Purpose: return cached or default responses to reduce downstream load and preserve user experience.
  - Libraries: `cachetools` (in-process), `aioredis` or `redis-py` (distributed), `dogpile.cache` (advanced), or in-memory LRU caches.

Example fallback using cache (sync style for clarity):

```python
from cachetools import TTLCache
cache = TTLCache(maxsize=1024, ttl=300)

def get_data(key):
    try:
        data = fetch_remote(key)  # could raise
        cache[key] = data
        return data
    except Exception:
        return cache.get(key, {"status": "unavailable"})
```

- Queue / Messaging
  - Purpose: decouple producers and consumers, smooth traffic spikes, and enable durable processing.
  - Options: Celery (with RabbitMQ/Redis), RQ, Faust/Kafka, or AWS SQS/Kinesis.
  - Use workers to process tasks reliably and apply retries/poison-queue handling.

Example Celery task:

```python
from celery import Celery

app = Celery("worker", broker="pyamqp://guest@localhost//")

@app.task(bind=True, autoretry_for=(Exception,), retry_backoff=True, max_retries=3)
def process_message(self, payload):
    # do work, call external service with retries/circuit-breaker
    pass
```

---

## Observability

Instrument logs, traces, and metrics so you can detect and diagnose issues quickly.

- Logs
  - Use the standard `logging` module with structured logging (JSON) via `structlog` or `python-json-logger`.
  - Include request IDs / trace IDs in logs to correlate with traces.

Example `structlog` setup:

```python
import logging
import structlog

logging.basicConfig(level=logging.INFO)
structlog.configure(processors=[structlog.processors.JSONRenderer()])
log = structlog.get_logger()
log.info("service_start", service="orders")
```

- Traces
  - Use OpenTelemetry Python to instrument web frameworks, HTTP clients, and DB drivers.
  - Export to OTLP/Jaeger/Zipkin for visualization.

Example (FastAPI + OpenTelemetry):

```python
from opentelemetry import trace
from opentelemetry.instrumentation.fastapi import FastAPIInstrumentor
from opentelemetry.instrumentation.httpx import HTTPXClientInstrumentor
from opentelemetry.exporter.otlp.proto.http.trace_exporter import OTLPSpanExporter
from opentelemetry.sdk.trace import TracerProvider
from opentelemetry.sdk.trace.export import BatchSpanProcessor

provider = TracerProvider()
processor = BatchSpanProcessor(OTLPSpanExporter())
provider.add_span_processor(processor)
trace.set_tracer_provider(provider)

# In app startup
FastAPIInstrumentor.instrument_app(app)
HTTPXClientInstrumentor().instrument()
```

- Metrics
  - Use `prometheus_client` or OpenTelemetry Metrics to record counters, histograms, and gauges.
  - Export metrics to Prometheus and build dashboards for error rate, latency percentiles, queue depth, and worker utilization.

Example Prometheus metrics for FastAPI:

```python
from prometheus_client import Counter, Histogram, start_http_server

REQUESTS = Counter('requests_total', 'Total requests', ['endpoint'])
LATENCY = Histogram('request_latency_seconds', 'Request latency', ['endpoint'])

start_http_server(8000)  # exposes /metrics
```

---

## Performance

- Cache
  - Multi-layer cache: local in-process cache for hot items, Redis for distributed caching.
  - Set appropriate TTLs and eviction strategies.

- Async
  - Use `asyncio` and async libraries (`httpx`, `asyncpg`) for IO-bound work to scale concurrency efficiently.
  - Avoid blocking calls in the event loop; use thread executors for CPU-heavy tasks.

- Worker Services
  - Use Celery, RQ, or asynchronous worker loops to offload long-running tasks.
  - Use batching, back-off, and concurrency limits to protect downstream services.

Performance example — delegate work to a background queue in FastAPI:

```python
from fastapi import FastAPI
from tasks import enqueue_task

app = FastAPI()

@app.post('/process')
async def process(payload: dict):
    enqueue_task.delay(payload)
    return {"status": "accepted"}
```

---

## Putting It Together — Minimal FastAPI example

This snippet demonstrates instrumentation, httpx client with retries, a circuit-breaker decorator, and Prometheus metrics.

```python
from fastapi import FastAPI
import httpx
from tenacity import retry, stop_after_attempt, wait_exponential
from aiobreaker import CircuitBreaker
from prometheus_client import Counter, start_http_server

app = FastAPI()
breaker = CircuitBreaker(fail_max=5, reset_timeout=30)
REQUESTS = Counter('requests_total', 'Total requests', ['endpoint'])

@retry(stop=stop_after_attempt(3), wait=wait_exponential(1, 2))
@breaker
async def get_remote(url):
    async with httpx.AsyncClient(timeout=10) as client:
        r = await client.get(url)
        r.raise_for_status()
        return r.json()

@app.get('/proxy')
async def proxy():
    REQUESTS.labels(endpoint='/proxy').inc()
    return await get_remote('https://api.example.com/data')

if __name__ == '__main__':
    start_http_server(8000)
    import uvicorn
    uvicorn.run(app, host='0.0.0.0', port=8080)
```

---

## Tuning & Operational Tips

- Monitor: dashboards for error rate, latency p50/p95/p99, breaker state, and queue depth.
- Alerts: alert on sudden spikes in error rate, increasing latency tail, or long-running retry loops.
- Safety nets: cache critical data and implement graceful degradation pathways.
- Testing: use fault injection (locust, tox, or custom harness) to validate retry, breaker, and fallback behavior.

---

## Recommended Python libraries & tools

- Resilience: `tenacity`, `backoff`, `aiobreaker`, `pybreaker`
- Caching: `cachetools`, `aioredis` / `redis-py`, `dogpile.cache`
- Messaging: Celery (RabbitMQ/Redis), RQ, Kafka (aiokafka), Faust
- Logging: `logging`, `structlog`, `python-json-logger`
- Tracing/Metrics: OpenTelemetry Python, `prometheus_client`, Jaeger/Zipkin exporters

---
