# Feature 7: Generate Invoices

## Header

- **Feature ID:** 7
- **Feature Name:** Generate Invoices
- **System:** Invoicing System
- **Branch:** invoicing-system
- **Status:** Draft
- **Purpose:** Allow authorized accounting personnel to generate accurate client invoices using approved employee project hours, applicable billing rates, and project-specific billing schedules.

## User Stories

**US-7.1: Generate a Client Invoice**

As an accounting staff member, I want to generate an invoice for a client's project so that the company can bill for completed consulting work.

**US-7.2: Apply Approved Project Time**

As an accounting staff member, I want invoices to include approved billable hours so that clients are charged only for work that has been reviewed.

**US-7.3: Apply Agreed Billing Rates**

As an accounting staff member, I want the system to use the appropriate project and role billing rates so that invoice amounts reflect client agreements.

**US-7.4: Support Different Billing Schedules**

As an accounting staff member, I want to generate invoices according to each project's billing schedule so that clients are billed at the agreed frequency.

**US-7.5: Review an Invoice Before Issuing**

As an accounting staff member, I want to review a draft invoice so that I can identify errors before sending it to the client.

**US-7.6: Prevent Duplicate Billing**

As an accounting staff member, I want the system to identify time already included in an invoice so that clients are not charged twice.

## Functional Requirements

- **FR-7.1:** The system shall allow authorized accounting personnel to generate invoices for existing projects.
- **FR-7.2:** Each invoice shall have a unique invoice identifier and invoice number.
- **FR-7.3:** Each invoice shall identify the client and associated project.
- **FR-7.4:** The system shall retrieve approved, uninvoiced time entries for the selected billing period.
- **FR-7.5:** Draft, Submitted, and Rejected time entries shall not be included in invoice calculations.
- **FR-7.6:** The system shall determine the applicable billing rate for each approved time entry using its project, role, and work date.
- **FR-7.7:** The system shall calculate each invoice line amount by multiplying approved hours by the applicable hourly rate.
- **FR-7.8:** The system shall calculate the invoice total from its line items.
- **FR-7.9:** The system shall support project-specific billing frequencies.
- **FR-7.10:** The system shall support weekly, biweekly, and monthly billing schedules.
- **FR-7.11:** The system shall identify the appropriate project contact for invoice delivery.
- **FR-7.12:** The system shall allow accounting personnel to review a draft invoice before issuing it.
- **FR-7.13:** The system shall prevent the same approved time entry from being billed on multiple active invoices.
- **FR-7.14:** The system shall preserve the hourly rate and calculated amount used on each invoice line.
- **FR-7.15:** The system shall prevent unauthorized users from generating invoices.
- **FR-7.16:** The system shall report missing billing rates or other errors that prevent accurate invoice generation.
- **FR-7.17:** The system shall retain invoice details for later retrieval and management.

## Assumptions

- Employees record project hours weekly.
- Project managers review and approve submitted time.
- Accounting is responsible for preparing client invoices.
- Invoices are normally generated biweekly, but billing schedules may vary by client or project.
- Weekly and monthly billing are supported as proposed schedule options.
- Each invoice is associated with one project and its client.
- A client may receive multiple invoices for different projects.
- Invoice recipients are selected from contacts associated with the project or client.
- Billing rates are determined by project, role, and effective date.
- Only approved, uninvoiced time entries are eligible for invoice generation.
- Invoice generation initially creates a Draft invoice.
- The invoice can be reviewed before it is issued.
- Due dates and payment terms may vary by client agreement; exact rules require confirmation.
- Invoice tax calculation is not included unless later specified.

## Edge Cases

- A billing period contains no approved time.
- Some time entries remain Submitted and await approval.
- A project has approved time but no applicable billing rate.
- An employee works on multiple projects during the billing period.
- Different project roles use different hourly rates.
- A billing rate changes during the billing period.
- The same time entry is selected for invoicing twice.
- A project has a billing schedule different from the company default.
- A project has no designated invoice contact.
- An invoice is generated for a nonexistent project.
- An unauthorized employee attempts to generate an invoice.
- Two accounting users attempt to invoice the same approved time simultaneously.
- A draft invoice is cancelled before being issued.
- A client has multiple projects with different billing periods.
- An invoice total contains decimal monetary calculations requiring rounding.

## Success Criteria

