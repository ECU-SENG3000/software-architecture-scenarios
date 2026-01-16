| Architecture | Key Concept | Strengths | Weaknesses | Example Use Case |
|--------------|-------------|-----------|------------|------------------|
| Monolithic | Single unified codebase | Simple deployment | Hard to scale and maintain | Legacy enterprise apps |
| Layered | Organized in logical layers | Separation of concerns | Can be rigid and slow | Web applications |
| Client-Server | Clients request services from servers | Centralized control | Server bottlenecks | Web browsing |
| Event-Driven | Components react to events | Loose coupling, scalable | Complex debugging | GUI apps, IoT systems |
| Microservices | Independent services communicate via APIs | Scalable, resilient | Complex deployment | Netflix, Amazon |
| Peer-to-Peer | Nodes act as both client and server | Decentralized, scalable | Security and consistency issues | File sharing (BitTorrent) |
| Service-Oriented (SOA) | Services communicate over a network | Platform-independent | Overhead from protocols | Enterprise systems |
| Microkernel | Core system with plug-in modules | Extensible, maintainable | Performance overhead | VS Code IDE |
| Broker | Middleware coordinates communication | Decouples components | Adds complexity | Message queues (RabbitMQ) |
| Proxy | Intermediary for requests | Security, caching | Can become bottleneck | Reverse proxy (NGINX) |
| Blackboard | Shared data structure for problem solving | Collaborative problem solving | Hard to design and debug | AI systems |
| Interpreter | Executes code line-by-line | Flexibility, dynamic execution | Slower than compiled code | Python interpreter |
| Space-Based | Distributed memory and processing | High scalability | Complex data consistency | Real-time analytics |
| Repository | Central data store accessed by components | Centralized data management | Tight coupling | Compiler architecture |
| Model-View-Controller (MVC) | Separates model, view, controller | Clear separation of concerns | Can be overkill for small apps | ASP.NET MVC, Django |
| Hexagonal | Core logic isolated via ports/adapters | Testable, flexible integrations | Learning curve | Domain-centric apps |
| Naked Objects | UI generated from domain objects | Rapid development | Limited customization | CRM systems |
| Lambda | Batch + real-time processing | Handles big data efficiently | Complex architecture | Hadoop + Spark |
| Domain-Driven Design | Focus on domain modeling | Rich domain logic | Requires deep domain knowledge | Insurance, banking systems |
| CQRS | Separate read/write models | Optimized performance | Increased complexity | Shopping cart systems |
| Event Sourcing | Store state as events | Auditability, replayability | Complex querying | Financial systems |