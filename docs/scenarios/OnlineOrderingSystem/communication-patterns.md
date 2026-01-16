## Communication Patterns Shown

- **Synchronous**: Frontend → API Gateway → Order Service
- **Asynchronous**: Order Service → Queue → Payment Service
- **Event-Driven**: Order Service → Event Bus → Downstream Services

---