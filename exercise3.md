## 3. Automated Market Monitoring & Watchlists

Your current system allows for manual trading of stocks. However, sophisticated investors rarely sit in front of a screen waiting for a specific price. They use **Watchlists** with automated triggers.

In this assignment, you will extend your existing stock market application to support user-defined Watchlists that automatically identify buying opportunities based on real-time market data.

### 3.1 Watchlist Management

Each **User** in the system can now own multiple **Watchlists** (e.g., "Tech Growth," "Energy Dividends").

* A Watchlist is a collection of **Entries**.
* Each Entry consists of a **Stock Ticker** (e.g., "AAPL") and a **Threshold Price**.
* A Ticker can appear in multiple watchlists across the same or different users.

**New Feature:** Implement a way for users to create a new Watchlist with stock tickers and their respective threshold prices.
**Users:** Note that we do not need a representation of the user in the system. A _Watchlist_ can represent its user owner as a String (ownerName).

### 3.2 The "Market Sentinel" (Daemon Process)

You must implement a background process (a "Daemon") that monitors the market.

* **Frequency:** The process must run periodically. The interval (minutes or hours) must be configurable via your application's configuration files (e.g., `application.properties`).
* **Logic:** For every ticker present in any active Watchlist, the process must fetch the current market price using the existing external API adapters.
* **Trigger:** If the **current price** is less than or equal to the **threshold price** defined in a Watchlist, the system should log a "Buy Signal" to the console (simulating a notification):
> `[BUY SIGNAL] User: {onerName} | List: {listName} | Ticker: {symbol} | Target price: {target price} | Current price: {current price}`

### 3.3 Technical Constraints

#### Persistence & Mapping

You must store these new entities in the database using JPA.

* **The Challenge:** Ensure that your business logic (Domain) remains entirely decoupled from your database logic (Persistence).
* **Requirement:** Your Domain objects must not contain JPA annotations (like `@Entity` or `@Table`). You must implement mappers to convert between the Database Entities and the Domain Objects within your persistence adapter.

#### The Daemon Process (Spring)

To implement the background process in Spring, you should look into the `@Scheduled` annotation.

* Consider whether this daemon acts as a **Driving Actor**.
* If it is a driving actor, does it need to interact with the core through a specific **Input Port**?
* Ensure the daemon does not contain business logic; its only job is to trigger the appropriate Use Case at the right time.

### 3.4 Evaluation Criteria

| Criteria | Expectation |
| --- | --- |
| **Hexagonal Integrity** | No "leakage" of JPA or Web dependencies into the Domain layer. |
| **Granularity** | Proper separation between the `Watchlist` aggregate and the `Portfolio` aggregate. |
| **Configuration** | The background task interval is properly read from environmental properties. |
| **Relational Mapping** | Correct handling of the Many-to-Many relationship between Watchlists and Tickers in the persistence adapter. |

