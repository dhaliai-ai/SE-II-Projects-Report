# Feature 4: Maintain Billing Rates

## Header

- **Feature ID:** 4
- **Feature Name:** Maintain Billing Rates
- **System:** Invoicing System
- **Branch:** invoicing-system
- **Status:** Draft
- **Purpose:** Allow authorized company personnel to maintain billing rates for consulting projects and roles while preserving existing client agreements and historical billing information.

## User Stories

**US-4.1: Establish a Billing Rate**

As an owner or authorized administrator, I want to establish billing rates for project roles so that the company can charge clients correctly for consulting services.

**US-4.2: Maintain Project-Specific Rates**

As an authorized administrator, I want to assign billing rates to individual projects so that different client agreements can be supported.

**US-4.3: Update Billing Rates**

As an owner or authorized administrator, I want to establish new billing rates when pricing changes so that future work can use updated rates without incorrectly changing existing agreements.

**US-4.4: View Billing Rates**

As an authorized project manager or accounting staff member, I want to view applicable project billing rates so that I can review project budgets and invoices.

**US-4.5: Preserve Historical Rates**

As an accounting staff member, I want previously agreed billing rates to remain available so that historical time entries and invoices can be calculated and reviewed accurately.

## Functional Requirements

- **FR-4.1:** The system shall allow authorized users to establish billing rates for consulting roles.
- **FR-4.2:** Each billing rate record shall have a unique identifier.
- **FR-4.3:** The system shall support different billing rates for different projects.
- **FR-4.4:** The system shall associate billing rates with project roles.
- **FR-4.5:** The system shall store the monetary rate charged per billable hour.
- **FR-4.6:** The system shall allow authorized users to establish new rates when pricing agreements change.
- **FR-4.7:** The system shall preserve existing client and project billing arrangements unless an authorized change is explicitly made.
- **FR-4.8:** The system shall retain historical billing rate records required for accurate invoicing.
- **FR-4.9:** The system shall allow authorized users to retrieve applicable billing rates.
- **FR-4.10:** The system shall prevent unauthorized users from creating or modifying billing rates.
- **FR-4.11:** The system shall validate that billing rates are nonnegative monetary values.
- **FR-4.12:** The system shall identify the applicable billing rate for a project role and date.
- **FR-4.13:** The system shall prevent ambiguous overlapping effective periods for the same project and role.
- **FR-4.14:** The system shall preserve billing rates referenced by existing invoices.

## Assumptions

- The company bills clients based on employee hours worked on consulting projects.
- Billing rates may depend on the employee's role and the project.
- Employees may perform different roles on different projects.
- Existing client agreements should not automatically change when new standard rates are introduced.
- Project-specific rates take precedence over general standard rates when an applicable project rate exists.
- Billing rates are expressed as monetary amounts per hour.
- Rates may have effective dates to support historical billing.
- The system will use one configured billing currency initially.
- Only owners or authorized administrators may modify billing rates.
- Project managers and accounting staff may view rates relevant to their responsibilities.
- The precise process for renegotiating existing client rates will be finalized during implementation.

## Edge Cases

- An administrator enters a negative billing rate.
- A user enters a nonnumeric rate.
- A project role has no applicable billing rate.
- An employee works on multiple projects with different billing rates.
- An employee performs different roles on separate projects.
- A new standard rate is introduced while an existing project agreement remains active.
- A project-specific billing rate changes during a project.
- A user attempts to create overlapping rate periods for the same project and role.
- A rate referenced by an existing invoice is changed.
- An unauthorized employee attempts to modify billing rates.
- A user requests a nonexistent billing rate.
- A time entry falls before the effective date of a new rate.

## Success Criteria

- Authorized users can establish and retrieve billing rates.
- Different projects can use different rates for the same role.
- Existing client agreements remain unchanged when standard rates change.
- Historical billing rates remain available.
- The correct rate can be determined for a project role and work date.
- Invalid and unauthorized rate changes are rejected.
- Previously generated invoices retain their original billing amounts.
- Billing rates can be used consistently by time-entry and invoice-generation features.

## Key Entities

**BillingRate**
- Represents a monetary billing rate applicable to a project role over a defined period.

**StandardBillingRate**
- Represents a proposed standard rate used when establishing new project agreements.

**Project**
- Represents the consulting project associated with a billing arrangement.

**ProjectRole**
- Represents the role performed by an employee on a project.

**ProjectAssignment**
- Connects an employee to a project and role.

**InvoiceLineItem**
- Represents billable work calculated using an applicable rate.

## Initial Data Model

**BillingRate**

| Field | Type | Description |
|---|---|---|
| billingRateId | Integer, PK | Unique billing rate identifier |
| projectId | Integer, FK | Project associated with the agreed rate |
| roleId | Integer, FK | Applicable project role |
| hourlyRate | Decimal(12,2) | Monetary amount charged per hour |
| effectiveDate | Date | Date the rate becomes effective |
| endDate | Date, Nullable | Last date the rate applies |
| createdAt | DateTime | Record creation timestamp |
| updatedAt | DateTime | Last modification timestamp |

**StandardBillingRate**

| Field | Type | Description |
|---|---|---|
| standardRateId | Integer, PK | Unique standard rate identifier |
| roleId | Integer, FK | Applicable role |
| hourlyRate | Decimal(12,2) | Standard hourly billing rate |
| effectiveDate | Date | Date the standard rate becomes effective |
| endDate | Date, Nullable | Last date the standard rate applies |

**Relationships**

