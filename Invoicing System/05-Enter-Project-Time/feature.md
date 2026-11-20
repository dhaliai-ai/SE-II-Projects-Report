# Feature 5: Enter Project Time

## Header

- **Feature ID:** 5
- **Feature Name:** Enter Project Time
- **System:** Invoicing System
- **Branch:** invoicing-system
- **Status:** Draft
- **Purpose:** Allow employees to record and submit the hours they work on assigned consulting projects so that project managers can approve the time and accounting can use approved hours for client invoicing.

## User Stories

**US-5.1: Record Project Hours**

As an employee, I want to record the hours I work on assigned projects so that the company can track my work for client billing.

**US-5.2: Record Hours for Multiple Projects**

As an employee, I want to record hours for different projects during the same week so that all my consulting work is represented accurately.

**US-5.3: Review Weekly Time**

As an employee, I want to review my recorded hours before submitting them so that I can identify missing or incorrect entries.

**US-5.4: Submit Weekly Time**

As an employee, I want to submit my weekly project time so that the appropriate project managers can review it for approval.

**US-5.5: Correct Unsubmitted Time**

As an employee, I want to edit or remove my unsubmitted time entries so that I can correct mistakes before submitting them.

**US-5.6: View Time Submission Status**

As an employee, I want to see whether my time is saved, submitted, or approved so that I know whether additional action is needed.

## Functional Requirements

- **FR-5.1:** The system shall allow authenticated employees to record hours worked on assigned consulting projects.
- **FR-5.2:** Each time entry shall have a unique identifier.
- **FR-5.3:** Each time entry shall identify the employee, project, project role, work date, and hours worked.
- **FR-5.4:** The system shall allow employees to record time for multiple projects during the same reporting week.
- **FR-5.5:** The system shall associate each time entry with an existing project assignment.
- **FR-5.6:** The system shall allow employees to view their recorded time entries.
- **FR-5.7:** The system shall allow employees to modify or remove their own unsubmitted time entries.
- **FR-5.8:** The system shall allow employees to submit their time for weekly review.
- **FR-5.9:** The system shall maintain a status for each time entry.
- **FR-5.10:** The system shall prevent employees from directly modifying approved time entries.
- **FR-5.11:** The system shall prevent employees from recording time on projects to which they are not assigned.
- **FR-5.12:** The system shall validate that recorded hours are greater than zero.
- **FR-5.13:** The system shall prevent employees from submitting time entries belonging to another employee.
- **FR-5.14:** The system shall record the date and time when an entry is submitted.
- **FR-5.15:** The system shall allow authorized project managers and accounting personnel to retrieve time information needed for their responsibilities.
- **FR-5.16:** The system shall preserve historical time entries associated with invoices.
- **FR-5.17:** The system shall support remote time entry for authenticated employees.
- **FR-5.18:** The system shall identify submitted time entries awaiting project manager approval.

## Assumptions

- Employees are salaried, but their project hours are used to calculate client invoices.
- Employees submit their time weekly.
- An employee may work on multiple projects during a week.
- Employees may have different roles on different projects.
- Project assignments and roles are maintained by the Maintain Projects feature.
- Employees may enter time throughout the week and submit it when ready.
- Time entries initially have Draft status.
- Submitted time entries are locked against ordinary employee edits until an authorized correction process is available.
- Proposed statuses are Draft, Submitted, Approved, and Rejected.
- The Approve Project Time feature manages approval and rejection decisions.
- Accounting may need to identify missing or late time submissions.
- Time entries record hours as decimal values.
- A standard reporting week begins on Monday; this is a proposed configuration choice.
- The exact correction process for previously submitted or approved time will be finalized during implementation.

## Edge Cases

- An employee attempts to record time without selecting a project.
- An employee attempts to enter zero or negative hours.
- An employee enters a nonnumeric hours value.
- An employee attempts to record time for a project to which they are not assigned.
- An employee works on multiple projects during one day.
- An employee performs different roles on different projects.
- An employee attempts to modify another employee's time.
- An employee attempts to edit an approved time entry.
- An employee submits an empty weekly timesheet.
- An employee submits time after the expected weekly submission date.
- An employee attempts to submit the same entry twice.
- An employee attempts to record more than 24 hours for one workday.
- A project assignment becomes inactive after time has been recorded.
- A project closes while an employee has draft time entries.
- A submitted entry is rejected by a project manager.
- A time entry has already been included in an invoice.

