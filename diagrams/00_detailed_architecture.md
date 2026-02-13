flowchart LR
    subgraph USER_SPACE["User Space"]
        USER[("User")]
    end
    
    subgraph MT5_PLATFORM["MetaTrader 5 Platform (Authenticated Process)"]
        direction LR
        
        subgraph AUTH["Authentication Layer"]
            MT5_GUI[MT5 Terminal GUI]
            SESSION["Authenticated Session<br/>[+] Account ID<br/>[+] Server Connection<br/>[+] Trading Permissions"]
        end
        
        subgraph DATA_SOURCES["MT5 Data Sources"]
            direction LR
            MARKET[("Market Data Feed<br/>Tick Stream")]
            ACCOUNT_DB[("Account Database<br/>ACCOUNT_BALANCE<br/>ACCOUNT_EQUITY<br/>ACCOUNT_MARGIN<br/>ACCOUNT_MARGIN_FREE")]
            POSITIONS_DB[("Positions Database<br/>Active Trades")]
            ORDERS_DB[("Orders Database<br/>Pending Orders")]
            HISTORY_DB[("Historical Database<br/>OHLC & Tick Data")]
        end
        
        subgraph EA_LAYER["Expert Advisor Layer"]
            EA[ZmqPublisher.mq5<br/>Expert Advisor]
            TRADE_ENGINE[CTrade Engine<br/>Order Execution]
        end
        
        USER -->|1. Manual Login<br/>account + password + server| MT5_GUI
        MT5_GUI --> SESSION
        SESSION -.->|Inherits Session| EA
        
        MARKET --> EA
        ACCOUNT_DB --> EA
        POSITIONS_DB --> EA
        ORDERS_DB --> EA
        HISTORY_DB --> EA
        EA --> TRADE_ENGINE
    end
    
    subgraph ZMQ_LAYER["ZeroMQ Transport Layer (localhost)"]
        direction LR
        PUB_SOCKET[["[PUB] PUB Socket<br/>tcp://0.0.0.0:5555<br/>Broadcast Mode"]]
        REP_SOCKET[["[REP] REP Socket<br/>tcp://0.0.0.0:5556<br/>Request-Reply Mode"]]
    end
    
    subgraph RUST_APP["Rust Application (mt5-chart)"]
        direction LR
        
        subgraph ASYNC_RUNTIME["Tokio Async Runtime"]
            TICK_TASK[["[Task] Tick Subscriber Task<br/>SubSocket<br/>Port 5555"]]
            ORDER_TASK[["[Task] Order Handler Task<br/>ReqSocket<br/>Port 5556"]]
        end
        
        subgraph CHANNELS["MPSC Channels"]
            direction LR
            TICK_CHAN[Tick Channel<br/>capacity: 100]
            ORDER_CHAN[Order Request Channel<br/>capacity: 10]
            RESPONSE_CHAN[Order Response Channel<br/>capacity: 10]
        end
        
        subgraph APP_STATE["Application State"]
            STATE[Mt5ChartApp<br/>• data: Vec&lt;TickData&gt;<br/>• balance, equity, margin<br/>• positions, orders<br/>• UI state]
        end
        
        subgraph GUI["egui GUI Components"]
            direction LR
            CHART[["[Chart] Price Chart<br/>Bid/Ask Lines<br/>Position Lines<br/>Order Breaklines"]]
            ACCOUNT_PANEL[["[Account] Account Info Panel<br/>Balance, Equity<br/>Margin, Free Margin"]]
            TRADE_PANEL[["[Trade] Trade Controls<br/>Market Orders<br/>Pending Orders"]]
            HISTORY_PANEL[["[History] History Download<br/>OHLC/Tick CSV Export"]]
            RECORD_PANEL[["[REC] Live Recording<br/>Real-time CSV Capture"]]
            POSITIONS_PANEL[["[Pos] Active Positions<br/>Close Management"]]
            ORDERS_PANEL[["[Orders] Pending Orders<br/>Cancel Management"]]
        end
        
        TICK_TASK --> TICK_CHAN
        ORDER_TASK <--> ORDER_CHAN
        ORDER_TASK <--> RESPONSE_CHAN
        
        TICK_CHAN --> STATE
        STATE <--> ORDER_CHAN
        RESPONSE_CHAN --> STATE
        
        STATE --> CHART
        STATE --> ACCOUNT_PANEL
        STATE --> TRADE_PANEL
        STATE --> HISTORY_PANEL
        STATE --> RECORD_PANEL
        STATE --> POSITIONS_PANEL
        STATE --> ORDERS_PANEL
    end
    
    EA --> PUB_SOCKET
    EA <--> REP_SOCKET
    
    PUB_SOCKET -.->|JSON Tick Stream<br/>Non-blocking| TICK_TASK
    ORDER_TASK -.->|JSON Request<br/>Blocking| REP_SOCKET
    REP_SOCKET -.->|JSON Response<br/>Blocking| ORDER_TASK
    
    %% White-Gold Theme Styles
    style USER_SPACE fill:#ffffff,stroke:#d4af37,stroke-width:2px
    style MT5_PLATFORM fill:#fffbea,stroke:#d4af37,stroke-width:3px
    style AUTH fill:#fff9e6,stroke:#d4af37,stroke-width:2px
    style ZMQ_LAYER fill:#ffffff,stroke:#d4af37,stroke-width:3px
    style RUST_APP fill:#fffbea,stroke:#d4af37,stroke-width:3px
    style SESSION fill:#ffffff,stroke:#d4af37,stroke-width:2px
