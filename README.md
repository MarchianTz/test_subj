# Financial Tracker Diagrams (PlantUML)

This directory contains UML diagrams for the Financial Tracker application in PlantUML format.

## Files

### Use Case Diagram
- **`use-case-diagram.puml`** — Shows the main actor (User), primary use cases (View, Add, Delete Transactions), and their relationship to the Persist use case.

### Sequence Diagrams
- **`sequence-view-transactions.puml`** — Shows the complete flow when a user navigates to view the transaction list.
- **`sequence-add-transaction.puml`** — Shows the flow when a user submits a new transaction (includes form validation and navigation).
- **`sequence-delete-transaction.puml`** — Shows the flow when a user deletes a transaction and the list refreshes.

## How to View / Render

### Option 1: Online PlantUML Editor
1. Go to [PlantUML Editor](https://www.plantuml.com/plantuml/uml/)
2. Copy/paste the `.puml` file content
3. Click "Submit" or press Ctrl+Enter to render
4. Export as PNG, SVG, or PDF from the menu

### Option 2: VS Code Extension
1. Install "PlantUML" extension by jebbs.plantuml
2. Open the `.puml` file
3. Press `Alt+D` to preview
4. Right-click → "Export Current Diagram" to save as PNG/SVG

### Option 3: Command Line (requires Java)
```bash
# Download plantuml.jar from https://plantuml.com/download
java -jar plantuml.jar use-case-diagram.puml
java -jar plantuml.jar sequence-view-transactions.puml
java -jar plantuml.jar sequence-add-transaction.puml
java -jar plantuml.jar sequence-delete-transaction.puml
```

## Architecture Overview

The Financial Tracker follows **MVC (Model-View-Controller)** pattern:

- **Models** (`src/app/models/`) — Data structures (Transaction interface)
- **Controllers** (`src/app/transaction-list/`, `src/app/add-transaction/`) — Angular Components managing logic and state
- **Views** (`.html` templates) — UI templates rendered by components
- **Services** (`src/app/services/`) — Business logic layer (TransactionService with localStorage persistence)

## Diagram Descriptions

### Use Case Diagram
- **Actor**: User (primary actor)
- **Use Cases**:
  - View Transactions: Display all stored transactions
  - Add Transaction: Create new transaction with form
  - Delete Transaction: Remove a transaction
  - Persist to LocalStorage: Common use case for storage operations
- **Relationships**: Add/Delete/View all include persistence

### Sequence: View Transactions
**Participants**: User → View (Template) → Controller (TransactionList) → Model (TransactionService) → LocalStorage

**Flow**:
1. User navigates to /transactions
2. Component's ngOnInit() is called
3. Controller calls TransactionService.getAll()
4. Service retrieves transactions from localStorage
5. Controller updates component state
6. View re-renders the Material table

### Sequence: Add Transaction
**Participants**: User → View (Form) → Controller (AddTransaction) → Model (TransactionService) → LocalStorage → Router

**Flow**:
1. User navigates to /add and fills the form
2. User submits the form
3. Controller validates the form
4. If valid, controller calls TransactionService.add()
5. Service generates auto-incremented ID and saves to localStorage
6. Router navigates back to /transactions
7. TransactionList component loads and displays updated list

### Sequence: Delete Transaction
**Participants**: User → View (Table) → Controller (TransactionList) → Model (TransactionService) → LocalStorage

**Flow**:
1. User clicks the delete button on a transaction row
2. Controller calls TransactionService.delete(id)
3. Service finds and removes the transaction, saves to localStorage
4. Controller reloads the transaction list by calling getAll()
5. View re-renders with updated data

## Notes

- All data is persisted to browser's `localStorage` under key `ft_transactions_v1`
- IDs are auto-incremented based on existing transactions
- Sorting is done by date (newest first) in the service layer
- Material Design components are used for UI (Angular Material)
