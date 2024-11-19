# Trading tool documentation

This document encapsulates the key aspects of our recent conversation regarding the development and refinement of UML diagrams for a Python trading script project. It serves as a comprehensive guide to ensure seamless continuity in future interactions with ChatGPT.
---

## Bullet-Point Summary

- **Project Focus:** Development of UML diagrams for a Python trading script utilizing a Multi-Timeframe MACD Confluence Strategy.
- **Initial UML Diagrams Created:**
  - Use Case Diagram
  - Class Diagram
  - Sequence Diagram
  - Activity Diagram
  - State Diagram
  - Component Diagram
- **Key Modifications:**
  - Separation of backtesting functions from live trading functions.
  - Introduction of new classes: `Backtester`, `DataGapChecker`, and `TradeManager`.
  - Integration of `IndicatorCalculator` within `DataProcessor`.
  - Renaming of "Generate Signals" to "Generate Order Signals" for clarity.
  - Removal of redundant data gap checks in the live trading path.
  - Renaming of `fetch_price_data` to `fetch_api_price_data`.
- **Workflow Enhancements:**
  - Conditional execution paths based on configuration settings (backtesting vs. live trading).
  - Ensuring data integrity by checking for data gaps prior to backtesting or live trading.
- **Documentation Updates:**
  - Iterative refinement of UML diagrams to reflect structural and functional changes.
  - Clarification of specific workflow steps, such as "Generate Order Signals."

---

## Decisions Made

1. **Separation of Functionalities:**
   - **Backtesting** and **Live Trading** are to be handled in distinct workflows to prevent simultaneous execution.
2. **Class Structure Refinement:**
   - Introduced new classes (`Backtester`, `DataGapChecker`, `TradeManager`) to encapsulate specific functionalities.
   - `IndicatorCalculator` is integrated within the `DataProcessor` class to handle indicator calculations as part of the ETL process.
3. **Workflow Optimization:**
   - `check_for_data_gaps()` is executed immediately after retrieving active strategies to ensure data currency for both backtesting and live trading.
   - Removed redundant data gap checks within the live trading execution path.
4. **Naming Conventions:**
   - Renamed "Generate Signals" to "Generate Order Signals" across all UML diagrams for enhanced clarity.
   - Renamed the function `fetch_price_data` to `fetch_api_price_data` to better reflect its purpose.

---

## Constraints Identified

- **Mutual Exclusivity of Modes:**
  - The system must operate in either **backtesting** mode or **live trading** mode at any given time, not both simultaneously.
- **Data Integrity:**
  - Ensuring that all data gaps are addressed before initiating backtesting or live trading to maintain accuracy and reliability.
- **Manual Invocation:**
  - Certain functions, like `upsert_price_data`, are designed to be invoked manually, adding a layer of control for the user.

---

## Key Insights

- **Modular Design:** Breaking down functionalities into distinct classes and modules (e.g., `Backtester`, `TradeManager`) enhances maintainability and scalability.
- **Data Processing Integration:** Incorporating `IndicatorCalculator` within `DataProcessor` streamlines the ETL process, ensuring that indicator calculations are tightly coupled with data handling.
- **Clear Separation of Concerns:** Differentiating between backtesting and live trading workflows prevents operational conflicts and promotes focused functionality.
- **Enhanced Clarity through Naming:** Precise naming conventions, such as changing to "Generate Order Signals," improve the comprehensibility of the system's processes.

---

## Chronological Progression

1. **Initial UML Diagram Creation:**
   - Developed foundational UML diagrams to outline the Python trading script's structure and functionalities.
2. **Incorporation of Partitions:**
   - Added partitions to separate backtesting functions from live trading functions.
   - Introduced manual invocation capability for `upsert_price_data`.
3. **Introduction of New Classes:**
   - Added `Backtester`, `DataGapChecker`, and `TradeManager` to handle specific workflows.
4. **Refinement of Class Relationships:**
   - Integrated `IndicatorCalculator` within `DataProcessor`.
   - Removed direct dependency between `StrategyManager` and `IndicatorCalculator`.
5. **Function Renaming and Streamlining:**
   - Renamed `fetch_price_data` to `fetch_api_price_data`.
   - Removed redundant data gap checks in the live trading sequence.
6. **Clarification of Workflow Steps:**
   - Renamed "Generate Signals" to "Generate Order Signals" for specificity.
   - Detailed the "Generate Order Signals" step in the Activity Diagram.
7. **Final Adjustments:**
   - Ensured consistency across all UML diagrams reflecting the latest structural and functional changes.

---

## Code Snippets to Be Used

### 1. **Class Diagrams**

```python
class StrategyManager:
    def get_active_strategies(self, config):
        """
        Retrieve the list of active strategies from the configuration.
        """
        active_strategies = [strategy for strategy, is_active in config['strategies'].items() if is_active]
        return active_strategies

    def strategy_macd_confluence(self, data):
        """
        Implement the MACD Multi-Timeframe Momentum Highlighter strategy.
        """
        # Strategy implementation
        pass

    def strategy_rsi_threshold(self, data):
        """
        Implement an RSI threshold strategy.
        """
        # Strategy implementation
        pass

    def generate_order_signals(self, data):
        """
        Generate buy and sell signals based on active strategies.
        """
        signals = {}
        if 'macd_confluence' in active_strategies:
            signals['macd_confluence'] = self.strategy_macd_confluence(data)
        if 'rsi_threshold' in active_strategies:
            signals['rsi_threshold'] = self.strategy_rsi_threshold(data)
        return signals
```

