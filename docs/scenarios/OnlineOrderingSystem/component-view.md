---

## 📊 Component Summary

| Component | Logical Role | Physical Deployment |
|-----------|--------------|---------------------|
| Frontend Web App | User interface | Container in K8s, served via CDN |
| API Gateway | Request routing, auth, rate limiting | Azure API Management / AWS API Gateway |
| Order Service | Order orchestration, business logic | Container pod (autoscaled) |
| Payment Service | Payment processing | Container pod (autoscaled, 3+ replicas) |
| Message Queue | Async decoupling | Azure Service Bus / SQS / RabbitMQ |
| Database | Persistent storage | Managed SQL (primary + read replicas) |
| Event Bus | Event distribution | Event Grid / EventBridge / Kafka |
| Notification Service | Email/SMS dispatch | Container pod |
| Inventory Service | Stock management | Container pod |
| Analytics Service | Metrics & reporting | Container pod |
| External Payment Provider | 3rd party charges | Stripe / PayPal / Adyen |

---

