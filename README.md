# Library Management System - Object-Oriented Analysis (OOA)

This repository is an analysis-and-design deliverable (not a full implementation) for a simplified Library Management System. It works through requirement analysis, architectural planning, Object-Oriented Analysis, and data/functional/behavioral modeling, then shows how the resulting design could translate into code.

Per the assignment scope, the goal is modeling and analysis - a companion implementation of a related library system (with working code and design patterns) lives at [library-management-system-ood](https://github.com/RamahB0/library-management-system-ood).

## Contents

- [Requirement Analysis](docs/01-requirement-analysis.md) - actors and use cases
- [System Architecture](docs/02-architecture.md) - layered components and their interactions
- [Class Diagram](docs/03-class-diagram.md) - OOA classes, attributes, methods, relationships
- [Use Case Diagrams](docs/04-use-case-diagrams.md) - actor/use-case relationships for the system
- [Sequence Diagrams](docs/05-sequence-diagrams.md) - Issue Book and Return Book flows
- [State Diagram](docs/06-state-diagram.md) - Book lifecycle
- [Data, Functional & Behavioral Models](docs/07-data-functional-behavioral-models.md)
- [Abstraction to Implementation](docs/08-pseudocode.md) - pseudocode/code snippets derived from the models

## How to read this repository

Each document in `docs/` corresponds to one step of the OOA process, in the order a real analysis would typically proceed: start from requirements, move to architecture, then to detailed object models (structure, then interaction, then behavior over time), then to the underlying data/functional/behavioral views, and finally sketch how those models would become code.
