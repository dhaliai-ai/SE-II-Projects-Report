# Feature 3: Maintain Projects

## Header

- **Feature ID:** 3
- **Feature Name:** Maintain Projects
- **System:** Invoicing System
- **Branch:** invoicing-system
- **Status:** Draft
- **Purpose:** Allow authorized company personnel to create and maintain consulting projects, assign project managers and employees, and track project information required for time entry and invoicing.

## User Stories

**US-3.1: Create a Project**

As an owner or authorized administrator, I want to create a project for an existing client so that the company can organize consulting work and track billable hours.

**US-3.2: Update Project Information**

As an authorized administrator or project manager, I want to maintain project information so that project details remain accurate.

**US-3.3: Assign a Project Manager**

As an owner or administrator, I want to assign a project manager so that someone is responsible for supervising project work and approving time.

**US-3.4: Assign Employees to Projects**

As a project manager, I want to assign employees to my projects so that they can record hours for the work they perform.

**US-3.5: Maintain Project Roles**

As a project manager, I want to identify the roles employees perform on projects so that their work can be billed at the appropriate rates.

**US-3.6: Monitor Project Budgets**

As a project manager, I want to view project budget information so that I can monitor project costs and progress.

**US-3.7: Maintain Project Contacts**

As an authorized user, I want to associate a client sponsor or contact with a project so that invoices and project communications reach the appropriate person.

## Functional Requirements

- **FR-3.1:** The system shall allow authorized users to create projects associated with existing clients.
- **FR-3.2:** Each project shall have a unique project identifier.
- **FR-3.3:** Each project shall be associated with one client.
- **FR-3.4:** The system shall maintain the project name, description, start date, and expected end date.
- **FR-3.5:** The system shall allow an authorized administrator to assign a project manager.
- **FR-3.6:** The system shall allow authorized users to assign employees to projects.
- **FR-3.7:** The system shall support multiple employees working on the same project.
- **FR-3.8:** The system shall support an employee working on multiple projects.
- **FR-3.9:** The system shall maintain the role associated with each employee's project assignment.
- **FR-3.10:** The system shall maintain project budget information.
- **FR-3.11:** The system shall allow authorized project managers to view budget information.
- **FR-3.12:** The system shall associate a project with an appropriate client sponsor or contact.
- **FR-3.13:** The system shall allow authorized users to update project information.
- **FR-3.14:** The system shall preserve project relationships with historical time entries and invoices.
- **FR-3.15:** The system shall prevent unauthorized changes to project information.
- **FR-3.16:** The system shall maintain project status information.

## Assumptions

- A client may have multiple consulting projects.
- Each project belongs to one client.
- Each project has one designated project manager at a time.
- A project may have multiple assigned employees.
- An employee may work on several projects simultaneously.
- An employee's role may differ between projects.
- Projects commonly last between three and eighteen months, but this is not a mandatory duration limit.
- Larger engagements may be divided into smaller projects.
- Billing rates are maintained by the Maintain Billing Rates feature.
- Time entries are maintained by the Enter Project Time feature.
- The project budget is stored as a monetary amount.
- Project statuses such as Planned, Active, Completed, and Cancelled are proposed initial values.
- The precise permissions for project managers to edit project details will be finalized during implementation.

## Edge Cases

- A user attempts to create a project without selecting a client.
- A user provides an expected end date earlier than the start date.
- An administrator attempts to assign a nonexistent employee as project manager.
- An employee is assigned to more than one project.
- A project has no employees assigned yet.
- An employee is assigned different roles on different projects.
- A user attempts to assign an inactive employee.
- An unauthorized user attempts to modify project information.
- A project reaches or exceeds its budget.
- A user attempts to close a project with unapproved time entries.
- A user attempts to remove a project that has historical invoices.
- A client has several projects with different sponsors.

## Success Criteria

