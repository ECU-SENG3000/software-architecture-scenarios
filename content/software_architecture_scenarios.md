# Software Architecture and Design Scenarios

## 1. Scenario
**Quality Attribute:** Interoperability

**Scenario:** A client system can only send GET requests and cannot include headers. Your API requires a bearer token in the Authorization header.

**Solution:** Accept the token via query string and use custom middleware to extract it and inject it into the Authorization header before authentication middleware runs.

## 2. Scenario
**Quality Attribute:** Performance

**Scenario:** Logging middleware captures request and response bodies, but under load, the app slows down.

**Solution:** Use conditional logging (e.g., only log errors), stream logs asynchronously, or buffer selectively using EnableBuffering().

## 3. Scenario
**Quality Attribute:** Correctness

**Scenario:** Authentication middleware runs before token transformation middleware.

**Solution:** Reorder middleware in Startup.cs so that token transformation runs before authentication.

## 4. Scenario
**Quality Attribute:** Modifiability

**Scenario:** You need to inject a custom header into every response.

**Solution:** Use context.Response.OnStarting() to modify headers before the response is sent.

## 5. Scenario
**Quality Attribute:** Scalability
**Scenario:** Public API needs rate limiting, but premium users should have higher limits.

**Solution:** Use middleware to inspect token claims and apply different rate limits using AspNetCoreRateLimit.

## 6. Scenario
**Quality Attribute:** Interoperability

**Scenario:** Some clients require XML instead of JSON.

**Solution:** Add XML formatters in Startup.cs using AddXmlSerializerFormatters().

## 7. Scenario
**Quality Attribute:** Performance

**Scenario:** Clients upload large files, causing memory pressure.

**Solution:** Use streaming uploads with IFormFile, buffer to disk, and avoid reading entire file into memory.

## 8. Scenario
**Quality Attribute:** Scalability

**Scenario:** API must serve multiple tenants with isolated data.

**Solution:** Use middleware to resolve tenant from subdomain or header, and inject tenant context into services.

## 9. Scenario
**Quality Attribute:** Maintainability

**Scenario:** A breaking change is needed in an endpoint.

**Solution:** Implement API versioning using URL path (/v1/, /v2/) or header-based versioning with Microsoft.AspNetCore.Mvc.Versioning.

## 10. Scenario
**Quality Attribute:** Reliability

**Scenario:** Payment service times out randomly.

**Solution:** Use Polly for retry, fallback, and circuit breaker policies.

## 11. Scenario
**Quality Attribute:** Usability

**Scenario:** UI strings are hardcoded in Razor components.

**Solution:** Use `.resx` files and inject `IStringLocalizer` into components.

## 12. Scenario
**Quality Attribute:** Reusability

**Scenario:** Consumers want to customize behavior of shared components.

**Solution:** Use `RenderFragment`, `EventCallback`, and cascading parameters.

## 13. Scenario
**Quality Attribute:** Availability

**Scenario:** Users want to use the app offline.

**Solution:** Use service workers and IndexedDB for caching data and UI state.

## 14. Scenario
**Quality Attribute:** Responsiveness

**Scenario:** Users want real-time notifications.

**Solution:** Use SignalR with Blazor Server or WebSockets with Blazor WebAssembly.

## 15. Scenario
**Quality Attribute:** Correctness

**Scenario:** Data is loaded in `OnInitialized`, but component doesn’t update.

**Solution:** Use `OnParametersSetAsync` or call `StateHasChanged()` after data updates.

## 16. Scenario
**Quality Attribute:** Modifiability

**Scenario:** Shared components use Bootstrap, but consuming app uses Tailwind.

**Solution:** Use CSS isolation and allow style overrides via parameters or cascading values.

## 17. Scenario
**Quality Attribute:** Reusability

**Scenario:** Components need to work with different UI frameworks.

**Solution:** Abstract styling and behavior; use theming support and conditional rendering.

## 18. Scenario
**Quality Attribute:** Security
**Scenario:** Admin-only features should be hidden from regular users.

**Solution:** Use `AuthorizeView` and inject `AuthenticationStateProvider` to check roles.

## 19. Scenario
**Quality Attribute:** Correctness

**Scenario:** Forms allow invalid data.

**Solution:** Use `EditForm`, `DataAnnotationsValidator`, and custom validation attributes.

## 20. Scenario
**Quality Attribute:** Modifiability

**Scenario:** Parent component needs to react to child component actions.

**Solution:** Use `EventCallback<T>` to notify parent components.

## 21. Scenario
**Quality Attribute:** Correctness

**Scenario:** Two services depend on each other.

**Solution:** Refactor to remove circular dependency or use factory pattern.

## 22. Scenario
**Quality Attribute:** Performance
**Scenario:** Singleton service depends on scoped service.

