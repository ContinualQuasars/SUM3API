# Data Structures & Type Hierarchy

> **Figure for**: Section IV-B (Rust Implementation — Data Structures)  
> **Suggested caption**: "MQL5-to-Rust data structure mapping"  
> **LaTeX label**: `fig:data_structures`

```mermaid
classDiagram
    class TickData {
        +String symbol
        +f64 bid
        +f64 ask
        +i64 time
        +u64 volume
        +f64 balance
        +f64 equity
        +f64 margin
        +f64 free_margin
        +f64 min_lot
        +f64 max_lot
        +f64 lot_step
        +Vec~PositionData~ positions
        +Vec~PendingOrderData~ orders
    }

    class PositionData {
        +u64 ticket
        +String pos_type
        +f64 volume
        +f64 price
        +f64 profit
    }

    class PendingOrderData {
        +u64 ticket
        +String order_type
        +f64 volume
        +f64 price
    }

    class OrderRequest {
        +String order_type
        +String symbol
        +f64 volume
        +f64 price
        +u64 ticket
        +Option~String~ timeframe
        +Option~String~ start
        +Option~String~ end
        +Option~String~ mode
    }

    class OrderResponse {
        +bool success
        +Option~i64~ ticket
        +Option~String~ error
        +Option~String~ message
    }

    TickData "1" *-- "*" PositionData
    TickData "1" *-- "*" PendingOrderData
```
