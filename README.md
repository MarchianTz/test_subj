# Sequence Diagram → Angular Mapping

This table maps the participants/objects shown in the sequence diagrams to the concrete Angular artifacts in this project: components, templates, services, methods, and routes.

| Sequence Object | Angular Element | File / Location | Key Methods / Bindings | Route / Template |
|---|---:|---|---|---|
| User (actor) | External actor (browser user) | n/a | Interacts via clicks, form submit, navigation | n/a |
| AddTransaction (View) | Component (Controller for add form) | `src/app/add-transaction/add-transaction.ts` | `onSubmit()` — validates form, calls `TransactionService.add(...)` | Template: `src/app/add-transaction/add-transaction.html` — form inputs (date, description, amount, type); Route: `/add` |
| AddTransaction (UI) | Reactive Form | within `AddTransaction` component | `transactionForm: FormGroup` — validators: required fields, amount >= 0 | Form controls bound in template via `formControlName` |
| TransactionList (View / Controller) | Component (Controller for list) | `src/app/transaction-list/transaction-list.ts` | `ngOnInit()` → `loadTransactions()` → calls `TransactionService.getAll()`; `deleteTransaction(id)` → calls `TransactionService.delete(id)` | Template: `src/app/transaction-list/transaction-list.html`; Route: `/transactions` |
| TransactionService (Model) | Injectable Service (single source of truth) | `src/app/services/transaction.service.ts` | `getAll(): Transaction[]` — returns sorted array; `add(tx: Omit<Transaction,'id'>): Transaction` — generates id, pushes and saves; `delete(id: number): boolean` — removes and saves | Persists to localStorage in `save()` / `load()` methods |
| LocalStorage (persistence) | Browser `localStorage` API | not in repo (browser API) | `localStorage.getItem('ft_transactions_v1')` and `localStorage.setItem('ft_transactions_v1', json)` used by `TransactionService` | Storage key: `ft_transactions_v1` |
| Router | Angular Router | `src/app/app.routes.ts` | Routes wired to components: `{ path: 'transactions', component: TransactionList }`, `{ path: 'add', component: AddTransaction }` | Used by components to `navigate(['/transactions'])` after add |

## Message → Implementation Mapping (from sequence diagrams)

- `submit(form)` (User → AddTransaction UI)
  - Implemented as user clicking the form submit button bound to `onSubmit()` in `AddTransaction` component.
  - Template: submit button calls `(ngSubmit)="onSubmit()"`.

- `onSubmit()` → `TransactionService.add(tx)`
  - `AddTransaction.onSubmit()` validates and packages form values and calls `TransactionService.add(...)`.
  - Code: `this.txService.add({ date, description, amount, type });`

- `getAll()` (TransactionList → TransactionService)
  - `TransactionList.loadTransactions()` calls `this.txService.getAll()` to fetch current transactions.

- `delete(id)` (TransactionList → TransactionService)
  - `TransactionList.deleteTransaction(id)` calls `this.txService.delete(id)` and reloads list on success.

- `localStorage.setItem(...)` / `getItem(...)`
  - Implemented inside `TransactionService.save()` and `TransactionService.load()`.

## Files of interest

- Components
  - `src/app/transaction-list/transaction-list.ts` (+ template and css)
  - `src/app/add-transaction/add-transaction.ts` (+ template and css)

- Service
  - `src/app/services/transaction.service.ts`

- Model
  - `src/app/models/transaction.ts` (interface definition)

- Routing
  - `src/app/app.routes.ts`

## Quick developer notes

- The components act as controllers (handling UI events and calling the service). The service encapsulates persistence and business logic (ID generation, sorting).
- If you add more sequence participants (e.g., Bank API), implement a new service (e.g., `BankService`) and add appropriate async calls from components/services.
- Consider adding confirmation/undo patterns on delete in the `TransactionList` component for better UX.