- Authorized users can create and maintain projects.
- Each project is associated with the correct client.
- Project managers can be assigned successfully.
- Employees can be assigned to multiple projects.
- Project-specific employee roles are maintained.
- Project budget information can be retrieved.
- Project contacts are correctly associated.
- Unauthorized modifications are rejected.
- Historical time entries and invoices remain associated with the correct project.
- Projects can support time tracking, approval, and invoicing workflows.

## Key Entities

**Project**
- Represents a consulting engagement performed for a client.

**Client**
- Represents the organization receiving consulting services.

**Employee**
- Represents a company employee.

**ProjectAssignment**
- Connects an employee to a project and identifies their role.

**ProjectRole**
- Represents a type of work or responsibility performed on a project.

**ClientContact**
- Represents a person associated with the client who may serve as a project sponsor.

**TimeEntry**
- Represents hours worked on a project.

**Invoice**
- Represents billing information associated with a project.

## Initial Data Model

**Project**

| Field | Type | Description |
|---|---|---|
| projectId | Integer, PK | Unique project identifier |
| clientId | Integer, FK | Client associated with the project |
| projectName | String | Name of the project |
| description | Text | Project description |
| projectManagerId | Integer, FK | Employee assigned as project manager |
| sponsorContactId | Integer, FK, Nullable | Client contact associated with the project |
| startDate | Date | Project start date |
| expectedEndDate | Date | Expected completion date |
| budgetAmount | Decimal | Project budget |
| status | Enum | Project status |
| createdAt | DateTime | Record creation timestamp |
| updatedAt | DateTime | Last modification timestamp |

**ProjectAssignment**

| Field | Type | Description |
|---|---|---|
| assignmentId | Integer, PK | Unique assignment identifier |
| projectId | Integer, FK | Associated project |
| employeeId | Integer, FK | Assigned employee |
| roleId | Integer, FK | Role performed on the project |
| assignedAt | Date | Assignment date |
| active | Boolean | Whether the assignment is currently active |

**ProjectRole**

| Field | Type | Description |
|---|---|---|
| roleId | Integer, PK | Unique project role identifier |
| roleName | String | Role name |
| description | String | Role description |

**Relationships**

- One Client may have many Projects.
- One Project belongs to one Client.
- One Project has one designated project manager at a time.
- One Project may have many ProjectAssignments.
- One Employee may have many ProjectAssignments.
- One ProjectRole may be referenced by many ProjectAssignments.
- One Project may have many TimeEntries.
- One Project may be associated with many Invoices.
- A Project's sponsorContactId must reference a contact belonging to the same client.

## Gherkin AC

**Scenario 1: Create a project successfully**

Given an authorized administrator is authenticated  
And an existing client is available  
When the administrator enters valid project information  
And submits the project form  
Then the system shall create the project  
And associate it with the selected client  
And assign a unique project identifier.

**Scenario 2: Reject a project without a client**

Given an authorized administrator is creating a project  
When the administrator submits the project without selecting a client  
Then the system shall reject the request  
And display a validation message.

**Scenario 3: Assign a project manager**

Given an authorized administrator is authenticated  
And an existing project is available  
And an active employee exists  
When the administrator assigns the employee as project manager  
Then the system shall associate that employee with the project.

**Scenario 4: Assign an employee to a project**

Given a project manager is authenticated  
And the project manager is responsible for an existing project  
And an active employee exists  
When the project manager assigns the employee to the project with a valid role  
Then the system shall create a project assignment  
And the employee shall be associated with the project.

**Scenario 5: Employee assigned to multiple projects**

Given an employee is assigned to an existing project  
When an authorized project manager assigns that employee to another project  
Then the system shall preserve both assignments  
And maintain the appropriate role for each assignment.

**Scenario 6: Reject invalid project dates**

Given an authorized user is creating a project  
When the expected end date is earlier than the start date  
Then the system shall reject the request  
And display an appropriate validation message.

**Scenario 7: Prevent unauthorized modification**

