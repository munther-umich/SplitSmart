# Feature Specification: SplitSmart Shared Expense Management

**Feature Branch**: Not created

**Created**: 2026-10-29

**Status**: Draft

**Input**: User description: "Use `UserRequirement.md` as the authoritative customer requirements; write a testable SplitSmart feature specification, preserve the original customer feature inventory, and label the Java Swing MVP subset separately."

## Clarifications

### Session 2026-10-29

- Q: Which sign-in and account-recovery method should SplitSmart require? (FR-001) → A: Verified email and password; reset through verified email.
- Q: Must the person who paid for an expense also be one of the people sharing it? (FR-003–FR-004) → A: No; the payer must be a group member but may be excluded from participants.
- Q: How should a person join a group, and what group records should they be able to see after removal? (FR-002) → A: An owner or admin invites and the person accepts; removed members retain read-only access only to expenses and payments they participated in.
- Q: What rules should SplitSmart apply to expense amounts and percentage shares? (FR-003–FR-004) → A: Require a positive amount; reject zero, negative, or malformed amounts; percentages must be 0–100% and total exactly 100%.
- Q: How should SplitSmart distribute leftover cents when an expense split produces fractions of a cent? (FR-004, NFR-001) → A: Use the largest fractional remainder first; break ties by participant selection order.
- Q: Through which channels should SplitSmart notify group members when another member creates an expense? (FR-006) → A: In-app notifications and email.
- Q: When a member records a settlement payment, should it reduce the balance immediately or only after the recipient confirms receiving it? (FR-008, FR-010) → A: Recording means the payment has been made; reduce the balance immediately.
- Q: What should SplitSmart do if a recorded payment is larger than the payer's current amount owed to the payee? (FR-010) → A: Reject amounts above the current amount owed.
- Q: Who should be allowed to approve an expense, and may the person who submitted it approve it themselves? (FR-007) → A: The group designates one or more approvers; the submitter cannot approve their own expense.
- Q: What is the intended role of the proposed Java Swing MVP relative to the customer-facing web and mobile product? (FR-013) → A: Use the listed Swing subset as an internal prototype; web, Android, and iOS remain separate customer-facing deliverables.

## User Scenarios & Testing *(mandatory)*

### Actors

- **Account holder**: A person who signs in to see their own expenses, groups, and balances.
- **Group member**: An account holder who participates in shared group expenses and payments.
- **Expense submitter**: A group member who records an expense and its participants.
- **Approver**: A group-designated member authorized to review submitted expenses; the submitter cannot approve their own expense.
- **Payer and payee**: Group members who record or receive a settlement payment.
- **Report viewer**: An authorized account holder who reviews group or personal summaries.

### User Story 1 - Record and approve a shared expense (Priority: P1)

As a group member, I want to record an expense, identify the people sharing it, and have it reviewed before it changes balances, so that the group can agree on what is owed.

**Why this priority**: Recording approved shared expenses is the core source of group balances.

**Independent Test**: Create a group with multiple members, submit an expense using each supported split method, approve it, and verify only the approved allocation changes balances.

**Acceptance Scenarios**:

1. **Given** a signed-in member of a group, **When** they submit an expense with a positive amount, date, description, a group-member payer, participants, and equal shares, **Then** the expense is saved as pending review and is not yet included in balances, even when the payer is not a participant.
2. **Given** an expense with valid percentage shares totaling 100%, **When** the submitter sends it for review, **Then** the expense records each participant's allocation and remains pending.
3. **Given** an expense awaiting review, **When** a group-designated approver other than the submitter approves it, **Then** it becomes approved and the affected pairwise balances reflect the expense exactly once.
4. **Given** an expense awaiting review, **When** an authorized approver rejects it, **Then** it does not affect balances and its status and rejection outcome are visible to the submitter.

---

### User Story 2 - Create an account and manage a group (Priority: P1)

As a person sharing expenses, I want my own account and groups with the right members, so that expense and balance information is available to the appropriate people.

**Why this priority**: Accounts and group membership establish ownership and access boundaries for all other journeys.

**Independent Test**: Create and sign in to two accounts, create a group with one account, add the other, and verify each sees only authorized group information.

**Acceptance Scenarios**:

1. **Given** a person without an account, **When** they register with a unique email, verify it, and sign in with their password, **Then** they can access their own account.
2. **Given** a signed-in group owner or admin, **When** they invite another account and that person accepts, **Then** both active members can see the group's authorized shared information.
3. **Given** a user who is not a member of a group, **When** they attempt to access that group's expenses or balances, **Then** access is denied.
4. **Given** a member removed from a group, **When** they view group history, **Then** they have read-only access only to expenses and payments in which they participated.