## Success Criteria

- Employees can record hours for assigned projects.
- Employees can record time across multiple projects and roles.
- Employees can review and correct their draft entries.
- Employees can submit weekly time for approval.
- Submitted entries are available to the appropriate project managers.
- Unauthorized time-entry modifications are rejected.
- Invalid hours and project assignments are rejected.
- Employees can view their submission statuses.
- Historical approved and invoiced time remains available.
- The recorded time supports the invoice-generation workflow.

## Key Entities

**TimeEntry**
- Represents hours worked by an employee on a project.

**Employee**
- Represents the employee who performed the work.

**Project**
- Represents the consulting project on which work was performed.

**ProjectAssignment**
- Identifies the employee's authorized project and role.

**ProjectRole**
- Identifies the type of work performed.

**TimeApproval**
- Represents the approval decision associated with submitted time.

**InvoiceLineItem**
- Represents a billing line derived from approved time.

## Initial Data Model

**TimeEntry**

| Field | Type | Description |
|---|---|---|
| timeEntryId | Integer, PK | Unique time entry identifier |
| employeeId | Integer, FK | Employee who performed the work |
| projectId | Integer, FK | Project associated with the work |
| assignmentId | Integer, FK | Employee's project assignment |
| roleId | Integer, FK | Role performed |
| workDate | Date | Date the work was performed |
| hoursWorked | Decimal(5,2) | Number of hours worked |
| description | Text, Nullable | Description of work performed |
| status | Enum | Draft, Submitted, Approved, or Rejected |
| submittedAt | DateTime, Nullable | Submission timestamp |
| createdAt | DateTime | Creation timestamp |
| updatedAt | DateTime | Last modification timestamp |

**Relationships**

- One Employee may have many TimeEntries.
- One Project may have many TimeEntries.
- One ProjectAssignment may be associated with many TimeEntries.
- Each TimeEntry references one ProjectAssignment.
- The referenced assignment must match the entry's employee, project, and role.
- A submitted TimeEntry may have a related TimeApproval record.
- An approved TimeEntry may later be associated with an InvoiceLineItem.
- Maintain Employees owns Employee records.
- Maintain Projects owns Project, ProjectRole, and ProjectAssignment records.
- Enter Project Time owns TimeEntry records.

## Gherkin AC

**Scenario 1: Record project time successfully**

Given an employee is authenticated  
And the employee is assigned to an active project  
When the employee enters a valid work date and hours worked  
And saves the time entry  
Then the system shall create a Draft time entry  
And associate it with the employee and project.

**Scenario 2: Record time for multiple projects**

Given an employee is assigned to two projects  
When the employee records valid hours for each project  
Then the system shall save separate time entries  
And associate each entry with the correct project and role.

**Scenario 3: Reject an unassigned project**

Given an employee is authenticated  
And the employee is not assigned to a selected project  
When the employee attempts to record time for that project  
Then the system shall reject the request  
And display an authorization or validation message.

**Scenario 4: Reject invalid hours**

Given an employee is creating a time entry  
When the employee enters zero or negative hours  
Then the system shall reject the entry  
And display a validation message.

**Scenario 5: Update a draft time entry**

Given an employee has an existing Draft time entry  
When the employee changes the recorded hours to a valid value  
Then the system shall save the updated hours.

**Scenario 6: Submit weekly time**

Given an employee has valid Draft time entries for a reporting week  
When the employee submits the weekly time  
Then the system shall mark those entries as Submitted  
And record the submission timestamp  
And make the entries available for project manager approval.

**Scenario 7: Prevent editing approved time**

Given an employee has an Approved time entry  
When the employee attempts to modify that entry  
Then the system shall reject the modification  
And preserve the approved information.

**Scenario 8: Prevent modification of another employee's time**

Given an employee is authenticated  
And a time entry belongs to another employee  
When the employee attempts to update the entry  
Then the system shall reject the request  
And leave the entry unchanged.

**Scenario 9: View weekly time**