Given an employee is authenticated  
And the employee does not have permission to modify a project  
When the employee attempts to update the project  
Then the system shall reject the request  
And preserve the existing project information.

**Scenario 8: View project budget**

Given a project manager is authenticated  
And the project manager is responsible for an existing project  
When the project manager opens the project details  
Then the system shall display the project budget information.

**Scenario 9: Preserve project history**

Given a project has existing time entries or invoices  
When an authorized user updates permitted project information  
Then the system shall preserve the project's historical time entries and invoice relationships.

## Data Ownership

- Maintain Projects owns the Project, ProjectAssignment, and ProjectRole entities.
- Maintain Clients owns Client and ClientContact records.
- Maintain Employees owns Employee records.
- Maintain Projects references clients and employees through their unique identifiers.
- Maintain Billing Rates owns billing rate records associated with project roles.
- Enter Project Time owns TimeEntry records.
- Generate Invoices owns invoice creation and references projects without modifying project master records.
- Authorized administrators may create and maintain project records.
- Project managers may maintain assignments and permitted details for projects they manage.
- Employees may view projects and assignments relevant to their work.
- Other features must not directly overwrite project master records.

## API Requirements

**Proposed REST API endpoints**

| Method | Endpoint | Purpose |
|---|---|---|
| GET | /projects | Retrieve permitted projects |
| GET | /projects/:id | Retrieve project details |
| POST | /projects | Create a project |
| PUT | /projects/:id | Update a project |
| PATCH | /projects/:id/status | Update project status |
| GET | /projects/:id/assignments | Retrieve project assignments |
| POST | /projects/:id/assignments | Assign an employee |
| PUT | /projects/:id/assignments/:assignmentId | Update an assignment |
| GET | /projects/:id/budget | Retrieve project budget information |

- All endpoints shall require authentication.
- Write operations shall enforce appropriate authorization.
- Project managers shall only modify projects for which they have the required permissions.
- Invalid requests shall return HTTP 400.
- Missing resources shall return HTTP 404.
- Unauthenticated requests shall return HTTP 401.
- Authenticated users without permission shall receive HTTP 403.
- The system shall validate client, employee, contact, and role references before saving assignments or project records.

## Screen Requirements

**Project List Screen**

- Display projects available to the authenticated user.
- Show project name, client, project manager, and status.
- Allow authorized users to open project details.
- Provide project creation access to authorized users.

**Project Detail Screen**

- Display project information, client, sponsor, manager, dates, status, and budget.
- Display assigned employees and their project roles.
- Allow authorized users to update permitted project details.

**Project Form**

- Allow an authorized user to select an existing client.
- Accept project name, description, dates, and budget information.
- Allow selection of a project manager and project sponsor.
- Validate required fields and dates.

**Project Assignment Screen**

- Display employees assigned to a project.
- Allow authorized project managers to assign employees and select their roles.
- Allow authorized users to update assignments.
- Display appropriate validation and save-result messages.

## Definition of Done

- Project creation, retrieval, and update operations are implemented.
- Project manager assignment is implemented.
- Employee project assignments and roles are maintained.
- Project budget information can be retrieved.
- Project sponsor associations are supported.
- Project status management is implemented.
- Authentication and authorization checks are enforced.
- Required validation rules are implemented.
- Historical time entry and invoice relationships are preserved.
- Automated tests cover successful operations, invalid input, and unauthorized access.
- Required API endpoints and screens are tested.
- Feature documentation is reviewed and complete.

## Out of Scope

- Creating or modifying client master records.
- Creating or modifying employee master records.
- Maintaining billing rate amounts.
- Recording employee work hours.
- Approving submitted time entries.
- Generating or delivering invoices.
- Recording client payments.
- Payroll processing.
- Automatic project scheduling or resource optimization.
- Automatic project budget alerts.
- Permanent deletion of projects with historical time entries or invoices.