---

### User Story 3 - Settle an amount owed (Priority: P2)

As a group member who owes or is owed money, I want to record a payment between members, so that balances reflect settlements as well as approved expenses.

**Why this priority**: A shared-expense ledger is useful only if members can record repayments and see the resulting amount owed.

**Independent Test**: Approve an expense that creates an amount owed, record a full or partial payment between the affected members, and verify the resulting balances.

**Acceptance Scenarios**:

1. **Given** one member owes another, **When** the payer records a completed payment with payee, amount, and date, **Then** the payment is recorded as completed and the pairwise balance is reduced immediately without payee confirmation.
2. **Given** a payment amount greater than zero, **When** it is recorded, **Then** the payment appears in the relevant member and group history.

---

### User Story 4 - Review expense, balance, and payment summaries (Priority: P2)

As an authorized member, I want to review expense, balance, and payment summaries, so that I can understand group activity and investigate a chosen period.

**Why this priority**: Summaries make the ledger useful for reconciliation and decision-making.

**Independent Test**: Create approved expenses and payments over multiple dates, then compare an unfiltered summary and a chosen date-range report with the underlying records.

**Acceptance Scenarios**:

1. **Given** an authorized member and recorded group activity, **When** they view a report, **Then** it summarizes expenses, current balances, and payments for the records they can access.
2. **Given** an authorized member and a valid date range, **When** they request a custom report, **Then** the report includes only matching records and identifies the selected range.
3. **Given** an unauthorized user, **When** they request another group's report, **Then** the request is denied.

---

### User Story 5 - Learn about a group expense (Priority: P3)

As a group member, I want to be notified when another member creates an expense, so that I can review new activity promptly.

**Why this priority**: Notifications improve timeliness but do not replace the expense review and balance controls.

**Independent Test**: Create an expense as one group member and verify that the other group members receive an expense-created notification that identifies its review state.

**Acceptance Scenarios**:

1. **Given** a group with multiple members, **When** one member creates an expense, **Then** every other active group member receives both an in-app notification and an email that identify the expense and whether it is awaiting review or approved.
2. **Given** an expense is rejected, **When** a member views its notification or linked activity, **Then** the outcome is not presented as an approved balance change.

### Edge Cases

- A zero, negative, or malformed amount is rejected without creating an expense or changing a balance.
- A percentage split that does not total 100% is rejected with a clear explanation.
- Each participant percentage must be between 0% and 100%, inclusive; the percentages must total exactly 100%, with no automatic normalization.
- A split involving a participant who is not a member of the expense's group is rejected.
- Equal shares or percentage allocations that produce fractional currency units are reconciled to the expense total by assigning leftover cents to participants with the largest fractional remainders; ties follow participant selection order.
- A pending or rejected expense does not change balances. Re-approving an already approved expense does not apply it twice.
- A payment cannot have a zero or negative amount or exceed the payer's current amount owed to the payee; an overpayment is rejected without changing the balance.
- A report with an invalid date range is rejected or corrected before results are shown.
- A missing or unreadable optional receipt does not prevent saving an otherwise valid expense.
- A removed group member cannot access records they did not participate in and cannot make changes to any group record.

## Requirements *(mandatory)*

### Customer Requirements Inventory

The following inventory preserves the features in `UserRequirement.md`. These requirements describe customer scope, not an implementation milestone.

- **Accounts (FR-001)**: Individual accounts allow people to sign in and access their own expense and balance information.
- **Groups (FR-002)**: Users create and manage groups for shared activities and track group expenses and balances.
- **Expenses (FR-003–FR-005)**: Users enter an amount, date, description, equal or percentage split, optional receipt image, and participating users.
- **Notifications (FR-006)**: Members are notified when another group member creates an expense.
- **Approvals (FR-007)**: Expenses are reviewed and approved before entering the ledger or changing balances; groups may need one or multiple approval levels.
- **Balances (FR-008–FR-009)**: The system tracks what each user owes every other user, based on entered expenses.
- **Payments (FR-008, FR-010)**: Users record payments that settle amounts owed, and balances reflect them.
- **Reports (FR-011–FR-012)**: Users view expense, balance, and payment summaries and can generate custom reports, including date ranges and other parameters.
- **Platforms (FR-013)**: The primary product is web-based and also has apps that run on Android and iOS.

### Functional Requirements

