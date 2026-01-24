# Financial Tracker - Updated Documentation

## Overview
The Financial Tracker is an Angular-based web application for managing personal income and expense transactions. It features a clean Material Design UI and now uses Firebase Firestore for cloud-based data persistence.

## Recent Updates (January 24, 2026)

### 1. Edit Feature Implementation
- Added edit functionality for existing transactions
- Edit button in the transaction list opens the add-transaction form pre-populated with existing data
- Submit button dynamically changes to "Save Changes" when editing

### 2. UI Improvements
- Action buttons (Edit/Delete) are now arranged horizontally with proper spacing
- Improved user experience with consistent button styling

### 3. Firebase Integration
- Replaced localStorage with Firebase Firestore for data persistence
- All CRUD operations now work with cloud database
- Data is synchronized across devices and persists permanently

## Architecture Diagram

```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   Angular App   │    │  Transaction    │    │   Firebase      │
│                 │    │   Service       │    │   Firestore     │
│ ┌─────────────┐ │    │                 │    │                 │
│ │Transaction  │ │◄──►│ ┌─────────────┐ │◄──►│ ┌─────────────┐ │
│ │List Component│ │    │ │CRUD Methods │ │    │ │Transactions │ │
│ └─────────────┘ │    │ └─────────────┘ │    │ │Collection    │ │
│                 │    │                 │    │ └─────────────┘ │
│ ┌─────────────┐ │    │                 │    │                 │
│ │Add/Edit     │ │    │                 │    │                 │
│ │Transaction  │ │    │                 │    │                 │
│ │Component    │ │    │                 │    │                 │
│ └─────────────┘ │    │                 │    │                 │
└─────────────────┘    └─────────────────┘    └─────────────────┘
```

## Component Architecture

```
App Component
├── Transaction List Component
│   ├── Material Table with Transactions
│   ├── Edit Button → Navigates to /edit/:id
│   └── Delete Button → Calls Service.delete()
│
├── Add Transaction Component (Reusable)
│   ├── Form Fields: Date, Description, Amount, Type
│   ├── Dynamic Button: "Add Transaction" / "Save Changes"
│   └── Navigation: Back to /transactions on success
│
└── Transaction Service
    ├── Firebase Firestore Integration
    ├── Async CRUD Operations
    └── Real-time Data Synchronization
```

## Data Flow

### Adding a Transaction:
1. User fills form in AddTransactionComponent
2. Component calls TransactionService.add()
3. Service adds document to Firestore 'transactions' collection
4. UI updates with new transaction
5. Navigation back to transaction list

### Editing a Transaction:
1. User clicks Edit button in TransactionListComponent
2. Navigation to /edit/:id route
3. AddTransactionComponent loads existing data
4. Form pre-populates with transaction details
5. Button shows "Save Changes"
6. On submit, calls TransactionService.update()
7. Firestore document updated
8. Navigation back to transaction list

### Deleting a Transaction:
1. User clicks Delete button
2. TransactionService.delete() called
3. Document removed from Firestore
4. Local array updated
5. UI refreshes automatically

## Firebase Configuration

```typescript
const firebaseConfig = {
  apiKey: "AIzaSyCIsGS5HSOzHAFGaaVnRq5Hv7rYn1CgIsk",
  authDomain: "moneylog-3c681.firebaseapp.com",
  projectId: "moneylog-3c681",
  storageBucket: "moneylog-3c681.firebasestorage.app",
  messagingSenderId: "400518685044",
  appId: "1:400518685044:web:4752699019ef2a108b8c21",
  measurementId: "G-8QHHX5J3ZZ"
};
```

## Transaction Model

```typescript
interface Transaction {
  id: string;           // Firestore document ID
  date: Date;           // Transaction date
  description: string;  // Transaction description
  amount: number;       // Transaction amount
  type: 'income' | 'expense'; // Transaction type
}
```

## Key Features

- ✅ Material Design UI components
- ✅ Responsive table layout
- ✅ Form validation
- ✅ Date picker integration
- ✅ Currency formatting (IDR)
- ✅ Cloud data persistence
- ✅ Real-time synchronization
- ✅ Edit existing transactions
- ✅ Delete transactions
- ✅ Horizontal action buttons
- ✅ Dynamic button labels

## Technologies Used

- **Frontend**: Angular 17+, TypeScript
- **UI Library**: Angular Material
- **Database**: Firebase Firestore
- **Build Tool**: Angular CLI
- **Styling**: SCSS

## Future Enhancements

- User authentication
- Transaction categories
- Filtering and search
- Data export functionality
- Charts and analytics
- Multi-device sync
- Offline support
