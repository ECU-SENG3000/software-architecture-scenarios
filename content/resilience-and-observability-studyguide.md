# Resilience, Observability, and Performance — .NET (Aspire) Study Guide

This guide focuses on practical patterns and examples for building resilient, observable, and high-performance applications on the .NET Aspire stack. It assumes you use Aspire's code-first AppHost approach and standard .NET DI and middleware.

---

**Quick orientation (Aspire):** use `CreateBuilder()` / `AddProject()` to register services, `WithReference()` and `WaitFor()` to express dependencies and start order, and `ConfigureServices` to register resilience/observability services (HttpClient, Polly policies, OpenTelemetry, logging, metrics, etc.).

Polly is a .Net package that helps handle transient faults and improve the resilience of your application.

https://www.pollydocs.org/index.html

---

## Resilience

Resilience means keeping the system responsive and avoiding cascading failures. Key patterns: retries (with timeouts), circuit breaker, cache/fallback, and queue/messaging (to decouple workloads).

- Retry (with timeout)
  - Purpose: survive transient failures (network hiccups, brief service unavailability).
  - Best practice: limit retries, use exponential backoff + jitter, combine with timeouts to avoid long blocking.
  - .NET example (Polly + HttpClientFactory):

```csharp
// In AppHost builder or ConfigureServices
// Register an HttpClient named "ThirdParty" with a handler lifetime so
// DNS and connection pooling are managed efficiently.
services.AddHttpClient("ThirdParty")
  .SetHandlerLifetime(TimeSpan.FromMinutes(5))
  // Add a simple retry policy that handles network exceptions or 5xx responses.
  // RetryAsync(3) will attempt the call up to 3 additional times on failure.
  .AddPolicyHandler(Policy<HttpResponseMessage>
    .Handle<HttpRequestException>()
    .OrResult(r => (int)r.StatusCode >= 500)
    .RetryAsync(3));

// Example: build a retry policy with exponential backoff. Prefer adding jitter
// in production to avoid thundering herd effects (not shown here).
var retry = Policy.Handle<Exception>()
  .WaitAndRetryAsync(new[] {
    TimeSpan.FromSeconds(1),
    TimeSpan.FromSeconds(2),
    TimeSpan.FromSeconds(4)
  });

// Per-request timeout policy (10 seconds). This prevents a single request
// from blocking retries or consuming threads for too long.
var timeout = Policy.TimeoutAsync<HttpResponseMessage>(10);

// Register another HttpClient that composes timeout + retry policies.
// Policy.WrapAsync(timeout, retry) runs the timeout around the retry behavior
// (order matters — choose wrapping order according to desired semantics).
services.AddHttpClient("Reliable")
  .AddPolicyHandler(Policy.WrapAsync(timeout, retry));
```

- Circuit Breaker
  - Purpose: stop hammering a service that's persistently failing; fail fast and allow system recovery.
  - Combine with retries: small number of immediate retries, then count failures toward circuit opening.
  - Polly circuit-breaker example:

```csharp
// Create a circuit-breaker policy:
// - Handle<HttpRequestException>() => treat network errors as failures
// - OrResult(...) => treat 5xx HTTP responses as failures as well
// The circuit trips (opens) after the configured number of failures and
// stays open for the specified duration, during which calls fail fast.
var breaker = Policy
  .Handle<HttpRequestException>()
  .OrResult<HttpResponseMessage>(r => (int)r.StatusCode >= 500)
  .CircuitBreakerAsync(
    // Number of consecutive failures (or handled results) before opening
    exceptionsAllowedBeforeBreaking: 5,
    // How long the circuit remains open before transitioning to Half-Open
    durationOfBreak: TimeSpan.FromSeconds(30)
  );

// Register an HttpClient that uses the circuit-breaker policy. When the
// circuit is open, calls will fail fast and the policy can trigger
// fallback/cache logic elsewhere. Combine with a small retry policy inside
// the Closed state (and backoff/jitter) if desired; avoid unbounded retries.
services.AddHttpClient("WithBreaker")
  .AddPolicyHandler(breaker);

// Notes / tuning:
// - exceptionsAllowedBeforeBreaking: lower values open faster (more sensitive),
//   higher values reduce false positives but delay fail-fast protection.
// - durationOfBreak: short durations cause frequent probe attempts; longer
//   durations give the downstream time to recover but increase degraded period.
// - Consider capturing circuit state events (onBreak/onReset/onHalfOpen) to
//   emit metrics/alerts and add visibility to dashboards.
```

  - Notes: tune `exceptionsAllowedBeforeBreaking` and `durationOfBreak` to traffic patterns. Use health checks to monitor.