- One Project may have many BillingRate records.
- One ProjectRole may have many BillingRate records across projects and effective periods.
- One ProjectRole may have many StandardBillingRate records over time.
- ProjectAssignment references the project and role used to determine an applicable rate.
- InvoiceLineItem stores the rate actually used for billing so that historical invoices remain accurate.
- Maintain Projects owns Project, ProjectRole, and ProjectAssignment.
- Maintain Billing Rates owns BillingRate and StandardBillingRate.

## Gherkin AC

**Scenario 1: Establish a project billing rate**

Given an authorized administrator is authenticated  
And an existing project and project role are available  
When the administrator enters a valid hourly rate and effective date  
And submits the billing rate form  
Then the system shall create the billing rate  
And associate it with the selected project and role.

**Scenario 2: Reject a negative billing rate**

Given an authorized administrator is creating a billing rate  
When the administrator enters a negative hourly rate  
Then the system shall reject the request  
And display a validation message.

**Scenario 3: Preserve an existing client agreement**

Given an existing project has an agreed billing rate  
And the company introduces a new standard rate  
When the new standard rate becomes effective  
Then the existing project's agreed rate shall remain unchanged  
Unless an authorized user explicitly changes the project agreement.

**Scenario 4: Apply different rates to different projects**

Given an employee works on two projects in the same role  
And each project has a different agreed billing rate  
When the system determines the rate for each project's work  
Then the system shall use the applicable rate for each project.

**Scenario 5: Determine a historical rate**

Given a project role has billing rates with different effective periods  
When the system requests the rate for a historical work date  
Then the system shall return the rate applicable on that date.

**Scenario 6: Reject overlapping rate periods**

Given a project role already has a billing rate for a defined period  
When an administrator attempts to create another rate with an overlapping effective period  
Then the system shall reject the conflicting rate  
And preserve the existing rate records.

**Scenario 7: Prevent unauthorized rate modification**

Given an employee does not have billing rate management permission  
When the employee attempts to modify a billing rate  
Then the system shall reject the request  
And leave the rate unchanged.

**Scenario 8: Preserve invoiced billing amounts**

Given an invoice has already been generated using an agreed billing rate  
When an authorized administrator establishes a new rate  
Then the existing invoice shall retain its original rate and billed amount.

## Data Ownership

- Maintain Billing Rates owns BillingRate and StandardBillingRate records.
- Maintain Projects owns Project, ProjectRole, and ProjectAssignment records.
- Maintain Billing Rates references projects and roles using their identifiers.
- Enter Project Time owns TimeEntry records and does not modify billing rates.
- Generate Invoices reads applicable billing rates and stores the rate used on each invoice line item.
- Authorized owners or administrators may create and modify billing rate arrangements.
- Project managers and accounting staff may retrieve billing rates within their permitted responsibilities.
- Other features shall not directly overwrite historical billing rate records.

## API Requirements

**Proposed REST API endpoints**

| Method | Endpoint | Purpose |
|---|---|---|
| GET | /billing-rates | Retrieve permitted billing rates |
| GET | /billing-rates/:id | Retrieve a billing rate |
| POST | /billing-rates | Establish a billing rate |
| PUT | /billing-rates/:id | Update a permitted rate record |
| GET | /projects/:id/billing-rates | Retrieve project billing rates |
| GET | /projects/:id/billing-rates/applicable | Retrieve the applicable rate for a role and date |
| GET | /standard-billing-rates | Retrieve standard billing rates |
| POST | /standard-billing-rates | Create a standard billing rate |

- All endpoints shall require authentication.
- Rate creation and modification shall require appropriate authorization.
- The applicable-rate endpoint shall accept a role identifier and work date.
- Invalid rate values and effective periods shall return HTTP 400.
- Missing records shall return HTTP 404.
- Unauthenticated requests shall return HTTP 401.
- Authenticated users without sufficient permission shall receive HTTP 403.
- Updates shall not silently alter historical invoice calculations.

## Screen Requirements

**Billing Rate List Screen**

- Display billing rates available to the authorized user.
- Show associated projects, roles, hourly rates, and effective dates.
- Allow authorized users to select a rate for viewing.

**Billing Rate Form**

- Allow authorized administrators to select a project and role.
- Accept hourly rate and effective-date information.
- Validate monetary values and effective periods.
- Display validation and save-result messages.

**Project Billing Rates Screen**

- Display rates associated with a selected project.
- Show historical and currently applicable rates.
- Allow authorized users to establish new agreed rates.

**Standard Billing Rates Screen**

- Display standard billing rates by role.
- Allow authorized administrators to establish updated standard rates.
- Explain that changes to standard rates do not automatically change existing project agreements.

## Definition of Done

- Billing rate creation and retrieval are implemented.
- Project-specific role rates are supported.
- Standard billing rate maintenance is implemented.
- Applicable-rate selection by project, role, and date is implemented.
- Existing client agreements are preserved when standard rates change.
- Historical billing rates remain available.
- Rate validation and authorization are enforced.
- Overlapping effective periods are rejected.
- Existing invoice amounts are protected from later rate changes.
- Required API endpoints and screens are implemented and tested.
- Automated tests cover valid rates, invalid values, historical rates, and authorization.
- Feature documentation is reviewed and complete.

## Out of Scope

- Creating or modifying clients.
- Creating employee records.
- Creating projects or assigning employees.
- Recording employee work hours.
- Approving employee time entries.
- Generating invoices.
- Recording client payments.
- Employee salary and payroll calculations.
- Automatic negotiation of client billing rates.
- Automatic currency conversion.
- Tax calculation and tax-rate management.
- Retroactively recalculating previously issued invoices.