- Authorized accounting personnel can generate draft invoices.
- Invoices include the correct client and project information.
- Only approved, uninvoiced hours are included.
- Applicable project and role billing rates are used.
- Invoice line amounts and totals are calculated accurately.
- Different billing schedules can be supported.
- Draft invoices can be reviewed before issuing.
- Duplicate billing is prevented.
- Missing information produces clear validation messages.
- Invoice details remain available for subsequent management and reporting.

## Key Entities

**Invoice**
- Represents a bill prepared for a client's consulting project.

**InvoiceLineItem**
- Represents a charge derived from approved project time.

**Client**
- Represents the organization receiving the invoice.

**ClientContact**
- Represents the contact responsible for receiving or processing the invoice.

**Project**
- Represents the consulting project being billed.

**TimeEntry**
- Represents approved employee hours eligible for billing.

**BillingRate**
- Represents the agreed rate used to calculate charges.

**BillingSchedule**
- Represents the frequency and configuration of project invoicing.

## Initial Data Model

**Invoice**

| Field | Type | Description |
|---|---|---|
| invoiceId | Integer, PK | Unique invoice identifier |
| invoiceNumber | String, Unique | Human-readable invoice number |
| clientId | Integer, FK | Client being billed |
| projectId | Integer, FK | Project being billed |
| contactId | Integer, FK, Nullable | Intended invoice recipient |
| periodStart | Date | First date of billing period |
| periodEnd | Date | Last date of billing period |
| invoiceDate | Date, Nullable | Date invoice is issued |
| dueDate | Date, Nullable | Payment due date |
| status | Enum | Draft, Issued, Cancelled, or Paid |
| totalAmount | Decimal(12,2) | Total invoice amount |
| createdAt | DateTime | Creation timestamp |
| updatedAt | DateTime | Last modification timestamp |

**InvoiceLineItem**

| Field | Type | Description |
|---|---|---|
| invoiceLineItemId | Integer, PK | Unique invoice line identifier |
| invoiceId | Integer, FK | Parent invoice |
| timeEntryId | Integer, FK | Approved time being billed |
| description | Text | Description of the billed work |
| hoursBilled | Decimal(5,2) | Number of approved hours |
| hourlyRate | Decimal(12,2) | Rate used for this invoice |
| lineAmount | Decimal(12,2) | Calculated charge |

**BillingSchedule**

| Field | Type | Description |
|---|---|---|
| billingScheduleId | Integer, PK | Unique schedule identifier |
| projectId | Integer, FK, Unique | Project using the schedule |
| frequency | Enum | Weekly, Biweekly, or Monthly |
| nextBillingDate | Date | Next planned billing date |
| active | Boolean | Whether the schedule is active |

**Relationships**

- One Client may have many Invoices.
- One Project may have many Invoices.
- One Invoice has many InvoiceLineItems.
- Each InvoiceLineItem references one approved TimeEntry.
- A TimeEntry may be included in at most one active invoice.
- One Project may have one active BillingSchedule.
- Invoice recipient contacts must belong to the associated client.
- Generate Invoices owns Invoice, InvoiceLineItem, and BillingSchedule creation rules.
- Manage Invoices controls later invoice lifecycle changes.

## Gherkin AC

**Scenario 1: Generate an invoice successfully**

Given an accounting staff member is authenticated  
And an existing project has approved, uninvoiced time entries  
And applicable billing rates exist  
When the accounting staff member selects the project and billing period  
And requests invoice generation  
Then the system shall create a Draft invoice  
And include the eligible time entries  
And calculate the invoice total.

**Scenario 2: Exclude unapproved time**

Given a project has Approved and Submitted time entries  
When accounting generates an invoice  
Then the system shall include only Approved time entries  
And exclude entries awaiting approval.

**Scenario 3: Apply different billing rates**

Given approved time entries have different project roles or effective billing rates  
When the system generates an invoice  
Then each line item shall use its applicable rate  
And calculate its amount from the approved hours.

**Scenario 4: Prevent duplicate billing**

Given an approved time entry is already included in an active invoice  
When accounting generates another invoice for an overlapping period  
Then the system shall exclude the previously invoiced entry  
And prevent duplicate billing.

**Scenario 5: Reject a missing billing rate**

Given an approved time entry has no applicable billing rate  
When accounting attempts to generate an invoice containing that entry  
Then the system shall report the missing rate  
And shall not finalize an inaccurate invoice.

**Scenario 6: Support project billing schedules**

Given one project uses weekly billing  
And another project uses monthly billing  
When accounting reviews upcoming billing periods  
Then the system shall identify each project's billing schedule  
And allow invoices to be generated for the appropriate periods.

**Scenario 7: Review a draft invoice**

