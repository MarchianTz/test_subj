# Financial Tracker - Project Report

## 1. System Description

**Financial Tracker** is an Angular-based web application designed to help users manage personal financial transactions. The system provides a clean, intuitive interface for recording income and expense transactions with automatic persistence to browser storage.

### Key Features
- Add transactions with date, description, amount, and type (income/expense)
- View all transactions in a sortable table format
- Delete transactions with confirmation
- Persistent storage (transactions saved automatically to localStorage)
- Currency display in Indonesian Rupiah (IDR)
- Responsive Material Design interface

### Technology Stack
- **Framework**: Angular (v20.3.8) with standalone components
- **UI Library**: Angular Material
- **Language**: TypeScript
- **Storage**: Browser localStorage
- **Localization**: Indonesian locale

---

## 2. Component Structure

### Application Architecture

```
Financial Tracker Application
├── AppComponent (src/app/app.ts)
│   ├── Toolbar (Navigation & Branding)
│   └── Router Outlet
│       ├── TransactionList Component
│       └── AddTransaction Component
├── TransactionService (src/app/services/transaction.service.ts)
│   └── localStorage Management (Key: ft_transactions_v1)
└── Transaction Model (src/app/models/transaction.ts)
```

### Core Components

#### **TransactionList Component** (`src/app/transaction-list/`)
- **Purpose**: Display all transactions in a Material table
- **Features**:
  - Shows transaction date, description, amount, and type
  - Currency formatted as IDR
  - Delete button for each transaction
  - Sorted by date (newest first)
- **Methods**:
  - `ngOnInit()`: Loads transactions on component initialization
  - `loadTransactions()`: Fetches all transactions from service
  - `deleteTransaction(id)`: Removes a transaction

#### **AddTransaction Component** (`src/app/add-transaction/`)
- **Purpose**: Form for creating new transactions
- **Features**:
  - Reactive form with validation
  - Date picker input
  - Description text field
  - Amount input with currency
  - Type dropdown (Income/Expense)
  - Submit button
- **Methods**:
  - `onSubmit()`: Adds transaction and navigates back to list

#### **TransactionService** (`src/app/services/transaction.service.ts`)
- **Purpose**: Handle CRUD operations and persistence
- **Methods**:
  - `load()`: Fetch transactions from localStorage
  - `save()`: Persist transactions to localStorage
  - `getAll()`: Return all transactions
  - `add(transaction)`: Create new transaction
  - `delete(id)`: Remove transaction
- **Storage**: localStorage key `ft_transactions_v1`

#### **AppComponent** (`src/app/app.ts`)
- **Purpose**: Main application container
- **Features**:
  - Material toolbar with navigation buttons
  - Router outlet for component switching
  - Links to Add Transaction and View Transactions

---

## 3. How to Run the Application

### Prerequisites
```
Node.js 18.x or higher
npm 9.x or higher
Angular CLI 20.3.8
```

### Installation Steps

1. **Clone the repository**
   ```bash
   git clone https://github.com/MarchianTz/financial_tracker.git
   cd financial_tracker
   ```

2. **Install dependencies**
   ```bash
   npm install
   ```

3. **Start the development server**
   ```bash
   npm start
   ```
   Or alternatively:
   ```bash
   ng serve
   ```

4. **Open in browser**
   - Navigate to `http://localhost:4200/`
   - The app loads automatically and hot-reloads on file changes

### Application Navigation
- **Home/Transactions**: View all saved transactions
- **Add Transaction**: Navigate to form to add new transaction
- **Delete**: Click delete icon on any transaction to remove it

---

## 4. Proof of Running Results (Screenshots)

### Screenshot 1: Transactions List View
![Transactions List](screenshots/transactions-list.png)
- Material table displaying all transactions
- Each row shows: Date, Description, Amount (IDR), Type
- Delete icon button on each row
- Sorted by date (newest first)

### Screenshot 2: Add Transaction Form
![Add Transaction Form](screenshots/add-transaction-form.png)
- Form fields: Date Picker, Description, Amount, Type (dropdown)
- Submit button to save transaction
- Form validation prevents empty fields
- Cancel/Back navigation option

### Screenshot 3: Application Toolbar
![App Toolbar](screenshots/app-toolbar.png)
- Header with "Financial Tracker" branding
- Navigation buttons: "View Transactions" and "Add Transaction"
- Material Design styling with responsive layout

### Screenshot 4: Transaction Persistence
![Transaction Data Persisted](screenshots/transaction-persisted.png)
- Demonstrates data persisting after page refresh
- Shows transactions stored in browser localStorage
- Example transactions in IDR currency format

---

## 5. Key Technical Details

### Data Model
```typescript
interface Transaction {
  id: number;
  date: string;
  description: string;
  amount: number;
  type: 'income' | 'expense';
}
```

### Routing Configuration (`src/app/app.routes.ts`)
```
/transactions → TransactionList Component
/add → AddTransaction Component
(default) → redirects to /transactions
```

### Locale Configuration
Indonesian locale registered in `src/app/app.ts` for proper IDR currency formatting.

---

## 6. Build & Deployment

### Build for Production
```bash
ng build
```
- Outputs optimized build to `dist/` directory
- Ready for deployment to static hosting

### Run Tests
```bash
npm test
```
- Executes unit tests using Karma

---

## Conclusion

The Financial Tracker application successfully delivers a functional, user-friendly solution for personal finance management. The implementation demonstrates:
- ✅ Clean component-based architecture
- ✅ Persistent data storage with localStorage
- ✅ Material Design UI principles
- ✅ TypeScript type safety
- ✅ Responsive and intuitive user interface
- ✅ Indonesian locale and IDR currency support

The application is production-ready and can be extended with additional features such as transaction editing, CSV export, and advanced filtering.

---

**Generated**: November 15, 2025  
**Project**: Financial Tracker  
**Version**: 1.0
