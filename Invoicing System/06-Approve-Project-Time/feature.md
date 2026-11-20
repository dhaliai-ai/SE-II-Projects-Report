# Feature 6: Approve Project Time

## Header

- **Feature ID:** 6
- **Feature Name:** Approve Project Time
- **System:** Invoicing System
- **Branch:** invoicing-system
- **Status:** Draft
- **Purpose:** Allow project managers to review, approve, or reject employee time submitted for their projects so that accounting can invoice clients using verified hours.

## User Stories

**US-6.1: View Submitted Time**

As a project manager, I want to view submitted time entries for projects I manage so that I can review employee work.

**US-6.2: Approve Time Entries**

As a project manager, I want to approve valid submitted hours so that they become eligible for invoicing.

**US-6.3: Reject Incorrect Time**

As a project manager, I want to reject incorrect time entries with a reason so that employees know what needs correction.

**US-6.4: Review Approval Status**

As an employee, I want to view approval decisions on my submitted time so that I know whether my hours have been accepted.

**US-6.5: Monitor Pending Approvals**

As an accounting staff member, I want to identify time entries awaiting approval so that I can determine whether an invoice is ready to prepare.

## Functional Requirements

- **FR-6.1:** The system shall allow authenticated project managers to view submitted time entries for projects they manage.
- **FR-6.2:** The system shall display the employee, project, work date, hours, and current status for each submitted entry.
- **FR-6.3:** The system shall allow authorized project managers to approve submitted time entries.
- **FR-6.4:** The system shall allow authorized project managers to reject submitted time entries.
- **FR-6.5:** The system shall require a reason when rejecting a time entry.
- **FR-6.6:** The system shall record the identity of the manager making an approval decision.
- **FR-6.7:** The system shall record the date and time of each approval decision.
- **FR-6.8:** Approved time entries shall become eligible for invoice generation.
- **FR-6.9:** Rejected time entries shall not be eligible for invoice generation.
- **FR-6.10:** The system shall prevent managers from approving time entries for projects they are not authorized to manage.
- **FR-6.11:** The system shall prevent approval of Draft time entries.
- **FR-6.12:** The system shall prevent duplicate approval decisions on entries that are no longer Submitted.
- **FR-6.13:** The system shall preserve approval decisions and their history.
- **FR-6.14:** Employees shall be able to view approval results for their own time entries.
- **FR-6.15:** Authorized accounting staff shall be able to view pending and approved time for billing preparation.
- **FR-6.16:** The system shall prevent ordinary modification of time entries already included in invoices.

## Assumptions

- Employees submit project time weekly.
- Each project has a designated project manager.
- Project managers approve time for projects they manage.
- Submitted time may contain entries for multiple projects, requiring review by different project managers.
- Approval occurs at the individual time-entry level.
- A rejected entry may be corrected and resubmitted by its employee.
- The correction process changes a rejected entry back to Draft before resubmission.
- Accounting invoices only approved, uninvoiced time entries.
- Approval decisions are recorded for audit purposes.
- Accounting may view approval status but cannot approve project time unless separately authorized.
- The exact escalation process for overdue approvals remains to be confirmed.

## Edge Cases

- A project manager attempts to approve a Draft entry.
- A manager attempts to approve time for a project managed by someone else.
- A manager attempts to approve the same entry twice.
- A manager rejects an entry without providing a reason.
- An employee has submitted time for multiple projects managed by different managers.
- A manager attempts to approve a nonexistent entry.
- A time entry is already approved or rejected.
- An employee attempts to approve their own time without manager authorization.
- A project manager changes while submitted time is awaiting approval.
- A previously approved entry has already been included in an invoice.
- Accounting attempts to generate an invoice while some time entries remain unapproved.
- A rejected entry is corrected and resubmitted.

## Success Criteria

