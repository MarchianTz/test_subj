# Financial Tracker - Complete Project Documentation

## Project Overview

The Financial Tracker is a modern Angular web application designed for personal finance management. It allows users to track income and expenses with a clean, intuitive interface built using Angular Material components.

**Current Version Features:**
- ✅ Transaction CRUD operations (Create, Read, Update, Delete)
- ✅ Material Design UI with responsive table
- ✅ Form validation and date picker
- ✅ Local storage persistence
- ✅ Edit existing transactions
- ✅ Dynamic button labels ("Add Transaction" / "Save Changes")
- ✅ Horizontal action buttons layout

## Architecture Overview

### System Architecture Diagram

```plantuml
@startuml System Architecture
!theme plain
skinparam backgroundColor #FEFEFE
skinparam component {
    BackgroundColor #F0F8FF
    BorderColor #4682B4
    BorderThickness 2
}

package "Angular Application" as App {
    component [TransactionList\nComponent] as List
    component [AddTransaction\nComponent] as Form
    component [Transaction\nService] as Service
}

database "Local Storage" as Storage {
    folder "ft_transactions_v1" as Data
}

List --> Service : getAll(), delete()
Form --> Service : add(), update()
Service --> Storage : Read/Write JSON
Service --> Data : Persist Transactions

note right of Service
    Manages transaction data
    Handles CRUD operations
    Persists to localStorage
end note
@enduml
```

### Component Architecture Diagram

```plantuml
@startuml Component Architecture
!theme plain
skinparam backgroundColor #FEFEFE
skinparam package {
    BackgroundColor #F0F8FF
    BorderColor #4682B4
}

package "App Module" {
    package "Components" {
        [TransactionListComponent] as List
        [AddTransactionComponent] as Form
    }

    package "Services" {
        [TransactionService] as Service
    }

    package "Models" {
        [Transaction] as Model
    }
}

package "Angular Material" {
    [MatTable] as Table
    [MatFormField] as FormField
    [MatButton] as Button
    [MatDatepicker] as DatePicker
}

List --> Table : uses
Form --> FormField : uses
Form --> Button : uses
Form --> DatePicker : uses

List --> Service : injects
Form --> Service : injects

Service --> Model : uses

note right of List
    Displays transaction table
    Handles edit/delete actions
    Routes to edit form
end note

note right of Form
    Reusable add/edit form
    Dynamic button labels
    Form validation
end note
@enduml
```

## Data Flow Diagrams

### Add Transaction Flow

```plantuml
@startuml Add Transaction Flow
!theme plain
skinparam backgroundColor #FEFEFE
skinparam sequenceParticipant underline

actor User
participant "AddTransaction\nComponent" as Form
participant "Transaction\nService" as Service
participant "LocalStorage" as Storage

User -> Form: Fill form fields
User -> Form: Click "Add Transaction"
Form -> Form: Validate form
Form -> Service: add(transaction)
Service -> Service: Generate next ID
Service -> Service: Create transaction object
Service -> Service: Add to transactions array
Service -> Storage: Save to localStorage
Service --> Form: Return new transaction
Form -> Form: Navigate to /transactions
@enduml
```

### Edit Transaction Flow

```plantuml
@startuml Edit Transaction Flow
!theme plain
skinparam backgroundColor #FEFEFE
skinparam sequenceParticipant underline

actor User
participant "TransactionList\nComponent" as List
participant "Router" as Router
participant "AddTransaction\nComponent" as Form
participant "Transaction\nService" as Service
participant "LocalStorage" as Storage

User -> List: Click Edit button
List -> Router: navigate('/edit/:id')
Router -> Form: Load component with route param
Form -> Form: Set isEditing = true
Form -> Service: getAll()
Service -> Storage: Load transactions
Storage --> Service: Return transactions array
Service --> Form: Return transactions
Form -> Form: Find transaction by ID
Form -> Form: Pre-populate form
Form -> Form: Change button to "Save Changes"

User -> Form: Modify form data
User -> Form: Click "Save Changes"
Form -> Form: Validate form
Form -> Service: update(id, transaction)
Service -> Service: Find transaction in array
Service -> Service: Update transaction data
Service -> Storage: Save to localStorage
Service --> Form: Return success
Form -> Router: navigate('/transactions')
@enduml
```

### Delete Transaction Flow

```plantuml
@startuml Delete Transaction Flow
!theme plain
skinparam backgroundColor #FEFEFE
skinparam sequenceParticipant underline

actor User
participant "TransactionList\nComponent" as List
participant "Transaction\nService" as Service
participant "LocalStorage" as Storage

User -> List: Click Delete button
List -> Service: delete(id)
Service -> Service: Find transaction by ID
Service -> Service: Remove from array
Service -> Storage: Save updated array
Service --> List: Return success
List -> List: Refresh transactions list
@enduml
```

