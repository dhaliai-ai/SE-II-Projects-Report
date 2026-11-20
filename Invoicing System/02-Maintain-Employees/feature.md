# Feature 2: Maintain Employees

## Header

- **Feature ID:** 2
- **Feature Name:** Maintain Employees
- **System:** Invoicing System
- **Branch:** invoicing-system
- **Status:** Draft
- **Purpose:** Allow authorized company personnel to maintain employee information used for project assignments, time tracking, and invoicing.

## User Stories

**US-2.1: Create Employee**

As an owner or administrator, I want to create employee records so that employees can be assigned to consulting projects.

**US-2.2: View Employee Information**

As an authorized user, I want to view employee information so that I can identify employees assigned to projects.

**US-2.3: Update Employee Information**

As an owner or administrator, I want to update employee information so that employee records remain accurate.

**US-2.4: Maintain Own Profile**

As an employee, I want to update my permitted personal information so that my profile remains current.

**US-2.5: Manage Employee Status**

As an owner or administrator, I want to mark employees as active or inactive so that former employees cannot be assigned new work while historical records remain available.

## Functional Requirements

- **FR-2.1:** The system shall allow authorized administrators to create employee records.
- **FR-2.2:** Each employee shall have a unique employee identifier.
- **FR-2.3:** The system shall maintain employee names and contact information.
- **FR-2.4:** The system shall allow authorized users to retrieve employee information.
- **FR-2.5:** The system shall allow administrators to update employee records.
- **FR-2.6:** Employees shall be allowed to update only their permitted personal information.
- **FR-2.7:** The system shall prevent employees from modifying another employee's information without authorization.
- **FR-2.8:** The system shall support active and inactive employee statuses.
- **FR-2.9:** The system shall preserve employee relationships with existing project assignments and historical time entries.
- **FR-2.10:** Employee information shall be accessible remotely to authenticated users with appropriate permissions.

## Assumptions

- Employees are salaried, but their project hours are tracked for client billing.
- An employee may work on multiple projects.
- An employee may perform different roles on different projects.
- Employee project assignments are managed by the Maintain Projects feature.
- Billing rates are managed separately from employee personal information.
- Only administrators may modify protected employee information.
- The exact list of employee-editable profile fields will be finalized during implementation.
- Inactive employees remain in the database for historical reporting and invoicing.

## Edge Cases

- An administrator attempts to create an employee without a name.
- An employee record contains an invalid email address.
- A duplicate employee record is submitted.
- An unauthorized user attempts to modify another employee's profile.
- An employee attempts to modify protected fields.
- An administrator marks an employee inactive while historical project assignments exist.
- A requested employee record does not exist.
- An employee is assigned to multiple projects.

## Success Criteria

- Authorized administrators can create and update employee records.
- Employees can view and update permitted profile information.
- Unauthorized changes are rejected.
- Employee records can be retrieved successfully.
- Active and inactive employee statuses are maintained.
- Historical project assignments and time entries remain associated with the correct employees.
- Employee records can support project assignment and time-tracking workflows.

## Key Entities

**Employee**
- Represents a person employed by the consulting company.

**UserAccount**
- Represents authentication and authorization information associated with an employee.

**ProjectAssignment**
- Represents an employee's assignment to a project.

**TimeEntry**
- Represents time recorded by an employee for project work.

## Initial Data Model

**Employee**

| Field | Type | Description |
|---|---|---|
| employeeId | Integer, PK | Unique employee identifier |
| firstName | String | Employee first name |
| lastName | String | Employee last name |
| email | String | Employee email address |
| phone | String | Employee contact number |
| status | Enum | Active or inactive |
| createdAt | DateTime | Record creation timestamp |
| updatedAt | DateTime | Last modification timestamp |

**UserAccount**

| Field | Type | Description |
|---|---|---|
| userId | Integer, PK | Unique account identifier |
| employeeId | Integer, FK | Associated employee |
| username | String | Login username |
| passwordHash | String | Securely stored password hash |
| accessRole | String | System authorization role |

**Relationships**

