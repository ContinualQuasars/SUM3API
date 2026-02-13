# System Overview Architecture

> **Figure for**: Section III (Methodology) — System Architecture  
> **Suggested caption**: "SUM3API three-layer system architecture"  
> **LaTeX label**: `fig:system_overview`

```mermaid
flowchart TB
    subgraph MT5["MetaTrader 5"]
        EA[ZmqPublisher.mq5<br/>Expert Advisor]
        ZMQ_LIB[MQL5’s native DLL<br/>libzmq.dll + <br/>libsodium.dll]
        WRAPPER[Zmq.mqh<br/>MQL5 Wrapper]
    end
    
    subgraph Network["ZeroMQ Transport"]
        PUB_SOCKET[PUB Socket<br/>Port 5555]
        REP_SOCKET[REP Socket<br/>Port 5556]
    end
    
    subgraph RUST["Rust Application"]
        SUB[SubSocket<br/>Tick Receiver]
        REQ[ReqSocket<br/>Order Sender]
        GUI[egui + egui_plot<br/>Trading GUI]
    end
    
    EA --> ZMQ_LIB
    ZMQ_LIB --> PUB_SOCKET & REP_SOCKET
    WRAPPER --> EA
    
    PUB_SOCKET -.->|Tick Data| SUB
    REQ -.->|Order Request| REP_SOCKET
    REP_SOCKET -.->|Response| REQ
    
    SUB --> GUI
    REQ --> GUI
```