## Class Diagrams

### Transaction Model

```plantuml
@startuml Transaction Model
!theme plain
skinparam backgroundColor #FEFEFE
skinparam class {
    BackgroundColor #F0F8FF
    BorderColor #4682B4
}

class Transaction {
    +id: number
    +date: Date
    +description: string
    +amount: number
    +type: 'income' | 'expense'
}

note right
    Represents a financial transaction
    Used throughout the application
    Stored as JSON in localStorage
end note
@enduml
```

### TransactionService Class

```plantuml
@startuml TransactionService Class
!theme plain
skinparam backgroundColor #FEFEFE
skinparam class {
    BackgroundColor #F0F8FF
    BorderColor #4682B4
}

class TransactionService {
    -transactions: Transaction[]
    -STORAGE_KEY: string

    +getAll(): Transaction[]
    +add(transaction: Omit<Transaction, 'id'>): Transaction
    +update(id: number, transaction: Omit<Transaction, 'id'>): boolean
    +delete(id: number): boolean
    -load(): void
    -save(): void
}

note right
    Singleton service for transaction management
    Handles all CRUD operations
    Persists data to localStorage
end note
@enduml
```

### Component Classes

```plantuml
@startuml Component Classes
!theme plain
skinparam backgroundColor #FEFEFE
skinparam class {
    BackgroundColor #F0F8FF
    BorderColor #4682B4
}

class TransactionList {
    +displayedColumns: string[]
    +transactions: Transaction[]

    +ngOnInit(): void
    +loadTransactions(): void
    +editTransaction(id: number): void
    +deleteTransaction(id: number): void
}

class AddTransaction {
    +transactionForm: FormGroup
    +isEditing: boolean
    +editingId: number | null

    +ngOnInit(): void
    +onSubmit(): void
}

TransactionList --> TransactionService : uses
AddTransaction --> TransactionService : uses
AddTransaction --> Router : uses
TransactionList --> Router : uses
@enduml
```

## User Interface Diagrams

### Main Transaction List

```plantuml
@startuml Transaction List UI
!theme plain
skinparam backgroundColor #FEFEFE

title Transaction List Screen

rectangle "Financial Tracker" as header #LightBlue

rectangle "Material Table" as table {
    rectangle "Date | Description | Amount | Type | Actions" as headers #LightGray
    rectangle "2024-01-15 | Salary | +5000000 | income | [Edit] [Delete]" as row1
    rectangle "2024-01-14 | Groceries | -150000 | expense | [Edit] [Delete]" as row2
    rectangle "..." as more
}

rectangle "Add Transaction Button" as addBtn #LightGreen

header --> table
table --> addBtn
@enduml
```

### Add/Edit Transaction Form

```plantuml
@startuml Transaction Form UI
!theme plain
skinparam backgroundColor #FEFEFE

title Add/Edit Transaction Form

rectangle "Transaction Form" as form #LightBlue

rectangle "Date Field (Date Picker)" as date
rectangle "Description Field (Text Input)" as desc
rectangle "Amount Field (Number Input)" as amount
rectangle "Type Field (Income/Expense Select)" as type

rectangle "Submit Button" as submit
note right of submit
    "Add Transaction" or "Save Changes"
    based on edit mode
end note

form --> date
form --> desc
form --> amount
form --> type
form --> submit
@enduml
```

## Database Schema

### LocalStorage Structure

```plantuml
@startuml LocalStorage Schema
!theme plain
skinparam backgroundColor #FEFEFE

storage "localStorage" as ls {
    folder "ft_transactions_v1" as key {
        card "JSON Array of Transactions" as data
    }
}

card "Transaction Object" as tx {
    rectangle "id: number" as id
    rectangle "date: string (ISO)" as date
    rectangle "description: string" as desc
    rectangle "amount: number" as amt
    rectangle "type: 'income'|'expense'" as type
}

data --> tx : contains
@enduml
```

## Routing Diagram

```plantuml
@startuml Routing Diagram
!theme plain
skinparam backgroundColor #FEFEFE

rectangle "Routes" as routes #LightBlue

rectangle "/" as root
rectangle "/transactions" as list
rectangle "/add" as add
rectangle "/edit/:id" as edit

root --> list : redirect
list --> add : "Add Transaction"
list --> edit : "Edit Transaction"
add --> list : "Submit"
edit --> list : "Save Changes"

note right of edit
    :id parameter contains
    transaction ID to edit
end note
@enduml
```

## Technology Stack