- One Employee may have one UserAccount.
- One Employee may have many ProjectAssignments.
- One Employee may have many TimeEntries.
- ProjectAssignment is owned by Maintain Projects.
- TimeEntry is owned by Enter Project Time.

## Gherkin AC

**Scenario 1: Create an employee successfully**

Given an administrator is authenticated  
And valid employee information is provided  
When the administrator submits the employee form  
Then the system shall create an employee record  
And assign a unique employee identifier.

**Scenario 2: Reject an employee without a name**

Given an administrator is creating an employee  
When the administrator submits the form without the required name  
Then the system shall reject the request  
And display a validation message.

**Scenario 3: Employee updates own profile**

Given an employee is authenticated  
And the employee opens their own profile  
When the employee changes a permitted field  
Then the system shall save the updated information.

**Scenario 4: Prevent unauthorized profile modification**

Given an employee is authenticated  
When the employee attempts to update another employee's protected information  
Then the system shall reject the request  
And preserve the original information.

**Scenario 5: Mark an employee inactive**

Given an administrator is authenticated  
And an active employee exists  
When the administrator changes the employee's status to inactive  
Then the system shall save the inactive status  
And preserve the employee's historical project assignments and time entries.

**Scenario 6: Retrieve an employee**

Given an authorized user is authenticated  
And an employee record exists  
When the user requests the employee information  
Then the system shall return the permitted employee fields.

## Data Ownership

- Maintain Employees owns the Employee entity.
- Maintain Employees manages the employee-to-user-account association; authentication and account security rules are shared system responsibilities.
- Maintain Projects owns ProjectAssignment records and references Employee through employeeId.
- Enter Project Time owns TimeEntry records and references Employee through employeeId.
- Only authorized administrators may change protected employee information.
- Employees may modify only permitted fields in their own profiles.
- Other features must not independently overwrite employee master records.

## API Requirements

**Proposed REST API endpoints**

| Method | Endpoint | Purpose |
|---|---|---|
| GET | /employees | Retrieve employee records |
| GET | /employees/:id | Retrieve an employee |
| POST | /employees | Create an employee |
| PUT | /employees/:id | Update an employee |
| PATCH | /employees/:id/status | Update employee status |
| GET | /employees/me | Retrieve the authenticated employee's profile |
| PATCH | /employees/me | Update permitted personal information |

- All endpoints shall require authentication.
- Administrative operations shall enforce administrator authorization.
- Employee self-service updates shall enforce record ownership.
- Invalid input shall return HTTP 400.
- Missing employee records shall return HTTP 404.
- Unauthenticated requests shall return HTTP 401.
- Authenticated users without sufficient permissions shall receive HTTP 403.
- Password hashes and other protected account information shall not be returned in employee profile responses.

## Screen Requirements

**Employee List Screen**

- Display employee names and statuses.
- Allow authorized users to select an employee.
- Provide employee creation access to administrators.

**Employee Detail Screen**

- Display permitted employee information.
- Allow administrators to edit employee records.
- Display employee status.

**Employee Profile Screen**

- Allow employees to view their own profile.
- Allow employees to update permitted personal information.
- Prevent changes to protected fields.

**Employee Form**

- Accept required employee information.
- Validate required fields and email format.
- Display appropriate validation and save-result messages.

## Definition of Done

- Employee creation, retrieval, and update operations are implemented.
- Employee profile self-service is implemented.
- Employee status management is implemented.
- Authentication and authorization checks are enforced.
- Input validation is implemented.
- Historical project and time-entry relationships are preserved.
- API endpoints are tested for valid and invalid requests.
- Automated tests cover employee permissions and ownership restrictions.
- Required screens are implemented and tested.
- Feature documentation is reviewed and complete.

## Out of Scope

- Creating and managing client records.
- Creating projects and assigning employees to projects.
- Managing project-specific billing rates.
- Entering or approving project time.
- Generating invoices.
- Processing client payments.
- Employee payroll and salary calculations.
- Permanent deletion of employee records referenced by historical project or billing information.