Given an employee is authenticated  
And the employee has recorded time during a reporting week  
When the employee opens the weekly timesheet  
Then the system shall display the employee's entries  
And show the associated project, role, hours, and status.

**Scenario 10: Prevent duplicate submission**

Given an employee has already submitted a time entry  
When the employee attempts to submit that entry again  
Then the system shall prevent duplicate submission  
And preserve the existing entry.

**Scenario 11: Preserve invoiced time**

Given a time entry has been included in an invoice  
When an employee attempts to modify or delete that entry  
Then the system shall reject the request  
And preserve the historical billing information.

## Data Ownership

- Enter Project Time owns the TimeEntry entity.
- Maintain Employees owns Employee records.
- Maintain Projects owns Project, ProjectRole, and ProjectAssignment records.
- Enter Project Time references project assignments to validate employee access.
- Approve Project Time owns approval decisions and manages approval-related status transitions.
- Generate Invoices reads approved TimeEntry records but does not modify recorded work details.
- Employees may create and modify their own Draft time entries.
- Project managers may retrieve submitted entries for projects they manage.
- Accounting personnel may retrieve time information needed to identify missing submissions and prepare invoices.
- Other features shall not directly overwrite recorded employee hours.

## API Requirements

**Proposed REST API endpoints**

| Method | Endpoint | Purpose |
|---|---|---|
| GET | /time-entries/me | Retrieve the authenticated employee's time entries |
| GET | /time-entries/:id | Retrieve a permitted time entry |
| POST | /time-entries | Create a time entry |
| PUT | /time-entries/:id | Update a Draft time entry |
| DELETE | /time-entries/:id | Remove a Draft time entry |
| POST | /time-entries/submit | Submit weekly time entries |
| GET | /time-entries/me/weekly | Retrieve weekly timesheet information |
| GET | /projects/:id/time-entries | Retrieve permitted project time entries |

- All endpoints shall require authentication.
- Employee creation and modification operations shall enforce record ownership.
- The system shall validate project assignments before accepting time entries.
- The weekly submission endpoint shall identify the reporting week.
- Invalid input shall return HTTP 400.
- Unauthenticated requests shall return HTTP 401.
- Authenticated users without permission shall receive HTTP 403.
- Missing records shall return HTTP 404.
- Attempts to modify locked or invoiced entries shall return HTTP 409.
- Approval decisions shall be handled by the Approve Project Time feature.

## Screen Requirements

**Weekly Timesheet Screen**

- Display the authenticated employee's time entries for a selected week.
- Show work dates, projects, roles, hours, and submission statuses.
- Display a weekly total of recorded hours.
- Allow the employee to submit eligible Draft entries.

**Time Entry Form**

- Allow employees to select an assigned project.
- Allow selection of an authorized project role.
- Accept work date, hours worked, and an optional work description.
- Validate required fields and hours.
- Display validation and save-result messages.

**Time Entry Detail Screen**

- Display recorded time information.
- Allow employees to edit or remove their own Draft entries.
- Prevent ordinary editing of Submitted, Approved, or invoiced entries.
- Display the current entry status.

**Submission Status View**

- Show whether entries are Draft, Submitted, Approved, or Rejected.
- Identify entries awaiting project manager approval.
- Display relevant submission timestamps.

## Definition of Done

- Employees can create, view, update, and remove permitted Draft time entries.
- Employees can record hours across multiple assigned projects.
- Project assignments and roles are validated.
- Weekly time submission is implemented.
- Time-entry statuses and submission timestamps are maintained.
- Approved and invoiced time entries are protected from unauthorized changes.
- Authentication, authorization, and ownership checks are enforced.
- Invalid hours and duplicate submissions are handled.
- Required API endpoints and screens are implemented and tested.
- Automated tests cover successful time entry, invalid inputs, weekly submission, and permission restrictions.
- Integration with the approval and invoice workflows is verified.
- Feature documentation is reviewed and complete.

## Out of Scope

- Creating or modifying employee master records.
- Creating projects or managing employee assignments.
- Maintaining billing rates.
- Approving or rejecting submitted time.
- Generating invoices.
- Recording client payments.
- Payroll processing.
- Automatic employee scheduling.
- Automatic calculation of employee salaries or overtime compensation.
- Automatic invoice delivery.
- Direct modification of historical invoiced time.