- Project managers can view submitted time for their projects.
- Authorized managers can approve or reject submitted entries.
- Rejected entries include a reason.
- Approval decisions record the manager and decision timestamp.
- Employees can view the results of approval decisions.
- Unauthorized approvals and invalid status transitions are rejected.
- Only approved, uninvoiced time is eligible for billing.
- Accounting can identify time awaiting approval.
- Approval history remains available for review.

## Key Entities

**TimeEntry**
- Represents hours recorded and submitted by an employee.

**TimeApproval**
- Represents a project manager's approval or rejection decision.

**Employee**
- Represents the employee who submitted the time.

**Project**
- Represents the consulting project associated with the time.

**ProjectAssignment**
- Represents the employee's assignment to the project.

**InvoiceLineItem**
- Represents an invoice charge generated from approved time.

## Initial Data Model

**TimeApproval**

| Field | Type | Description |
|---|---|---|
| approvalId | Integer, PK | Unique approval decision identifier |
| timeEntryId | Integer, FK | Time entry being reviewed |
| managerId | Integer, FK | Employee who made the decision |
| decision | Enum | Approved or Rejected |
| rejectionReason | Text, Nullable | Reason for rejecting the entry |
| decidedAt | DateTime | Decision timestamp |

**Existing TimeEntry Fields Used**

| Field | Type | Description |
|---|---|---|
| timeEntryId | Integer, PK | Time entry identifier |
| employeeId | Integer, FK | Employee who recorded the hours |
| projectId | Integer, FK | Project associated with the work |
| hoursWorked | Decimal(5,2) | Hours submitted |
| status | Enum | Draft, Submitted, Approved, or Rejected |
| submittedAt | DateTime, Nullable | Time submission timestamp |

**Relationships**

- One TimeEntry may have multiple TimeApproval records over successive review cycles.
- Each TimeApproval references one TimeEntry.
- Each TimeApproval references one manager.
- One Project may have many submitted TimeEntries.
- One Employee may have many submitted TimeEntries.
- An InvoiceLineItem may reference an approved TimeEntry.
- Enter Project Time owns the recorded work details.
- Approve Project Time owns approval decisions and approval-related status transitions.

## Gherkin AC

**Scenario 1: View submitted time**

Given a project manager is authenticated  
And the manager is responsible for an existing project  
And employees have submitted time for that project  
When the manager opens the pending approval screen  
Then the system shall display the submitted time entries  
And show each employee, work date, hours, and status.

**Scenario 2: Approve a submitted time entry**

Given a project manager is authenticated  
And a Submitted time entry belongs to a project they manage  
When the manager approves the entry  
Then the system shall change its status to Approved  
And record the manager and decision timestamp  
And make the entry eligible for invoicing.

**Scenario 3: Reject an incorrect time entry**

Given a project manager is authenticated  
And a Submitted time entry belongs to a project they manage  
When the manager rejects the entry with a reason  
Then the system shall change its status to Rejected  
And record the rejection reason and decision timestamp  
And exclude the entry from invoice generation.

**Scenario 4: Reject a decision without a reason**

Given a project manager is reviewing a Submitted entry  
When the manager attempts to reject it without a reason  
Then the system shall reject the decision  
And leave the time entry in Submitted status.

**Scenario 5: Prevent unauthorized approval**

Given a manager is authenticated  
And a Submitted time entry belongs to a project they do not manage  
When the manager attempts to approve that entry  
Then the system shall reject the request  
And preserve the entry's status.

**Scenario 6: Prevent approval of Draft time**

Given a Draft time entry exists  
When a project manager attempts to approve the entry  
Then the system shall reject the request  
And preserve its Draft status.

**Scenario 7: Prevent duplicate approval**

Given a time entry has already been approved  
When a manager attempts to approve the same entry again  
Then the system shall reject the duplicate decision  
And preserve the existing approval record.

**Scenario 8: Employee views rejection**

Given an employee has a Rejected time entry  
When the employee opens their timesheet  
Then the system shall display the Rejected status  
And show the rejection reason.

**Scenario 9: Correct and resubmit rejected time**