- **FR-001 (Accounts)**: The system MUST allow a person to create an individual account using a unique verified email and password, sign in with those credentials, and reset a forgotten password through the verified email. **Acceptance**: An account holder can sign in only after email verification, can reset a forgotten password through that email, and can view their own records; another account cannot access those records without authorization.
- **FR-002 (Groups)**: The system MUST allow users to create groups and allow group owners or admins to invite accounts; an invitee must accept before becoming an active member. **Acceptance**: An accepted invite grants group-member access; a pending invite and a nonmember grant no group access; a removed member has read-only access only to expenses and payments in which they participated.
- **FR-003 (Expense details)**: The system MUST allow an authorized group member to submit an expense with a positive amount, date, description, and payer selected from the group. The submitter and payer may differ. **Acceptance**: A valid submission records the specified group-member payer and required details; zero, negative, malformed, missing, or otherwise invalid required values prevent submission.
- **FR-004 (Participants and splits)**: The system MUST let the submitter select the group members who share an expense and allocate it equally or by participant percentages. The payer need not be a participant. **Acceptance**: Only group members can participate; equal allocations total the expense amount; percentages are each between 0% and 100%, inclusive, total exactly 100%, and are not automatically normalized. Any leftover cents go to participants in descending order of fractional remainder, with ties broken by participant selection order.
- **FR-005 (Receipt)**: The system MUST allow an optional receipt image to be attached to an expense. **Acceptance**: An expense can be submitted without an image, and an attached image is associated with that expense and visible only to authorized users.
- **FR-006 (Notifications)**: The system MUST notify every other active group member through both in-app notification and email when an expense is created, and distinguish its review status. **Acceptance**: Each other active member receives an in-app notification and an email for a newly created expense; both identify its pending, approved, or rejected state and do not represent a pending or rejected expense as an approved balance change.
- **FR-007 (Approval workflow)**: The system MUST support group-designated approvers and a group-configurable single- or multiple-level review process; the submitter MUST NOT approve their own expense. **Acceptance**: An expense progresses through the configured review levels and is approved only by designated approvers other than its submitter; rejected or pending expenses do not affect balances; each approval records its outcome and approver.
- **FR-008 (Balance updates)**: The system MUST update balances from approved expenses and completed payments only. **Acceptance**: A pending or rejected expense has no balance effect; approval applies its allocation once; recording a completed payment immediately reduces the amount owed by the payment amount without requiring payee confirmation.
- **FR-009 (Pairwise balance tracking)**: The system MUST show the amount owed between every pair of members in a group, accounting for approved expenses and payments. **Acceptance**: For a known set of ledger entries, each displayed pairwise balance equals the net amount owed, including zero when no amount is owed, and the total allocated amount for each expense equals its recorded amount.
- **FR-010 (Payment tracking)**: The system MUST let a payer record a completed settlement payment with payee, amount, and date and show it in the relevant history. **Acceptance**: A valid full or partial payment no greater than the payer's current amount owed is recorded as completed once and immediately updates the related pairwise balance without payee confirmation; zero, negative, malformed, or excessive amounts are rejected without changing the balance.
- **FR-011 (Reports and summaries)**: The system MUST provide authorized users with summaries of expenses, balances, and payments. **Acceptance**: The summary agrees with the underlying authorized ledger records.
- **FR-012 (Custom reports)**: The system MUST let authorized users generate reports for a specified date range and other supported parameters. **Acceptance**: A report contains only records matching the chosen criteria, which are visible in the report.
- **FR-013 (Customer platforms)**: The customer-facing product MUST be primarily web-based and provide apps that run on Android and iOS. **Acceptance**: On each platform, an authorized member can sign in, open a group, and access its expenses and balances; the web product is designated as the primary experience.

### Non-Functional Requirements

- **NFR-001 (Monetary integrity)**: Monetary calculations MUST preserve the recorded expense and payment values, and allocations MUST reconcile to the expense total without silent loss or creation of value.
- **NFR-002 (Privacy and access control)**: The system MUST restrict expense, receipt, balance, payment, notification, and report access to authorized active group members; a removed member may read only expense and payment records in which they participated and MUST NOT edit group data.
- **NFR-003 (User-perceived responsiveness)**: Under the agreed launch workload, at least 95% of ordinary account, group, ledger, and report actions MUST show a result or clear progress feedback within 2 seconds. The workload profile is an open planning item.
- **NFR-004 (Task usability)**: In usability testing, at least 90% of first-time participants MUST complete account sign-in, expense submission, and review of their current balance without facilitator assistance.
- **NFR-005 (Data consistency)**: After an expense approval or payment is confirmed, the corresponding history and pairwise balances MUST reflect the same ledger event, without duplicate application.

