# ZeroMQ Communication Layer

> **Figure for**: Section III-B (Communication Architecture)  
> **Suggested caption**: "Dual-socket ZeroMQ communication patterns"  
> **LaTeX label**: `fig:zmq_patterns`

```mermaid
flowchart LR
    subgraph PUBSUB["PUB/SUB Pattern — Port 5555"]
        direction LR
        PUB["MQL5 Publisher<br/>(one-to-many)"]
        SUB1["Rust Subscriber"]
        SUB2["Other Subscribers<br/>(Go, Java, C++)"]
        PUB -->|"Tick JSON<br/>non-blocking"| SUB1
        PUB -->|"Broadcast"| SUB2
    end

    subgraph REQREP["REQ/REP Pattern — Port 5556"]
        direction LR
        REQ["Rust Requester"]
        REP["MQL5 Responder"]
        REQ -->|"Order JSON"| REP
        REP -->|"Result JSON"| REQ
    end

    style PUBSUB fill:#e8f5e9,stroke:#2e7d32,stroke-width:2px
    style REQREP fill:#fff3e0,stroke:#ef6c00,stroke-width:2px
```