```plantuml
@startuml Technology Stack
!theme plain
skinparam backgroundColor #FEFEFE

package "Frontend" {
    [Angular 17+] as Angular
    [TypeScript] as TS
    [Angular Material] as Material
    [SCSS] as Styles
}

package "Build Tools" {
    [Angular CLI] as CLI
    [npm] as NPM
}

package "Runtime" {
    [Browser APIs] as Browser
    [LocalStorage] as Storage
}

Angular --> TS : uses
Angular --> Material : uses
Angular --> Styles : uses
Angular --> CLI : built with
CLI --> NPM : uses
Angular --> Browser : runs in
Angular --> Storage : persists to
@enduml
```

## Development Workflow

```plantuml
@startuml Development Workflow
!theme plain
skinparam backgroundColor #FEFEFE

start
:Code Changes;
:Run ng serve;
:Browser auto-reloads;
:Test functionality;
if (Works correctly?) then (yes)
    :Commit changes;
    :Run ng build;
    :Deploy to production;
    stop
else (no)
    :Debug issues;
    :Fix code;
    :Repeat;
endif
@enduml
```

## File Structure

```
financial_tracker/
├── src/
│   ├── app/
│   │   ├── app.config.ts
│   │   ├── app.routes.ts
│   │   ├── app.component.ts/html/css
│   │   ├── models/
│   │   │   └── transaction.ts
│   │   ├── services/
│   │   │   └── transaction.service.ts
│   │   ├── transaction-list/
│   │   │   ├── transaction-list.component.ts/html/css
│   │   └── add-transaction/
│   │       └── add-transaction.component.ts/html/css
│   ├── styles.css
│   └── main.ts
├── angular.json
├── package.json
├── tsconfig.json
└── docs/
    └── projectDoc.pdf
```

## Key Code Snippets

### Transaction Service Methods

```typescript
// Add new transaction
add(transaction: Omit<Transaction, 'id'>): Transaction {
  const nextId = this.transactions.reduce((m, t) => Math.max(m, t.id), 0) + 1;
  const newTx: Transaction = {
    id: nextId,
    ...transaction
  };
  this.transactions.push(newTx);
  this.save();
  return newTx;
}

// Update existing transaction
update(id: number, transaction: Omit<Transaction, 'id'>): boolean {
  const idx = this.transactions.findIndex(t => t.id === id);
  if (idx >= 0) {
    this.transactions[idx] = { id, ...transaction };
    this.save();
    return true;
  }
  return false;
}
```

### Component Interaction

```typescript
// Edit transaction navigation
editTransaction(id: number): void {
  this.router.navigate(['/edit', id]);
}

// Dynamic form submission
onSubmit(): void {
  if (this.transactionForm.valid) {
    const formValue = this.transactionForm.value;
    if (this.isEditing && this.editingId) {
      this.txService.update(this.editingId, formValue);
    } else {
      this.txService.add(formValue);
    }
    this.router.navigate(['/transactions']);
  }
}
```

## Future Enhancements

```plantuml
@startuml Future Enhancements
!theme plain
skinparam backgroundColor #FEFEFE

rectangle "Planned Features" as features #LightGreen

rectangle "Firebase Integration" as firebase
rectangle "User Authentication" as auth
rectangle "Data Visualization" as charts
rectangle "Export to CSV/PDF" as export
rectangle "Transaction Categories" as categories
rectangle "Search & Filtering" as search

features --> firebase
features --> auth
features --> charts
features --> export
features --> categories
features --> search
@enduml
```

## Testing Strategy

```plantuml
@startuml Testing Strategy
!theme plain
skinparam backgroundColor #FEFEFE

rectangle "Testing Pyramid" as pyramid

rectangle "Unit Tests" as unit {
    rectangle "Service methods"
    rectangle "Component logic"
    rectangle "Form validation"
}

rectangle "Integration Tests" as integration {
    rectangle "Component interaction"
    rectangle "Service-Storage integration"
    rectangle "Routing behavior"
}

rectangle "E2E Tests" as e2e {
    rectangle "User workflows"
    rectangle "Add/Edit/Delete flows"
    rectangle "Form validation"
}

pyramid --> unit
pyramid --> integration
pyramid --> e2e
@enduml
```

## Deployment Diagram

```plantuml
@startuml Deployment Diagram
!theme plain
skinparam backgroundColor #FEFEFE

node "Development" as dev {
    [Angular CLI] as cli
    [Local Browser] as browser
    [localStorage] as storage
}

node "Production" as prod {
    [Web Server] as server
    [CDN] as cdn
    [User Browser] as userBrowser
    [localStorage] as userStorage
}

cli --> browser : serves
browser --> storage : persists

server --> cdn : hosts
cdn --> userBrowser : delivers
userBrowser --> userStorage : persists
@enduml
```

---

**Document Version:** 2.0
**Last Updated:** January 24, 2026
**PlantUML Version:** Compatible with PlantUML 1.2023.0+
