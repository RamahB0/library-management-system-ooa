# 1. Requirement Analysis

This document identifies the people and external systems that interact with the Library Management System (the actors) and the key things they need to be able to do (the use cases). It is the starting point for everything else in this repository: the architecture, class diagram, and behavioral models all trace back to the use cases listed here.

## 1.1 Actors

- **Member** - a registered library patron who searches the catalog and borrows/returns books. Represents students, teachers, or the general public depending on the deployment.
- **Librarian** - front-desk staff who manage the day-to-day circulation of books: registering members, issuing and returning books, and managing the catalog.
- **Administrator** - manages librarian accounts, configures system-wide policies (loan duration, fine rates, borrowing limits), and reviews reports. A superset role that oversees the Librarian.
- **Notification System** (secondary/supporting actor) - an external or internal service the system invokes to email/SMS members about due dates and overdue fines. Modeled as an actor because, from the system's point of view, it is invoked and responds independently of the main flow.

## 1.2 Use Cases by Actor

### Member

- **Search Catalog** - search for books by title, author, ISBN, or subject.
- **View Borrowed Books** - see currently borrowed titles and their due dates.
- **Reserve Book** - place a hold on a book that is currently issued to someone else.
- **Renew Loan** - extend the due date of a currently borrowed book, if no one else has reserved it.
- **Pay Fine** - settle an outstanding overdue fine.

### Librarian

- **Register Member** - create a new member account.
- **Issue Book** - lend an available book to a member, creating a loan record and a due date.
- **Return Book** - close out a loan record when a member brings a book back, calculating any overdue fine.
- **Manage Catalog** - add, update, or remove book records (title, author, ISBN, number of copies).
- **Track Overdue Loans** - review loans that are past their due date.
- **Collect Fine** - record payment of a fine at the desk on a member's behalf.

### Administrator

- **Manage Librarian Accounts** - create, disable, or update librarian accounts.
- **Configure Loan Policies** - set loan duration, renewal limits, and fine-per-day rates.
- **Generate Reports** - view aggregate statistics such as most-borrowed titles, overdue counts, and fines collected.

## 1.3 Use Case Summary Table

| Use Case | Primary Actor | Preconditions | Postconditions |
|---|---|---|---|
| Search Catalog | Member | none | matching book list returned |
| Issue Book | Librarian | book is Available; member is under borrowing limit | book becomes Issued; loan record created |
| Return Book | Librarian | book is Issued | book becomes Available (or Reserved if a hold exists); loan closed; fine calculated if overdue |
| Reserve Book | Member | book is Issued (not Available) | reservation queued for member |
| Renew Loan | Member | loan is active; no pending reservation on the book | due date extended |
| Pay Fine | Member / Librarian | outstanding fine > 0 | fine marked paid |
| Manage Catalog | Librarian | none | book record added/updated/removed |
| Configure Loan Policies | Administrator | none | policy values updated system-wide |

These use cases are elaborated with diagrams in the later documents: two of them (**Issue Book** and **Return Book**) are detailed further as sequence diagrams in [05-sequence-diagrams.md](05-sequence-diagrams.md), since they represent the core circulation workflow of the system.
