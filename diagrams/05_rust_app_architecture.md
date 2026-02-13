# Rust Application Architecture

> **Figure for**: Section III-C (Rust Application Architecture)  
> **Suggested caption**: "Tokio async runtime and MPSC channel architecture"  
> **LaTeX label**: `fig:rust_arch`

```mermaid
flowchart TB
    subgraph TOKIO["Tokio Async Runtime"]
        TICK_TASK["Tick Subscriber Task<br/>SubSocket :5555"]
        ORDER_TASK["Order Handler Task<br/>ReqSocket :5556"]
    end

    subgraph CHANNELS["MPSC Channels"]
        TICK_CH["tick_channel<br/>(capacity: 100)"]
        ORDER_CH["order_channel<br/>(capacity: 10)"]
        RESP_CH["response_channel<br/>(capacity: 10)"]
    end

    subgraph APP["Mt5ChartApp State"]
        STATE["Application State<br/>• data: Vec of TickData<br/>• balance, equity, margin<br/>• positions, orders<br/>• UI state fields"]
    end

    subgraph EGUI["egui GUI (Main Thread)"]
        CHART["Price Chart Panel"]
        ACCT["Account Info Panel"]
        TRADE["Trade Controls"]
        HIST["History Download"]
        REC["Live Recording"]
        POS["Positions Panel"]
        ORD["Orders Panel"]
    end

    TICK_TASK --> TICK_CH
    ORDER_TASK <--> ORDER_CH
    ORDER_TASK <--> RESP_CH

    TICK_CH --> STATE
    STATE <--> ORDER_CH
    RESP_CH --> STATE

    STATE --> CHART
    STATE --> ACCT
    STATE --> TRADE
    STATE --> HIST
    STATE --> REC
    STATE --> POS
    STATE --> ORD

    style TOKIO fill:#e6f3ff,stroke:#0066cc,stroke-width:2px
    style EGUI fill:#ffe6f0,stroke:#cc0066,stroke-width:2px
```
