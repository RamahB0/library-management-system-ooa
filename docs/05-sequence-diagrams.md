# 5. Sequence Diagrams

Per the assignment, this document details two use cases as sequence diagrams: **Issue Book** and **Return Book**. These were chosen because together they form the core circulation workflow that every other use case (reservations, fines, overdue tracking) builds on.

## 5.1 Issue Book

```mermaid
sequenceDiagram
    actor Librarian
    participant UI as Librarian Desk UI
    participant Circ as CirculationService
    participant BookRepo as BookRepository
    participant Book
    participant LoanRepo as LoanRepository

    Librarian->>UI: scan member card + book barcode
    UI->>Circ: issueBook(isbn, memberId)
    Circ->>BookRepo: findByIsbn(isbn)
    BookRepo-->>Circ: book
    Circ->>Book: isAvailable()
    Book-->>Circ: true
    Circ->>Circ: checkMemberBorrowingLimit(memberId)
    alt limit not exceeded
        Circ->>Book: markIssued()
        Circ->>LoanRepo: save(new Loan(book, member, dueDate))
        LoanRepo-->>Circ: loan
        Circ-->>UI: success(loan)
        UI-->>Librarian: display due date
    else limit exceeded
        Circ-->>UI: error("borrowing limit reached")
        UI-->>Librarian: display error
    end
```

## 5.2 Return Book

```mermaid
sequenceDiagram
    actor Member
    actor Librarian
    participant UI as Librarian Desk UI
    participant Circ as CirculationService
    participant LoanRepo as LoanRepository
    participant Loan
    participant Book
    participant Notify as NotificationService

    Member->>Librarian: hand back book
    Librarian->>UI: scan book barcode
    UI->>Circ: returnBook(isbn)
    Circ->>LoanRepo: findActiveLoanByIsbn(isbn)
    LoanRepo-->>Circ: loan
    Circ->>Loan: isOverdue()
    alt overdue
        Circ->>Loan: calculateFine()
        Loan-->>Circ: fineAmount
        Circ->>Notify: notifyOverdue(member, book, fineAmount)
        Notify-->>Member: overdue/fine notification
    else on time
        Circ->>Circ: no fine due
    end
    Circ->>Book: markReturned()
    Circ->>LoanRepo: closeLoan(loan, returnDate)
    Circ-->>UI: success
    UI-->>Librarian: display confirmation
```

## 5.3 Notes

Both diagrams show `CirculationService` as the orchestrator: it is the only object that talks to `BookRepository`, `LoanRepository`, and `NotificationService` directly, which matches the layered architecture in [02-architecture.md](02-architecture.md) and keeps `Book`/`Loan` focused purely on their own state and calculations rather than coordination logic.