Given an employee has a Rejected time entry  
When the employee corrects the entry through the authorized correction process  
And resubmits the entry  
Then the system shall mark the corrected entry as Submitted  
And make it available for another review  
And preserve the earlier rejection decision.

**Scenario 10: Accounting views pending approvals**

Given an accounting staff member is authenticated  
And submitted time entries are awaiting approval  
When the accounting staff member opens the approval status view  
Then the system shall display entries awaiting approval  
And distinguish them from approved entries.

## Data Ownership

- Approve Project Time owns TimeApproval records.
- Enter Project Time owns TimeEntry work details.
- Approve Project Time controls approval-related TimeEntry status transitions through a defined interface.
- Maintain Projects owns Project and ProjectAssignment records.
- Maintain Employees owns Employee records.
- Project managers may approve or reject time only for projects they are authorized to manage.
- Employees may view approval results for their own entries.
- Accounting personnel may view approval information needed for billing.
- Generate Invoices reads approved TimeEntry records but does not change approval decisions.
- Historical approval decisions shall not be overwritten when an entry is corrected and resubmitted.

## API Requirements

**Proposed REST API endpoints**

| Method | Endpoint | Purpose |
|---|---|---|
| GET | /time-approvals/pending | Retrieve time awaiting approval |
| GET | /projects/:id/time-approvals | Retrieve approval information for a project |
| POST | /time-entries/:id/approve | Approve a submitted entry |
| POST | /time-entries/:id/reject | Reject a submitted entry with a reason |
| GET | /time-entries/:id/approvals | Retrieve approval history |
| GET | /time-approvals/status | Retrieve permitted approval status information |

- All endpoints shall require authentication.
- Approval and rejection operations shall enforce project-manager authorization.
- Rejection requests shall include a nonblank reason.
- Only Submitted entries may be approved or rejected.
- Invalid input shall return HTTP 400.
- Unauthenticated requests shall return HTTP 401.
- Authenticated users without sufficient permission shall receive HTTP 403.
- Missing records shall return HTTP 404.
- Invalid status transitions shall return HTTP 409.
- Approval decisions and status changes shall be saved together to prevent inconsistent records.

## Screen Requirements

**Pending Time Approval Screen**

- Display submitted entries for projects managed by the authenticated project manager.
- Show employee, project, date, hours, and status.
- Allow managers to select entries for review.
- Provide approval and rejection actions.

**Time Approval Detail Screen**

- Display the selected entry's recorded information.
- Allow an authorized manager to approve the entry.
- Require a reason when rejecting the entry.
- Display validation and decision-result messages.

**Employee Approval Status View**

- Display approval statuses for the employee's submitted entries.
- Show rejection reasons where applicable.
- Allow employees to identify entries requiring correction.

**Accounting Approval Status Screen**

- Display pending and approved time information relevant to billing.
- Allow accounting personnel to identify entries that are not yet approved.
- Prevent unauthorized accounting personnel from changing approval decisions.

## Definition of Done

- Authorized project managers can retrieve submitted time for their projects.
- Submitted entries can be approved or rejected.
- Rejections require a reason.
- Approval decisions record manager identity and timestamps.
- Employees can view approval results and rejection reasons.
- Invalid status transitions and unauthorized approvals are prevented.
- Approval history is preserved across correction and resubmission.
- Accounting can view approval status for billing preparation.
- Approved time is available to invoice generation.
- Required API endpoints and screens are implemented and tested.
- Automated tests cover approvals, rejections, authorization, duplicate decisions, and status transitions.
- Feature documentation is reviewed and complete.

## Out of Scope

- Creating or modifying employee master records.
- Creating projects or managing employee assignments.
- Recording new employee work hours.
- Maintaining billing rates.
- Generating invoices.
- Delivering invoices to clients.
- Recording client payments.
- Payroll processing.
- Automatic approval of employee time.
- Automatic escalation of overdue approvals.
- Direct editing of time entries already included in invoices.