# Order Processing Pipeline Diagrams


---

## 🧠 Logical Architecture Diagram

The **logical view** shows the conceptual components and their relationships, independent of physical deployment. It focuses on *what* the system does.

```mermaid
flowchart TB
    subgraph Presentation["🖥️ Presentation Layer"]
        FE[Frontend Web App]
    end

    subgraph Gateway["🚪 Gateway Layer"]
        GW[API Gateway]
    end
    

    subgraph Core["⚙️ Core Business Services"]
        OS[Order Service]
        PS[Payment Service]
    end

    subgraph Messaging["📬 Messaging Layer"]
        Q[(Message Queue)]
        EB[Event Bus]
    end

    subgraph Data["💾 Data Layer"]
        DB[(Database)]
    end

    subgraph Downstream["📡 Downstream Services"]
        NS[Notification Service]
        IS[Inventory Service]
        AS[Analytics Service]
    end

    subgraph External["🌐 External Systems"]
        PP[Payment Provider]
        Email[Email/SMS Gateway]
    end

    FE --> GW
    GW --> OS
    OS --> DB
    OS --> Q
    Q --> PS
    PS --> PP
    PS --> OS
    OS --> EB
    EB --> NS
    EB --> IS
    EB --> AS
    NS --> Email
    IS --> DB
    AS --> DB
```

---