- Cache / Fallback
  - Purpose: return cached or default responses instead of failing the whole flow.
  - Use `IMemoryCache` for in-process cache and `IDistributedCache` (Redis, etc.) for multi-instance caching.
  - Example: fallback-to-cache using Polly FallbackPolicy:

```csharp
// Register caches
services.AddMemoryCache();

// Fallback policy that returns cached value on error
var fallback = Policy<string>
    .Handle<Exception>()
    .FallbackAsync(async ct => {
        // read from cache
        var cache = provider.GetRequiredService<IMemoryCache>();
        if (cache.TryGetValue("key", out string cached)) return cached;
        return "default";
    });

// Usage: wrap your call with fallback and update cache on success
```

- Queue / Messaging
  - Purpose: decouple producers and consumers, absorb spikes, and enable durable processing.
  - Options: Azure Service Bus, RabbitMQ, Kafka, or lightweight durable queues. MassTransit integrates well with .NET.
  - Aspire note: register a worker service (background) with `AddProject()` and `WaitFor()` to start consumers after dependencies are ready.

Example consumer (BackgroundService):

```csharp
// QueueWorker.cs

public class QueueWorker : BackgroundService {
    protected override async Task ExecuteAsync(CancellationToken stoppingToken) {
        await foreach(var msg in MessageReceiver.ReceiveAllAsync(stoppingToken)) {
            // process, use retry/circuit-breaker as needed
        }
    }
}
```

```csharp
// Program.cs

services.AddHostedService<QueueWorker>();
```

---

## Observability

Observability includes logs, traces, and metrics — the three pillars. Use standard .NET libraries and OpenTelemetry to collect and export data.

- Logs
  - Use `ILogger<T>` everywhere; prefer structured logging (message templates, properties).
  - Serilog is a widely-used provider for structured logs and sinks (file, seq, elasticsearch).
  - Example:

```csharp
// Configure Serilog as the application's logging provider
// - Enrich.FromLogContext() ensures properties added to the LogContext are included
//   in structured output (useful for request IDs, user ids, etc.)
// - WriteTo.Console() sends logs to the console (good for local dev and container stdout)
// - WriteTo.File(...) persists rolling logs to disk; adjust path/retention for your environment
//
// Application -> Serilog Logger -> Enrichers -> Filters -> Sink A (Console)
//             -> Sink B (File)
//             -> Sink C (Seq/OTLP)
Log.Logger = new LoggerConfiguration()
  .Enrich.FromLogContext()
  .WriteTo.Console()
  .WriteTo.File("logs/app-.txt", rollingInterval: RollingInterval.Day)
  .CreateLogger();

// Integrate Serilog with the generic host so Microsoft.Extensions.Logging uses it
// This ensures framework logs (hosting, DI, etc.) flow through Serilog's sinks
builder.Host.UseSerilog();
```

- Traces
  - Use OpenTelemetry (.NET) to instrument HTTP client/server, database calls, and background workers.
  - Export to OTLP/Jaeger/Zipkin.
  - Example:

```csharp
services.AddOpenTelemetryTracing(builder =>
{
  // Instrument incoming HTTP requests handled by ASP.NET Core
  // Produces server spans for each request
  builder.AddAspNetCoreInstrumentation();

  // Instrument outgoing HTTP calls made with HttpClient
  // Produces client spans for external/service calls
  builder.AddHttpClientInstrumentation();

  // Instrument SQL client operations (e.g., System.Data.SqlClient)
  // Produces database spans for queries and commands
  builder.AddSqlClientInstrumentation();

  // Set resource information such as the service name that appears in traces
  builder.SetResourceBuilder(ResourceBuilder.CreateDefault().AddService("MyAspireService"));

  // Configure exporter(s) — OTLP exporter sends traces to an OTLP-compatible collector
  // Configure endpoint via environment variables (e.g., OTEL_EXPORTER_OTLP_ENDPOINT)
  builder.AddOtlpExporter();
});
```

- Metrics
  - Use `System.Diagnostics.Metrics` + OpenTelemetry Metrics or prometheus-net.
  - Capture request durations, error rates, queue lengths, and custom business metrics (orders/sec).
  - Example (basic):