**Solution:** Correct lifetimes: avoid injecting scoped into singleton; use factory or restructure.

## 23. Scenario
**Quality Attribute:** Modifiability

**Scenario:** Service needs access to app settings.

**Solution:** Use `IOptions<T>` or `IConfiguration` to bind settings.

## 24. Scenario
**Quality Attribute:** Maintainability

**Scenario:** Service needs structured logging.

**Solution:** Use `ILogger<T>` via constructor injection.

## 25. Scenario
**Quality Attribute:** Interoperability

**Scenario:** Service needs user info from request.

**Solution:** Inject `IHttpContextAccessor`.

## 26. Scenario
**Quality Attribute:** Modifiability

**Scenario:** Replace built-in service with custom logic.

**Solution:** Register custom implementation in DI container.

## 27. Scenario
**Quality Attribute:** Testability

**Scenario:** Unit tests fail due to real dependencies.

**Solution:** Use interfaces and inject mocks via DI.

## 28. Scenario
**Quality Attribute:** Modifiability

**Scenario:** Different strategies for same interface.

**Solution:** Use named services or resolve via `IEnumerable<IService>`.

## 29. Scenario
**Quality Attribute:** Correctness

**Scenario:** Service needs request-specific data.

**Solution:** Use scoped service or factory delegate.

## 30. Scenario
**Quality Attribute:** Modifiability
**Scenario:** Middleware needs DI-injected services.

**Solution:** Use `UseMiddleware<T>()` with constructor injection.

## 31. Scenario
**Quality Attribute:** Maintainability

**Scenario:** Changes in shared models break consumers.

**Solution:** Use DTOs and semantic versioning.

## 32. Scenario
**Quality Attribute:** Correctness

**Scenario:** Multiple methods with same name.

**Solution:** Use explicit namespaces or rename methods.

## 33. Scenario
**Quality Attribute:** Modifiability

**Scenario:** Hard to maintain startup logic.

**Solution:** Use modular startup classes and extension methods.

## 34. Scenario
**Quality Attribute:** Reusability

**Scenario:** UI components reused across apps.

**Solution:** Use Razor Class Library (RCL).

## 35. Scenario
**Quality Attribute:** Maintainability

**Scenario:** Duplicate validation logic.

**Solution:** Create shared validation library.

## 36. Scenario
**Quality Attribute:** Modifiability

**Scenario:** Inconsistent values across services.

**Solution:** Use shared static class or library.

## 37. Scenario
**Quality Attribute:** Modifiability

**Scenario:** Multiple apps need same settings.

**Solution:** Use shared JSON config and bind via `IOptions`.

## 38. Scenario
**Quality Attribute:** Maintainability
**Scenario:** Inconsistent logging.

**Solution:** Create logging abstraction and inject via DI.

## 39. Scenario
**Quality Attribute:** Reusability

**Scenario:** Common middleware logic.

**Solution:** Package middleware in shared library.

## 40. Scenario
**Quality Attribute:** Interoperability

**Scenario:** Services use different contract versions.

**Solution:** Use shared contract library with versioning.

## 41. Scenario
**Quality Attribute:** Testability

**Scenario:** Middleware logic is untested.

**Solution:** Use test server and mock `HttpContext`.

## 42. Scenario
**Quality Attribute:** Testability

**Scenario:** UI logic is untested.

**Solution:** Use bUnit for Blazor component testing.

## 43. Scenario
**Quality Attribute:** Security

**Scenario:** API must validate tokens.

**Solution:** Use `AddAuthentication().AddJwtBearer()`.

## 44. Scenario
**Quality Attribute:** Security

**Scenario:** Unauthorized users access admin features.

**Solution:** Use `AuthorizeView` and role checks.

## 45. Scenario
**Quality Attribute:** Deployability

**Scenario:** Host app in cloud.

**Solution:** Use Azure App Service or Static Web Apps.

## 46. Scenario
**Quality Attribute:** Deployability

**Scenario:** Manual deployment is error-prone.

**Solution:** Use GitHub Actions or Azure DevOps pipelines.

## 47. Scenario
**Quality Attribute:** Reliability

**Scenario:** No visibility into service status.

**Solution:** Use health checks and expose `/health` endpoint.

## 48. Scenario
**Quality Attribute:** Maintainability

**Scenario:** Errors are not logged.

**Solution:** Use `UseExceptionHandler()` and centralized logging.

## 49. Scenario
**Quality Attribute:** Security

**Scenario:** Secrets stored in plain text.

**Solution:** Use Azure Key Vault or user secrets.

## 50. Scenario
**Quality Attribute:** Modifiability

**Scenario:** Enable/disable features dynamically.

**Solution:** Use `IFeatureManager` from `Microsoft.FeatureManagement`.