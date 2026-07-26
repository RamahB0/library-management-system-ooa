# 4. Use Case Diagrams

Mermaid (the diagramming syntax GitHub renders inline) does not provide a dedicated UML use-case diagram type, so the diagrams below use its flowchart notation to represent the same information: actors as round nodes outside a system boundary, use cases as stadium-shaped nodes inside it, and plain arrows for "actor performs use case" associations. Dotted arrows are used for `include`/`extend` relationships between use cases, per standard UML convention.

## 4.1 System-Wide Use Case Diagram

```mermaid
flowchart LR
    Member(("Member"))
    Librarian(("Librarian"))
    Administrator(("Administrator"))

    subgraph LMS["Library Management System"]
        UC1(["Search Catalog"])
        UC2(["View Borrowed Books"])
        UC3(["Reserve Book"])
        UC4(["Renew Loan"])
        UC5(["Pay Fine"])
        UC6(["Register Member"])
        UC7(["Issue Book"])
        UC8(["Return Book"])
        UC9(["Manage Catalog"])
        UC10(["Track Overdue Loans"])
        UC11(["Collect Fine"])
        UC12(["Manage Librarian Accounts"])
        UC13(["Configure Loan Policies"])
        UC14(["Generate Reports"])
    end

    Member --> UC1
    Member --> UC2
    Member --> UC3
    Member --> UC4
    Member --> UC5

    Librarian --> UC6
    Librarian --> UC7
    Librarian --> UC8
    Librarian --> UC9
    Librarian --> UC10
    Librarian --> UC11

    Administrator --> UC12
    Administrator --> UC13
    Administrator --> UC14
```

## 4.2 Focused Use Case Diagram - Core Circulation

This second diagram zooms into the two use cases that are elaborated further as sequence diagrams in [05-sequence-diagrams.md](05-sequence-diagrams.md): **Issue Book** and **Return Book**. It shows their `include`/`extend` relationships with supporting use cases.

```mermaid
flowchart LR
    Librarian2(("Librarian"))

    subgraph Core["Core Circulation"]
        Issue(["Issue Book"])
        Return(["Return Book"])
        CheckAvail(["Check Availability"])
        CalcFine(["Calculate Fine"])
        Notify(["Notify Member"])
    end

    Librarian2 --> Issue
    Librarian2 --> Return
    Issue -. include .-> CheckAvail
    Return -. include .-> CalcFine
    Return -. extend .-> Notify
```

## 4.3 Notes

- **include** means the base use case always triggers the included one (issuing a book always includes checking availability first).
- **extend** means the extending use case only runs conditionally (a member is only notified if the returned book was overdue, i.e. `CalcFine` produced a non-zero fine).
