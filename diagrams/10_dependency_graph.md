# Dependency Graph

> **Figure for**: Section IV (Implementation — Dependencies)  
> **Suggested caption**: "File-level dependency graph for MQL5 and Rust components"  
> **LaTeX label**: `fig:dependency_graph`

```mermaid
flowchart TB
    subgraph MQL5["MQL5 Component"]
        EA["ZmqPublisher.mq5<br/>(451 lines)"]
        ZMQ_H["Zmq.mqh<br/>(145 lines)"]
        TRADE_H["Trade.mqh<br/>(MT5 built-in)"]
        LIBZMQ["libzmq.dll"]
        LIBSOD["libsodium.dll"]

        EA --> ZMQ_H
        EA --> TRADE_H
        ZMQ_H -->|"#import"| LIBZMQ
        LIBZMQ --> LIBSOD
    end

    subgraph RUST["Rust Component"]
        MAIN["main.rs<br/>(853 lines)"]
        EF["eframe 0.27"]
        EG["egui 0.27"]
        EP["egui_plot 0.27"]
        SE["serde 1.0"]
        SJ["serde_json 1.0"]
        TK["tokio 1.x"]
        ZR["zeromq 0.3"]
        CH["chrono 0.4"]

        MAIN --> EF
        MAIN --> EP
        MAIN --> SE
        MAIN --> SJ
        MAIN --> TK
        MAIN --> ZR
        MAIN --> CH
        EF --> EG
    end

    style MQL5 fill:#ffffff,stroke:#d4af37,stroke-width:2px
    style RUST fill:#ffffff,stroke:#d4af37,stroke-width:2px
```