### 2. **Sequence Diagrams**

```plantuml
@startuml
actor User

participant Main
participant ConfigurationManager
participant GoogleAPIAuthenticator
participant DataRetriever
participant StrategyManager
participant DataProcessor
participant Backtester
participant TradeManager
participant TradeExecutor
participant Logger
participant ErrorHandler

User -> Main: main()
Main -> ConfigurationManager: load_configuration()
ConfigurationManager --> Main: config

Main -> GoogleAPIAuthenticator: authenticate_google_apis()
GoogleAPIAuthenticator --> Main: Services Authenticated

Main -> DataRetriever: get_folder_id_from_url(folder_url)
DataRetriever --> Main: folder_id

Main -> DataRetriever: get_top_assets(asset_type, n)
DataRetriever --> Main: assets

Main -> StrategyManager: get_active_strategies(config)
StrategyManager --> Main: active_strategies

Main -> DataProcessor: check_for_data_gaps()
DataProcessor -> DataGapChecker: check_for_data_gaps()
DataGapChecker --> DataProcessor: gaps_found / no_gaps
alt Gaps Found
    DataProcessor -> DataRetriever: fetch_api_price_data()
    DataRetriever --> DataProcessor: data_updated
end
DataProcessor --> Main: data_checked

alt Backtesting Enabled
    Main -> Backtester: run_backtests()
    Backtester -> StrategyManager: strategy_macd_confluence()
    Backtester --> Main: Backtest Results
else Live Trading Enabled
    Main -> StrategyManager: generate_order_signals()
    StrategyManager --> Main: signals
    Main -> TradeManager: trigger_buy_sell_orders()
    TradeManager -> TradeExecutor: execute_trades(signals)
    TradeExecutor --> TradeManager: trades_executed
end

Main --> Logger: log activities
Main --> ErrorHandler: handle_errors(e)

@enduml
```

### 3. **Activity Diagrams**

```plantuml
@startuml
start

:Load Configuration;
:Authenticate with Google APIs;
:Retrieve Folder ID;
:Get Top Assets;
:Get Active Strategies;

:Check for Data Gaps;
if (Gaps Found?) then (yes)
    :Upsert Price Data;
else (no)
    :Proceed;
endif

if (Backtesting Enabled?) then (yes)
    partition Backtesting {
        :Run Backtests;
        :Generate Backtest Reports;
    }
else (no)
    partition Live_Trading {
        :Generate Order Signals;
        note right of Generate Order Signals
            Analyzes asset data using active strategies
            and technical indicators to identify
            buy/sell opportunities.
        end note
        :Trigger Buy/Sell Orders;
    }
endif

:Handle Errors;

stop
@enduml
```

### 4. **Class Diagrams**

```plantuml
@startuml
class Main {
    +main()
    +run_backtesting()
    +run_live_trading()
}

class ConfigurationManager {
    -config_file: str
    -config: dict
    +load_configuration()
    +create_default_configuration()
    +update_configuration()
    +check_missing_settings()
    +prompt_user_for_settings()
    +is_backtesting_enabled(): bool
}

class GoogleAPIAuthenticator {
    -drive_service
    -sheets_service
    +authenticate_google_apis()
}

class DataRetriever {
    +get_top_assets()
    +fetch_api_price_data()
}

class DataProcessor {
    +upsert_price_data()
    +ETL_asset_data()
    +check_and_fill_data_gaps()
    +calculate_indicators()
}

class StrategyManager {
    +get_active_strategies()
    +strategy_macd_confluence()
    +strategy_rsi_threshold()
    +generate_order_signals()
}

class TradeExecutor {
    +execute_trades()
    +execute_alpaca_trades()
    +execute_ib_trades()
}

class Logger {
    +log_message()
}

class ErrorHandler {
    +handle_errors()
}

class Backtester {
    +run_backtests()
}

class DataGapChecker {
    +check_for_data_gaps()
}

class TradeManager {
    +trigger_buy_sell_orders()
}

Main --> ConfigurationManager
Main --> GoogleAPIAuthenticator
Main --> DataRetriever
Main --> DataProcessor
Main --> StrategyManager
Main --> TradeExecutor
Main --> Logger
Main --> ErrorHandler

StrategyManager --> DataProcessor

Main --> Backtester : if backtesting enabled
Main --> TradeManager : if live trading enabled

DataProcessor --> DataGapChecker
TradeManager --> DataGapChecker

Backtester --> StrategyManager
Backtester --> DataRetriever

TradeManager --> TradeExecutor
TradeManager --> StrategyManager
TradeManager --> DataRetriever

@enduml
```

### 5. **State Diagrams**

```plantuml
@startuml
[*] --> Idle

Idle --> CheckingDataGaps : Triggered
CheckingDataGaps --> UpsertingData : Gaps Found
CheckingDataGaps --> GenerateOrderSignals : No Gaps

UpsertingData --> GenerateOrderSignals : Data Upserted

GenerateOrderSignals --> ExecutingBuyOrder : Buy Signal
GenerateOrderSignals --> ExecutingSellOrder : Sell Signal
GenerateOrderSignals --> Idle : No Signal

ExecutingBuyOrder --> TradeCompleted : Buy Order Executed
ExecutingSellOrder --> TradeCompleted : Sell Order Executed

TradeCompleted --> Idle

ExecutingBuyOrder --> Error : Execution Failed
ExecutingSellOrder --> Error : Execution Failed
TradeCompleted --> Error : Post-Trade Error

Error --> Idle : Handle Error

@enduml
```
