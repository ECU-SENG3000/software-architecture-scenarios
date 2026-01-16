# Polly — .NET Resilience Library (Quick Reference)

Polly is a .NET library that provides a set of resilience and transient-fault-handling patterns as reusable policies. It helps you make HTTP calls, database requests, and other remote interactions more robust by declaratively applying retry, timeout, circuit-breaker, fallback, bulkhead, and cache behaviors.

---

## Key Concepts
- Policy: a reusable resilience strategy (Retry, CircuitBreaker, Timeout, Fallback, Bulkhead, Cache).
- PolicyWrap: combine multiple policies into a single behavior (order matters).
- Handlers / delegates: hooks such as `onRetry`, `onBreak`, `onReset`, and `onHalfOpen` for logging/metrics.
- Async + sync support: use `Execute` / `ExecuteAsync` variants.

---

## Common Policies & When to Use Them
- Retry: for transient failures (network blips, throttling). Use bounded retries with backoff + jitter.
- Timeout: limit how long a single attempt may take to avoid thread exhaustion.
- Circuit Breaker: stop calling a failing dependency so your system fails fast and recovers gracefully.
- Fallback: provide cached/default responses or alternate paths when primary call fails.
- Bulkhead: limit concurrent access to a resource to avoid overload.
- Cache: transparently cache results for a policy-protected call.

---

## Examples

Retry (simple):

```csharp
var retryPolicy = Policy
    .Handle<HttpRequestException>()
    .OrResult<HttpResponseMessage>(r => (int)r.StatusCode >= 500)
    .RetryAsync(3); // 3 retries

// Usage
await retryPolicy.ExecuteAsync(() => httpClient.GetAsync(url));
```

Retry with exponential backoff and jitter (recommended):

```csharp
var jitterer = new Random();
var retryWithBackoff = Policy
    .Handle<HttpRequestException>()
    .OrResult<HttpResponseMessage>(r => (int)r.StatusCode >= 500)
    .WaitAndRetryAsync(
        retryCount: 3,
        sleepDurationProvider: attempt =>
        {
            // exponential backoff + random jitter
            var backoff = TimeSpan.FromSeconds(Math.Pow(2, attempt));
            var jitter = TimeSpan.FromMilliseconds(jitterer.Next(0, 100));
            return backoff + jitter;
        },
        onRetry: (outcome, timespan, context) => {
            // log or increment retry metric
        });
```

Timeout + Retry (wraps):

```csharp
var timeout = Policy.TimeoutAsync<HttpResponseMessage>(10); // 10s per attempt
var retry = Policy.Handle<Exception>().RetryAsync(2);

// Wrap: timeout outside, retry inside (order chosen to match semantics)
var combined = Policy.WrapAsync(timeout, retry);
```

Circuit Breaker with events:

```csharp
var breaker = Policy
    .Handle<HttpRequestException>()
    .OrResult<HttpResponseMessage>(r => (int)r.StatusCode >= 500)
    .CircuitBreakerAsync(
        exceptionsAllowedBeforeBreaking: 5,
        durationOfBreak: TimeSpan.FromSeconds(30),
        onBreak: (outcome, breakDelay) => {
            // emit metric / log
        },
        onReset: () => {
            // emit metric / log
        },
        onHalfOpen: () => {
            // attempt probe logged
        });
```

Fallback example (cache fallback):

```csharp
var fallback = Policy<HttpResponseMessage>
    .Handle<Exception>()
    .FallbackAsync(async cancellationToken => {
        // return a cached HttpResponseMessage or a synthetic response
        return cachedResponse ?? new HttpResponseMessage(HttpStatusCode.ServiceUnavailable);
    });

// Use Policy.WrapAsync(fallback, breaker, retry) to combine behaviors
```

---

## Integration Patterns
- HttpClientFactory: register policies per-named `HttpClient` using `AddPolicyHandler`.

```csharp
services.AddHttpClient("api")
    .AddPolicyHandler(retryWithBackoff)
    .AddPolicyHandler(breaker);
```

- DI / central config: register policies in startup (Program.cs) or a Resilience module so thresholds are centrally controlled and configurable by environment.
- Telemetry: use `onRetry` / `onBreak` handlers to emit metrics and logs (use OpenTelemetry/ILogger/Metric instruments).

---

## Best Practices
- Keep retry counts small for user-facing requests; prefer background retries for long-running tasks.
- Always add jitter to backoff to avoid synchronized retries.
- Choose the policy order consciously when wrapping (timeout around retry vs retry around timeout).
- Emit metrics on retry, fallback, and circuit events; alert on sustained breaker openings.
- Persist circuit state only if you have multi-instance coordination requirements (Polly's in-memory breaker is process-local).
- Test policies with fault injection and synthetic tests.

---

## Useful Links
- Official docs: https://www.pollydocs.org
- Polly GitHub: https://github.com/App-vNext/Polly

---

