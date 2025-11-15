# Use Case Descriptions - Financial Tracker

## Use Case 1: View Transactions

| Attribute | Description |
|-----------|-------------|
| **Use Case Name** | View Transactions |
| **Goal** | Display all stored transactions in a sortable, filterable list with details (date, description, amount, type) |
| **Actors** | User (primary) |
| **Preconditions** | • User is logged into the application<br>• User has navigated to the transactions page or app has loaded<br>• At least zero transactions exist in localStorage |
| **Main Flow** | 1. User navigates to `/transactions` route<br>2. TransactionList component initializes (ngOnInit)<br>3. Component calls TransactionService.getAll()<br>4. Service retrieves transactions from localStorage (key: `ft_transactions_v1`)<br>5. Service sorts transactions by date (newest first)<br>6. Service returns sorted array to component<br>7. Component updates `transactions[]` property<br>8. View renders Material table with all transactions<br>9. User sees transaction details: Date, Description, Amount (in IDR), Type (income/expense) |
| **Postconditions** | • Transaction list is displayed on screen<br>• User can see all stored transactions<br>• User can interact with delete buttons for each transaction<br>• User can navigate to "Add Transaction" form |
| **Alternative Flows** | **A1: No transactions exist**<br>  - Service returns empty array<br>  - View displays empty table message or placeholder<br>**A2: LocalStorage is corrupted or empty**<br>  - Service initializes empty transactions array<br>  - View displays empty table |
| **Exceptions** | **E1: Browser localStorage is disabled**<br>  - Service logs error<br>  - User sees empty list (graceful degradation)<br>**E2: Invalid JSON in localStorage**<br>  - Service catches parsing error, reinitializes as empty<br>  - User sees empty list |

---

## Use Case 2: Add Transaction

| Attribute | Description |
|-----------|-------------|
| **Use Case Name** | Add Transaction |
| **Goal** | Create a new transaction with user-provided details and persist it to storage |
| **Actors** | User (primary) |
| **Preconditions** | • User is logged into the application<br>• User has navigated to `/add` route (Add Transaction page)<br>• User has access to a form with fields: date, description, amount, type |
| **Main Flow** | 1. User navigates to `/add` route<br>2. AddTransaction component renders form with fields: date picker, description input, amount input, type dropdown (income/expense)<br>3. User fills in form:<br>   - Selects date (required)<br>   - Enters description (required)<br>   - Enters amount as number ≥ 0 (required)<br>   - Selects type: "income" or "expense" (required)<br>4. User clicks "Add Transaction" button<br>5. Component validates form (all fields required, amount ≥ 0)<br>6. If valid, component calls TransactionService.add({date, description, amount, type})<br>7. Service generates auto-incremented ID (max existing ID + 1)<br>8. Service creates new Transaction object with generated ID<br>9. Service adds transaction to in-memory array<br>10. Service persists array to localStorage (key: `ft_transactions_v1`)<br>11. Service returns new Transaction object<br>12. Component calls Router.navigate(['/transactions'])<br>13. User is redirected to transaction list page<br>14. TransactionList component loads and displays updated list with new transaction |
| **Postconditions** | • Transaction is created and saved to localStorage<br>• Transaction has a unique auto-incremented ID<br>• User is redirected to the transaction list<br>• New transaction appears in the list (sorted by date)<br>• User can view, delete, or add more transactions |
| **Alternative Flows** | **A1: Form validation fails**<br>  - Component displays error message near invalid field<br>  - Form is not submitted<br>  - User corrects input and retries<br>**A2: Amount is invalid (negative or non-numeric)**<br>  - Form field shows validation error<br>  - Submit button remains disabled<br>  - User corrects input |
| **Exceptions** | **E1: LocalStorage quota exceeded**<br>  - Service.add() catches error<br>  - User sees error message: "Unable to save transaction"<br>  - Transaction is not persisted<br>**E2: Browser localStorage is disabled**<br>  - Service.add() catches error<br>  - User sees error message: "Storage unavailable"<br>  - Transaction is lost on page reload<br>**E3: User navigates away before save completes**<br>  - Save operation still completes asynchronously<br>  - Transaction is persisted if no error occurs |

---

## Use Case 3: Delete Transaction

