<!--
Sync Impact Report
Version change: uninitialized scaffold -> 1.0.0
Modified principles: None; this is the initial project constitution.
Added sections: Customer Requirements and Traceability; Object-Oriented Design;
  Separation of Presentation and Business Logic; Exact Monetary Arithmetic;
  Automated Verification and Human Review; Technical Implementation Decisions;
  Development Workflow and Quality Gates.
Removed sections: None.
-->

# SplitSmart Constitution

## Core Principles

### I. Customer Requirements and Traceability
The customer request recorded in `UserRequirement.md` is the authoritative source
for customer needs, including accounts, groups, expenses, notifications,
approvals, balances, payments, reporting, and web, Android, and iOS support.
Every customer requirement MUST be traceable through design and implementation
to verification. Implementation choices MUST be identified as such and MUST NOT
be described as customer requirements. A choice that narrows or changes
customer-requested behavior or platform support requires customer approval.

### II. Object-Oriented Design
Java code MUST use cohesive classes with clear responsibilities and encapsulated
state and business invariants. Domain behavior MUST be represented in domain
objects or focused services rather than duplicated across callers. Abstractions
and inheritance MUST have a concrete design purpose; unnecessary indirection
MUST be avoided.

### III. Separation of Presentation and Business Logic
Java Swing components MUST handle presentation and user interaction, then
delegate work to application or domain logic. Expense splitting, approval
decisions, and balance calculations MUST NOT be implemented in Swing event
handlers or presentation classes. Business logic MUST be testable without
constructing or displaying the GUI.

### IV. Exact Monetary Arithmetic
All monetary amounts and monetary calculations MUST use `BigDecimal`; binary
floating-point types MUST NOT be used for money. Currency scale, rounding, and
percentage-split reconciliation MUST be explicit at defined calculation
boundaries and covered by tests.

### V. Automated Verification and Human Review
Automated tests MUST use JUnit 5 and verify business rules, customer
requirements, and relevant edge cases, including monetary rounding. PlantUML
diagrams MUST be used for material architecture and interaction designs and
kept consistent with the implementation. AI-generated code, tests, diagrams,
and documentation MUST receive human review for correctness, requirement
traceability, and consistency before being accepted.

## Technical Implementation Decisions
The project implementation uses Java 21 and Maven. Its graphical interface uses
Java Swing; automated tests use JUnit 5; and PlantUML is the diagram format.
These are project implementation decisions, not statements of customer
requirements. In particular, selecting Swing MUST NOT be treated as satisfying
or replacing the customer-requested web, Android, or iOS platforms. Any
implementation plan that changes or excludes those platforms requires customer
approval under Principle I.

## Development Workflow and Quality Gates
Before implementation, each in-scope customer requirement MUST be linked to
planned behavior and verification. Design and implementation changes MUST
preserve those links, and diagrams MUST be updated when the design they describe
changes. A change is complete only when its relevant JUnit 5 tests pass, its
requirement traceability is current, and its AI-assisted contributions (if any)
have been reviewed by a human. Reviews MUST identify any unmet customer
requirements rather than imply they are fulfilled by technical choices.

## Governance
This constitution governs project engineering decisions. Amendments MUST be
reviewed and approved by the project owner, explain their impact, and update
this document's version and last-amended date. An amendment that changes
customer scope also requires customer approval.

Constitution versions use MAJOR.MINOR.PATCH: increment MAJOR for backward-
incompatible principle or governance changes, MINOR for new principles or
materially expanded requirements, and PATCH for clarifications and other
non-semantic edits. Compliance MUST be checked during planning and change
review. A deviation requires documented rationale and project-owner approval;
it MUST NOT waive a customer requirement without customer approval.

**Version**: 1.0.0 | **Ratified**: 2026-10-29 | **Last Amended**: 2026-10-29
