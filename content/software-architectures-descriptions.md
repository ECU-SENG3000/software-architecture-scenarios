# Software Architecture Styles

## 1. Monolithic
**Explanation**: A single-tiered software application where all components are interconnected and interdependent.  
**Example**: A traditional e-commerce app where the UI, business logic, and database access are all part of one codebase.

## 2. Layered
**Explanation**: Organizes software into layers (e.g., presentation, business logic, data access), each with specific responsibilities.  
**Example**: A web app with a frontend (UI), a middle layer for processing, and a backend for database operations.

## 3. Client-Server
**Explanation**: Divides the system into clients that request services and servers that provide them.  
**Example**: A web browser (client) requesting pages from a web server.

## 4. Event-Driven
**Explanation**: Components communicate by emitting and responding to events.  
**Example**: A GUI application where button clicks trigger specific actions.

## 5. Microservices
**Explanation**: Breaks an application into small, independently deployable services.  
**Example**: An online store with separate services for inventory, payment, and user management.

## 6. Peer-to-Peer (P2P)
**Explanation**: Each node acts as both client and server, sharing resources directly.  
**Example**: BitTorrent file sharing.

## 7. Service-Oriented Architecture (SOA)
**Explanation**: Uses services to support software components across different platforms.  
**Example**: A banking system where account services and transaction services communicate via SOAP.

## 8. Microkernel
**Explanation**: Core system provides minimal functionality; additional features are plug-ins.  
**Example**: Eclipse IDE with core editing and plug-ins for Java, Python, etc.

## 9. Broker
**Explanation**: Middleware coordinates communication between components.  
**Example**: CORBA or a message broker like RabbitMQ.

## 10. Proxy
**Explanation**: Acts as an intermediary for requests from clients to servers.  
**Example**: A reverse proxy like NGINX handling incoming web traffic.

## 11. Blackboard
**Explanation**: Multiple subsystems work cooperatively on a shared data structure (blackboard).  
**Example**: AI systems for speech recognition where different modules contribute to a solution.

## 12. Interpreter
**Explanation**: Executes instructions written in a programming or scripting language.  
**Example**: Python interpreter running Python scripts.

## 13. Space-Based
**Explanation**: Uses distributed memory (space) to handle scalability and concurrency.  
**Example**: GigaSpaces for real-time analytics.

## 14. Repository
**Explanation**: Central data store accessed by various components.  
**Example**: Compiler architecture where different phases (lexical, syntax, semantic) access a shared symbol table.

## 15. Model-View-Controller (MVC)
**Explanation**: Separates application into Model (data), View (UI), and Controller (logic).  
**Example**: ASP.NET MVC web applications.

## 16. Hexagonal (Ports and Adapters)
**Explanation**: Core logic is isolated from external systems via ports and adapters.  
**Example**: A banking app where the core logic can be accessed via REST, CLI, or tests.

## 17. Naked Objects
**Explanation**: UI is automatically generated from domain objects.  
**Example**: A CRM system where each customer object directly maps to a UI form.

## 18. Lambda Architecture
**Explanation**: Combines batch and real-time processing for big data.  
**Example**: A system using Hadoop for batch and Spark Streaming for real-time analytics.

## 19. Domain-Driven Design (DDD)
**Explanation**: Focuses on modeling software based on complex business domains.  
**Example**: An insurance system with rich domain models like Policy, Claim, and Coverage.

## 20. Command Query Responsibility Segregation (CQRS)
**Explanation**: Separates read and write operations into different models.  
**Example**: A shopping cart system with one model for updating cart items and another for querying cart state.

## 21. Event Sourcing
**Explanation**: Stores state changes as a sequence of events.  
**Example**: A banking app where account balance is derived from a log of deposit and withdrawal events.