```csharp
// Create a Meter to produce metrics for your application. Typically this is
// done once during startup and reused via DI or a shared static instance.
var meter = new Meter("MyApp", "1.0");

// Create instruments for the metrics you care about. 
// Counter: for monotonic counts
// Histogram: for distributions, percentiles, buckets, or durations
// ObservableCounter: triggered sampling an existing state
// ObservableGauge: triggered sampling - memory usage, thread pool size, etc
// etc...
var requestCounter = meter.CreateCounter<long>("requests");

// Increment the counter where the event actually happens. For HTTP request
// counts, do this in middleware (so every request is counted reliably).
// For business events (orders created, tasks processed), increment in the
// controller/handler or after successful processing in the worker.
// Example: increment with a tag for the endpoint (use labels/tags to group).
requestCounter.Add(1, new KeyValuePair<string, object?>("endpoint", "/api/pay"));

// Where to implement this logic:
// - HTTP middleware: measure request counts and latency (use Histogram for latency)
//   and attach route/handler labels. This centralizes HTTP metrics.
// - Controllers / service layer: emit business-level metrics where domain events occur.
// - Background workers: record processed item counts, failures, and queue depth.
// - Circuit-breaker event handlers: increment metrics or emit gauges when breaker
//   state changes (onBreak/onReset/onHalfOpen) to track resilience state.
// - Startup: register exporters (OpenTelemetry/Prometheus) and attach MeterProvider
//   so metrics are exported to your backend.

// Example placement (outline):
// - Program.cs / Startup: create Meter, configure exporters, add Telemetry services.
// - Middleware: capture request start/stop and call requests.Add / histo.Record.
// - Worker / Handlers: call requests.Add when processing items.

```

---

## Performance

Focus on latency, throughput, and efficient resource use.

- Cache
  - Use caching to reduce repeated expensive operations. Apply appropriate expiration and cache invalidation rules.
  - Consider multi-layer cache: in-memory for hot data; Redis for distributed cache.

- Async
  - Use asynchronous APIs (`async`/`await`) for IO-bound work to improve scalability.
  - Avoid blocking calls (`.Result` / `.Wait()`), and configure `HttpClient` lifetimes properly.

- Worker Services
  - Offload long-running or retryable tasks to `BackgroundService` or queue consumers (hosted services).
  - Use batching and back-off to reduce load on downstream systems.

Example: ASP.NET route delegating work to queue instead of blocking request:

```csharp
[HttpPost("/process")]
public IActionResult Enqueue([FromBody] Payload p) {
    queueClient.Enqueue(p);
    return Accepted(); // fast response
}
```

---

## Putting It Together (Example Aspire AppHost snippet)

```csharp
var builder = DistributedApplication.CreateBuilder(args);

builder.AddProject("PaymentService");
builder.AddProject("CreditApi");
builder.WithReference("PaymentService", "CreditApi");
builder.WaitFor("CreditApi");

// Register services directly on the builder's IServiceCollection
builder.Services.AddLogging();

// HttpClient + Polly
builder.Services.AddHttpClient("CreditClient")
    .AddPolicyHandler(...); // retry + breaker

// Cache
builder.Services.AddStackExchangeRedisCache(options => { ... });

// Observability
builder.Services.AddOpenTelemetryTracing(...);

// Worker
builder.Services.AddHostedService<QueueWorker>();

var host = builder.Build();
host.Run();
```

---

## Tuning & Operational Tips

- Monitor: use dashboards for error rate, latency percentiles, queue depth, and breaker state.
- Alerts: alert on sustained high error rate, long tail latencies (p95/p99), and growing queue length.
- Safety nets: use graceful degradation (caching, feature flags, reduced capacity modes).
- Testing: use chaos experiments (fault injection) to validate breaker and fallback behavior.

---

## Cheat-sheet (Recommended libraries & tools)

- Resilience: Polly (retries, timeouts, circuit-breaker, fallback)
- Caching: IMemoryCache, IDistributedCache (Redis/StackExchange.Redis)
- Messaging: Azure Queue Storage, Azure Service Bus, RabbitMQ, Kafka, MassTransit
- Logging: Microsoft.Extensions.Logging, Serilog
- Tracing/Metrics: OpenTelemetry (.NET), Prometheus exporters

---

## Runbook

### Definition

- **Runbook**: A concise, actionable document that describes the steps operators should follow to detect, diagnose, mitigate, and recover from a specific operational event or routine task.
### When to Use

