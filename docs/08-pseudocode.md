# 8. Abstraction to Implementation

This final step sketches how the models in this repository would become real code. It is intentionally pseudocode/skeleton-level - the goal of this repository is analysis and modeling, not a full implementation (a complete, runnable implementation of a related system, using the Factory/Singleton/Observer patterns, is in the companion repo: [library-management-system-ood](https://github.com/RamahB0/library-management-system-ood)).

## 8.1 Book (from the class and state diagrams)

```
class Book
    isbn: String
    title: String
    author: String
    copiesTotal: Integer
    copiesAvailable: Integer
    status: BookStatus  // AVAILABLE | ISSUED | RESERVED | LOST | WITHDRAWN

    function isAvailable() -> Boolean
        return status == AVAILABLE and copiesAvailable > 0

    function markIssued()
        assert isAvailable()
        copiesAvailable = copiesAvailable - 1
        if copiesAvailable == 0
            status = ISSUED

    function markReturned(hasPendingReservation: Boolean)
        copiesAvailable = copiesAvailable + 1
        status = RESERVED if hasPendingReservation else AVAILABLE
end class
```

## 8.2 Loan (from the class diagram and Loan state diagram)

```
class Loan
    loanId: String
    book: Book
    member: Member
    borrowDate: Date
    dueDate: Date
    returnDate: Date | null

    function isOverdue(today: Date) -> Boolean
        return returnDate == null and today > dueDate

    function calculateFine(today: Date, finePerDay: Money) -> Money
        if not isOverdue(today)
            return 0
        daysLate = today - dueDate
        return daysLate * finePerDay
end class
```

## 8.3 CirculationService.issueBook (from the Issue Book sequence diagram)

```
function issueBook(isbn: String, memberId: String) -> Result
    book = bookRepository.findByIsbn(isbn)
    if book is null
        return Error("book not found")

    if not book.isAvailable()
        return Error("book not available")

    member = memberRepository.findById(memberId)
    if member.borrowedBooks.size >= member.maxBooksAllowed
        return Error("borrowing limit reached")

    book.markIssued()
    loan = new Loan(book, member, today(), today() + LOAN_PERIOD_DAYS)
    loanRepository.save(loan)
    bookRepository.save(book)

    return Success(loan)
end function
```

## 8.4 CirculationService.returnBook (from the Return Book sequence diagram)

```
function returnBook(isbn: String) -> Result
    loan = loanRepository.findActiveLoanByIsbn(isbn)
    if loan is null
        return Error("no active loan for this book")

    fine = 0
    if loan.isOverdue(today())
        fine = loan.calculateFine(today(), policy.finePerDay)
        notificationService.notifyOverdue(loan.member, loan.book, fine)

    hasPendingReservation = reservationRepository.hasPendingReservation(isbn)
    loan.book.markReturned(hasPendingReservation)
    loan.returnDate = today()

    loanRepository.save(loan)
    bookRepository.save(loan.book)

    return Success(fine)
end function
```

## 8.5 Traceability

Every snippet above maps directly back to an earlier artifact: `Book` and its transitions come from [06-state-diagram.md](06-state-diagram.md), the two service functions follow the message order from [05-sequence-diagrams.md](05-sequence-diagrams.md) step by step, and every attribute used matches a field on the corresponding class in [03-class-diagram.md](03-class-diagram.md). This traceability - requirement to model to code - is the point of doing OOA before writing implementation code.
