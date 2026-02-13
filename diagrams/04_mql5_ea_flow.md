# MQL5 Expert Advisor Internal Flow

> **Figure for**: Section IV-A (MQL5 Implementation Details)  
> **Suggested caption**: "Expert Advisor OnTick() processing flow"  
> **LaTeX label**: `fig:ea_flow`

```mermaid
flowchart TD
    TICK(["OnTick() Event"]) --> CHK_REQ{"Check REP Socket<br/>(non-blocking)"}

    CHK_REQ -->|"Request found"| RECV["Receive JSON Request"]
    RECV --> PROCESS["ProcessOrderRequest()"]
    PROCESS --> SEND_RESP["Send JSON Response<br/>(blocking)"]
    SEND_RESP --> CHK_PUB

    CHK_REQ -->|"No request"| CHK_PUB{"Publisher valid?"}
    CHK_PUB -->|"NULL"| END(["Return"])
    CHK_PUB -->|"Valid"| GATHER["Gather Data"]

    subgraph GATHER["Data Gathering"]
        direction TB
        G1["SymbolInfoTick()"]
        G2["AccountInfoDouble()<br/>balance, equity, margin"]
        G3["SymbolInfoDouble()<br/>min/max lot, lot step"]
        G4["Loop: PositionsTotal()<br/>Build positions JSON"]
        G5["Loop: OrdersTotal()<br/>Build orders JSON"]
        G1 --> G2 --> G3 --> G4 --> G5
    end

    GATHER --> BUILD["StringConcatenate()<br/>Build complete JSON"]
    BUILD --> PUBLISH["PUB Socket Send<br/>(non-blocking)"]
    PUBLISH --> END

    style TICK fill:#e6f3ff,stroke:#0066cc
    style PUBLISH fill:#ccffcc,stroke:#00cc00
```