### Proposed Java Swing MVP Subset

This subsection defines an internal prototype using Java Swing to validate the listed core expense and balance workflows. It is not a customer-facing release, is not a customer requirement, and does not replace the complete customer inventory above. A Swing desktop experience does not satisfy the primary web, Android, or iOS platform requirement.

**Included in the proposed subset**:

- Account access and group creation/membership (FR-001, FR-002).
- Expense entry, participant selection, equal or percentage splits, and optional receipt (FR-003 through FR-005).
- A single review/approval level, with pending and rejected expenses excluded from balances (a subset of FR-007 and FR-008).
- Pairwise balance display and recording full or partial payments (FR-009, FR-010).
- Basic summaries of expenses, balances, and payments (FR-011).

**Not included in this proposed subset**: expense-created notifications (FR-006), multiple approval levels (FR-007), custom reports (FR-012), and web/Android/iOS customer platform delivery (FR-013). These remain in the full requirements inventory and require follow-on delivery; their exclusion from the Swing subset is not customer approval to remove them.

### Key Entities

- **Account**: A person's sign-in identity and access to their own information.
- **Group**: A shared context with members, expense review policy, expenses, and settlements.
- **Membership**: A person's invited, accepted, active, or removed relationship to a group, including their resulting access.
- **Expense**: A submitted amount, date, description, payer, participants, allocation method, optional receipt, and review state.
- **Allocation**: A participant's share of an expense under the selected equal or percentage method.
- **Approval**: A review outcome, reviewer, time, and applicable workflow level for an expense.
- **Balance**: The net amount one group member owes another based on approved allocations and payments.
- **Payment**: A dated settlement amount between a payer and payee.
- **Notification**: An expense-created or review-state message addressed to an eligible group member.
- **Report**: A summary or filtered view of authorized expense, balance, and payment information.

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: In a representative test group, a member can submit a complete shared expense in 3 minutes or less.
- **SC-002**: For every test ledger, approved expense allocations sum exactly to their recorded expense amounts, and pending or rejected expenses change no balance.
- **SC-003**: In all tested combinations of approved expenses and payments, displayed pairwise balances match independently calculated expected balances.
- **SC-004**: At least 90% of first-time usability-test participants complete account sign-in, expense submission, and balance review without assistance.
- **SC-005**: Every customer feature in the requirements inventory has a requirement ID, acceptance condition, and explicit full-product or proposed-MVP scope designation before release planning is approved.
- **SC-006**: Authorized users can produce a report for a selected date range whose included records and totals match the underlying ledger.
- **SC-007**: The supported core user journeys are available on the primary web product and on Android and iOS apps before the full customer platform requirement is considered met.

## Assumptions

- Group expenses are denominated in one currency per group for the initial specification; multi-currency conversion is not implied.
- The expense submitter may record any group member as the payer; the payer need not be included among the expense participants.
- Each account uses a unique email address verified during registration; forgotten passwords are reset using a link sent to that verified address.
- A group owner or admin invites accounts, and an invitee must accept before becoming a member. Removed members retain read-only access only to expenses and payments in which they participated.
- Expense amounts must be positive; percentage allocations must consist of values from 0% through 100% totaling exactly 100%.
- Recording a payment means it has already been made; the balance is reduced immediately, without payee confirmation.
- A group designates approvers and selects single- or multiple-level approval for full product scope. The submitter cannot approve their own expense. The proposed Swing MVP uses one level only.
- Expense-created notifications are delivered in-app and by email to every other active group member.
- Equal and percentage splits use the group's currency precision. Leftover cents go to participants in descending order of fractional remainder, with ties broken by participant selection order.
- Basic reports include personal or group summaries; custom reports support inclusive date ranges and group/member filters unless additional parameters are agreed.
- The Java Swing MVP is an internal prototype only, not a customer-facing release. Web, Android, and iOS remain separate customer-facing deliverables and requirements.
- The usability and responsiveness thresholds above are proposed test targets, not targets supplied by the customer.
- Payments greater than the current amount owed are rejected; payments do not create credit balances.

### Open Questions

- Which currencies and per-currency minor-unit precision must be supported?
- Is functional parity expected across web, Android, and iOS, or may advanced features differ by platform?
- Which group roles may designate approvers, and what happens when an approver is unavailable?
- What policy governs receipt retention and group deletion?
- Which additional parameters, beyond date range and group/member filters, must custom reports support?
- What user volume and device/network conditions define the launch workload for NFR-003?