Given an accounting staff member has generated a Draft invoice  
When the staff member opens the invoice details  
Then the system shall display the client, project, billing period, line items, rates, and total  
And the invoice shall remain in Draft status until issued.

**Scenario 8: Prevent unauthorized invoice generation**

Given an employee is authenticated without invoice generation permission  
When the employee attempts to generate an invoice  
Then the system shall reject the request  
And no invoice shall be created.

**Scenario 9: Preserve historical invoice rates**

Given an invoice was generated using an agreed billing rate  
When a new billing rate becomes effective  
Then the existing invoice line items shall retain their original rates and amounts.

**Scenario 10: Prevent concurrent duplicate invoicing**

Given two accounting staff members attempt to invoice the same approved time  
When their requests are processed  
Then the system shall prevent the same time entry from appearing in two active invoices.

## Data Ownership

- Generate Invoices owns the creation of Invoice and InvoiceLineItem records.
- Generate Invoices owns BillingSchedule configuration in the initial design.
- Maintain Clients owns Client and ClientContact records.
- Maintain Projects owns Project records.
- Enter Project Time owns TimeEntry work details.
- Approve Project Time owns approval decisions.
- Maintain Billing Rates owns BillingRate records.
- Generate Invoices reads approved time and applicable rates without modifying their historical values.
- Manage Invoices owns invoice issuance, cancellation, and payment-status management.
- Only authorized accounting personnel may generate invoices.
- Other features shall not independently recalculate issued invoice amounts.

## API Requirements

**Proposed REST API endpoints**

| Method | Endpoint | Purpose |
|---|---|---|
| POST | /invoices/generate | Generate a draft invoice |
| GET | /invoices/:id | Retrieve invoice details |
| GET | /invoices/:id/line-items | Retrieve invoice line items |
| GET | /projects/:id/billing-schedule | Retrieve project billing schedule |
| PUT | /projects/:id/billing-schedule | Create or update billing schedule |
| GET | /projects/:id/billable-time | Retrieve approved, uninvoiced time |
| GET | /invoices/preview | Preview proposed invoice charges |

- All endpoints shall require authentication.
- Invoice generation shall require accounting authorization.
- The generation request shall identify the project and billing period.
- The system shall validate that the project and client exist.
- The system shall validate time-entry eligibility and applicable rates.
- Invalid requests shall return HTTP 400.
- Unauthenticated requests shall return HTTP 401.
- Authenticated users without permission shall receive HTTP 403.
- Missing resources shall return HTTP 404.
- Duplicate billing conflicts shall return HTTP 409.
- Invoice creation and time-entry billing reservations shall be processed atomically.

## Screen Requirements

**Invoice Generation Screen**

- Allow authorized accounting staff to select a project and billing period.
- Display the client and relevant billing schedule.
- Show eligible approved time entries.
- Provide an action to generate a Draft invoice.
- Display validation messages for missing rates or incomplete information.

**Invoice Preview Screen**

- Display client, project, invoice recipient, and billing period.
- Display proposed line items, hours, rates, and calculated charges.
- Display the proposed invoice total.
- Allow accounting staff to review the information before generating the invoice.

**Draft Invoice Detail Screen**

- Display the generated invoice number, client, project, billing period, and line items.
- Display invoice status and total amount.
- Provide access to the invoice management workflow.

**Billing Schedule Screen**

- Display the billing frequency configured for each project.
- Allow authorized users to maintain the project's schedule.
- Show the next planned billing date.

## Definition of Done

- Authorized accounting staff can generate draft invoices.
- Only approved, uninvoiced time is included.
- Billing rates are selected correctly by project, role, and work date.
- Invoice line items and totals are calculated accurately.
- Project-specific billing schedules are supported.
- Duplicate billing is prevented, including concurrent requests.
- Draft invoice preview and review are available.
- Invoice recipient information is associated correctly.
- Authentication and authorization are enforced.
- Invalid inputs and missing billing information are handled.
- Required API endpoints and screens are implemented and tested.
- Automated tests cover calculations, billing eligibility, schedules, historical rates, and duplicate prevention.
- Feature documentation is reviewed and complete.

## Out of Scope

- Creating or modifying client master records.
- Creating employee or project records.
- Entering or approving employee time.
- Maintaining billing rates.
- Issuing or delivering invoices to clients.
- Recording client payments.
- Automatically charging client payment methods.
- Payroll processing.
- Automatic tax calculation.
- Automatic currency conversion.
- Recalculating previously issued invoices.