- **Incidents**: Failures like service outages, high error rates, or - circuit-breaker openings.
- **Routine Ops**: Deploys, scaling, backups, and maintenance tasks.
- **On-call Handoffs**: Provide a repeatable playbook for responders.
### Typical Contents

- **Title**: One-line incident/task name.
- **Scope / Impact**: Which services/regions/owners are affected.
- **Detection / Alerts**: What monitors/alerts indicate the problem - (metrics, thresholds, alert names).
- **Immediate Steps**: Step-by-step actions to stop damage or restore - service.
- **Diagnostics**: Where to check logs, traces, dashboards, and - specific queries or commands.
- **Mitigation / Workarounds**: Temporary actions (throttle, reroute, - enable fallback).
- **Recovery / Validation**: How to confirm the issue is resolved and - what checks to run.
- **Escalation & Contacts**: Who to call, paging order, Slack/Teams - channels, vendor contacts.
- **Post-incident Tasks**: RCA owners, follow-up tickets, threshold/- tuning changes.
- **Last Updated / Owner**: Who maintains the runbook and when it was last changed.
### Quick Runbook Template

- **Title**:
- **Owner**:
- **Detection**: (alert name, metric + threshold)
- **Immediate actions**:
  1. Acknowledge alert.
  1. Run diagnostic command(s) or open dashboard.
  1. Apply mitigation (exact command/config).
  1. Monitor recovery for X minutes.
- **Diagnostics**: (log queries, trace IDs, dashboards)
- **Escalation**: (names, contact info)
- **Recovery verification**: (what to check)
- **Postmortem tasks**: (ticket, RCA date)
### Example — Circuit Breaker Opens (short runbook entry)

- **Title**: Circuit Breaker: CreditApi.Breaker opened
- **Owner**: Payments team on-call
- **Detection**: Alert breaker_open_creditapi firing when open count > 1
- **Immediate actions**:
  1. Acknowledge alert and post to #oncall-payments.
  1. Check dashboard: error rate, failure count, p95 latency, queue depth.
  1. Pull recent traces for failing requests (OpenTelemetry/Jaeger).
  1. Ensure fallback/cache is active and returning correct responses.
  1. If downstream (third-party) is failing, notify vendor and enable degraded mode.
  1. Do NOT force-close breaker unless you verify recovery — if forced, monitor for - 15 minutes and document reason.
- **Diagnostics**: grep log pattern, trace IDs, GET /health on downstream, Redis - cache hit rate.
- **Escalation**: Contact alice@company → bob@company → vendor support.
- **Recovery verification**: 5 minutes of successful trial requests (p95 < - threshold) and no increase in failures after Half-Open.
- **Postmortem**: Create RCA ticket, consider lowering retry rate or increasing - backoff and add synthetic test.
### Best Practices

- Keep it short and actionable: Operators should be able to follow it under - stress.
- Make it discoverable: Link runbooks from alert details and runbook repository - (wiki).
- Automate safe steps: Provide scripts/Playbooks for diagnostics and mitigations - where possible. Always include manual fallback steps.
- Version & ownership: Track who updated the runbook and when. Review - periodically (quarterly).
- Instrument the runbook: Add checks that the runbook itself is correct (synthetic tests, playground runs).


## Checklist — When a Circuit Breaker Opens

Use this concise checklist for first-response and mitigation when a circuit breaker trips. Follow your runbook for escalation steps and document actions.

- **Acknowledge the alert**: record timestamp, affected service, circuit-breaker name/state, and any alert IDs.
- **Check dashboards & metrics**: error rate, failure count, p50/p95/p99 latency, throughput, and queue depth for related services.
- **Inspect logs & traces**: look for correlated trace IDs, stack traces, and recent errors via OpenTelemetry/Jaeger/ELK.
- **Determine scope**: single instance, service-wide, region, or external dependency; check recent deploys or config changes.
- **Activate fallbacks**: verify cache/fallback responses are functioning and are instrumented (logs/metrics).
- **Apply traffic controls**: throttle/reroute traffic, scale resources if safe, or enable degraded feature modes.
- **Avoid forcing the breaker closed**: only close manually after verification and monitoring; document why.
- **Notify owners**: contact downstream teams or external providers with logs and traces if the failure is not internal.
- **Monitor recovery (Half-Open)**: watch trial requests and ensure success before restoring normal traffic.
- **Post-incident**: perform RCA, tune thresholds/backoffs, add synthetic checks, and update the runbook.