| Attribute | Description |
|-----------|-------------|
| **Use Case Name** | Delete Transaction |
| **Goal** | Remove a transaction from storage and update the display |
| **Actors** | User (primary) |
| **Preconditions** | • User is logged into the application<br>• User is on the transaction list page (`/transactions`)<br>• At least one transaction exists in the list<br>• User can see the delete button (trash icon) on a transaction row |
| **Main Flow** | 1. User views the transaction list with Material table<br>2. User locates a transaction to delete<br>3. User clicks the delete (trash) button on the transaction row<br>4. Component calls deleteTransaction(id) with the transaction ID<br>5. Component calls TransactionService.delete(id)<br>6. Service searches for transaction with matching ID in array<br>7. If found, service removes transaction from array (splice)<br>8. Service persists updated array to localStorage (key: `ft_transactions_v1`)<br>9. Service returns true (success)<br>10. Component calls loadTransactions() to refresh the list<br>11. Component calls TransactionService.getAll()<br>12. Service returns updated sorted transaction array<br>13. Component updates `transactions[]` property<br>14. View re-renders table without the deleted transaction<br>15. User sees updated list |
| **Postconditions** | • Transaction is removed from localStorage<br>• Transaction no longer appears in the list<br>• User sees updated transaction count (if displayed)<br>• Remaining transactions are still visible and sorted |
| **Alternative Flows** | **A1: Transaction ID not found**<br>  - Service returns false<br>  - Component silently fails (no visual change)<br>  - User sees the same list (transaction was already deleted)<br>**A2: User clicks delete by mistake**<br>  - Current implementation has no undo or confirmation dialog<br>  - Transaction is immediately deleted<br>  - (Consider adding confirmation modal in future) |
| **Exceptions** | **E1: LocalStorage is disabled or corrupted**<br>  - Service.delete() catches error<br>  - Service logs error and returns false<br>  - List may not refresh; user sees stale data<br>**E2: Transaction array is empty**<br>  - Service finds no matching ID<br>  - Returns false<br>  - No error shown to user |

---

## Use Case 4: Persist to LocalStorage

| Attribute | Description |
|-----------|-------------|
| **Use Case Name** | Persist to LocalStorage |
| **Goal** | Save transaction data to browser's localStorage for offline persistence and app state recovery |
| **Actors** | System (TransactionService), Browser (LocalStorage) |
| **Preconditions** | • Browser supports localStorage API<br>• Browser storage quota has available space<br>• Transaction data exists in memory (TransactionService array) |
| **Main Flow** | 1. TransactionService has a transaction operation (add, delete, or init)<br>2. Service calls private save() method<br>3. save() method converts transactions array to JSON string<br>4. save() calls localStorage.setItem("ft_transactions_v1", jsonString)<br>5. Browser's localStorage engine persists data to disk<br>6. Operation completes successfully<br>7. Data is now recoverable across browser sessions |
| **Postconditions** | • Transaction array is stored in localStorage under key `ft_transactions_v1`<br>• Data persists even after browser closes<br>• Application can recover data on next load<br>• Service maintains in-memory copy in sync with localStorage |
| **Alternative Flows** | **A1: First app load (no prior data)**<br>  - Service load() method calls localStorage.getItem(...)<br>  - Returns null<br>  - Service initializes empty array and saves to localStorage<br>  - First save operation creates the key |
| **Exceptions** | **E1: LocalStorage quota exceeded**<br>  - localStorage.setItem() throws QuotaExceededError<br>  - Service.save() catches error and logs to console<br>  - Transaction is NOT persisted<br>  - In-memory array still contains the transaction<br>  - User may lose data on page reload<br>**E2: LocalStorage is disabled by browser**<br>  - localStorage.setItem() throws error<br>  - Service catches and logs error<br>  - App continues with in-memory data only<br>  - Data is lost on page reload<br>**E3: Corrupted JSON in localStorage**<br>  - Service load() catches JSON.parse() error<br>  - Service reinitializes empty array<br>  - Corrupted data is overwritten on next save |

---

## Summary Table

| Use Case | Primary Actor | Trigger | Main Outcome | Data Involved |
|----------|---------------|---------|--------------|----------------|
| View Transactions | User | Navigate to /transactions | Display sorted transaction list | Transaction array from localStorage |
| Add Transaction | User | Submit form | Create new transaction and redirect | New Transaction object, auto-incremented ID |
| Delete Transaction | User | Click delete button | Remove transaction and refresh list | Transaction ID, updated array |
| Persist to LocalStorage | System | Add/Delete operation | Save data to localStorage | Transaction array (JSON serialized) |

---

## Key Notes

- **Data Storage**: All use cases interact with browser localStorage (key: `ft_transactions_v1`)
- **Persistence Layer**: TransactionService acts as a single source of truth
- **UI Framework**: Angular components (TransactionList, AddTransaction) manage views and user interactions
- **Material Design**: Angular Material table and form components provide the UI
- **Error Handling**: All use cases handle edge cases (missing data, quota exceeded, disabled storage)
- **Offline-First**: Application works offline; data syncs to localStorage
