# Feature 8: Manage Invoices

## Header

- **Feature ID:** 8
- **Feature Name:** Manage Invoices
- **System:** Invoicing System
- **Branch:** invoicing-system
- **Status:** Draft
- **Purpose:** Allow authorized accounting personnel to review, issue, deliver, track, and manage client invoices while preserving accurate billing records.

## User Stories

**US-8.1: View Invoices**

As an accounting staff member, I want to view existing invoices so that I can monitor client billing.

**US-8.2: Issue an Invoice**

As an accounting staff member, I want to issue an approved draft invoice so that the client can be billed for completed work.

**US-8.3: Deliver an Invoice**

As an accounting staff member, I want to prepare an invoice for delivery to the appropriate client contact so that the client can process payment.

**US-8.4: Track Invoice Status**

As an accounting staff member, I want to track invoice statuses so that I know which invoices have been issued, paid, or cancelled.

**US-8.5: Record Payment Status**

As an accounting staff member, I want to mark an invoice as paid when payment is confirmed so that billing records remain accurate.

**US-8.6: Review Invoice History**

As an owner or authorized administrator, I want to review historical invoices so that I can monitor company billing activity.

**US-8.7: Cancel an Invoice**

As an authorized accounting staff member, I want to cancel an invoice when necessary so that incorrect invoices are not treated as outstanding charges.

## Functional Requirements

- **FR-8.1:** The system shall allow authorized users to retrieve existing invoices.
- **FR-8.2:** The system shall display invoice number, client, project, billing period, total amount, and status.
- **FR-8.3:** The system shall allow authorized accounting personnel to review draft invoices.
- **FR-8.4:** The system shall allow authorized accounting personnel to issue valid draft invoices.
- **FR-8.5:** The system shall record the date an invoice is issued.
- **FR-8.6:** The system shall identify the appropriate client contact for invoice delivery.
- **FR-8.7:** The system shall support generating a printable or downloadable invoice document.
- **FR-8.8:** The system shall allow authorized accounting personnel to record invoice delivery information.
- **FR-8.9:** The system shall maintain invoice statuses.
- **FR-8.10:** The system shall allow authorized accounting personnel to mark an issued invoice as paid when payment is confirmed.
- **FR-8.11:** The system shall record the date payment is confirmed.
- **FR-8.12:** The system shall allow authorized accounting personnel to cancel an eligible invoice.
- **FR-8.13:** The system shall require a reason when cancelling an invoice.
- **FR-8.14:** The system shall preserve historical invoice details and billing amounts.
- **FR-8.15:** The system shall prevent unauthorized changes to invoice information.
- **FR-8.16:** The system shall prevent invalid invoice status transitions.
- **FR-8.17:** The system shall allow authorized users to search or filter invoices by client, project, date, or status.
- **FR-8.18:** The system shall prevent ordinary editing of issued invoice line amounts.
- **FR-8.19:** The system shall maintain an audit history of invoice lifecycle changes.

## Assumptions

- Accounting personnel are responsible for issuing and tracking client invoices.
- Invoice generation initially creates a Draft invoice.
- Draft invoices are reviewed before being issued.
- Invoices are delivered to the contact associated with the project or client.
- The previous manual process used Word documents and mail or fax.
- Downloadable or printable invoice documents are proposed to support delivery.
- Electronic delivery may be added later if approved.
- Invoice statuses initially include Draft, Issued, Paid, and Cancelled.
- Accounting records payment confirmation but does not process payment transactions within this feature.
- Payment dates are recorded when an invoice is marked Paid.
- Invoice cancellation requires an explanation.
- Issued invoice amounts are preserved for historical accuracy.
- The exact rules for correcting issued invoices and releasing cancelled invoice time entries require confirmation.

## Edge Cases

- An accounting user attempts to issue an invoice without line items.
- An invoice has no designated recipient.
- An invoice is issued twice.
- An unauthorized employee attempts to issue an invoice.
- An accounting user attempts to mark a Draft invoice as Paid.
- An invoice is marked Paid more than once.
- An accounting user attempts to cancel an already Paid invoice.
- An invoice is cancelled after being issued.
- A user attempts to modify an issued invoice amount.
- An invoice document cannot be generated.
- A client has several invoices for different projects.
- An invoice has passed its due date but remains unpaid.
- An accounting user searches for an invoice that does not exist.
- Two accounting users attempt to change the same invoice simultaneously.
- A cancelled invoice contains time entries that might need to be billed again.
- A user attempts to delete an invoice needed for historical reporting.

## Success Criteria

- Authorized accounting staff can retrieve and review invoices.
- Draft invoices can be issued after validation.
- Invoice documents can be prepared for client delivery.
- Invoice delivery information can be recorded.
- Invoice statuses are accurately maintained.
- Payment confirmation can be recorded.
- Eligible invoices can be cancelled with a reason.
- Unauthorized actions and invalid status transitions are rejected.
- Historical invoice amounts remain unchanged.
- Accounting can search and filter invoice records.
- Invoice lifecycle changes remain auditable.

## Key Entities

**Invoice**
- Represents a client billing document and its current lifecycle status.

**InvoiceLineItem**
- Represents an individual charge included in an invoice.

**Client**
- Represents the organization receiving the invoice.

**ClientContact**
- Represents the intended invoice recipient.

**Project**
- Represents the consulting engagement being billed.

**InvoiceStatusHistory**
- Represents a recorded invoice status change.

**InvoiceDelivery**
- Represents a recorded invoice delivery event.

## Initial Data Model

**Existing Invoice Fields Used**

| Field | Type | Description |
|---|---|---|
| invoiceId | Integer, PK | Unique invoice identifier |
| invoiceNumber | String, Unique | Invoice number |
| clientId | Integer, FK | Client being billed |
| projectId | Integer, FK | Project being billed |
| contactId | Integer, FK, Nullable | Intended recipient |
| invoiceDate | Date, Nullable | Issue date |
| dueDate | Date, Nullable | Payment due date |
| status | Enum | Draft, Issued, Paid, or Cancelled |
| totalAmount | Decimal(12,2) | Total invoice amount |

**Additional Invoice Fields**

| Field | Type | Description |
|---|---|---|
| paidAt | DateTime, Nullable | Payment confirmation timestamp |
| cancelledAt | DateTime, Nullable | Cancellation timestamp |
| cancellationReason | Text, Nullable | Reason for cancellation |

**InvoiceStatusHistory**

| Field | Type | Description |
|---|---|---|
| statusHistoryId | Integer, PK | Unique history identifier |
| invoiceId | Integer, FK | Associated invoice |
| previousStatus | String | Status before the change |
| newStatus | String | Status after the change |
| changedBy | Integer, FK | User making the change |
| changedAt | DateTime | Change timestamp |
| reason | Text, Nullable | Explanation for the change |

**InvoiceDelivery**

| Field | Type | Description |
|---|---|---|
| deliveryId | Integer, PK | Unique delivery identifier |
| invoiceId | Integer, FK | Associated invoice |
| contactId | Integer, FK | Recipient contact |
| deliveryMethod | String | Method used for delivery |
| deliveredAt | DateTime | Delivery timestamp |
| recordedBy | Integer, FK | User recording the delivery |

**Relationships**

- One Invoice has many InvoiceLineItems.
- One Invoice may have many InvoiceStatusHistory records.
- One Invoice may have many InvoiceDelivery records.
- One Client may have many Invoices.
- One Project may have many Invoices.
- One ClientContact may receive many Invoices.
- Generate Invoices creates Invoice and InvoiceLineItem records.
- Manage Invoices controls invoice lifecycle status changes and owns InvoiceStatusHistory and InvoiceDelivery records.

## Gherkin AC

**Scenario 1: View existing invoices**

Given an accounting staff member is authenticated  
And invoices exist in the system  
When the staff member opens the invoice list  
Then the system shall display the permitted invoices  
And show invoice numbers, clients, projects, amounts, and statuses.

**Scenario 2: Issue a draft invoice**

Given an accounting staff member is authenticated  
And a valid Draft invoice exists with billable line items  
When the staff member issues the invoice  
Then the system shall change its status to Issued  
And record the issue date  
And preserve the invoice amounts.

**Scenario 3: Prevent issuing an empty invoice**

Given an accounting staff member is reviewing a Draft invoice  
And the invoice has no billable line items  
When the staff member attempts to issue the invoice  
Then the system shall reject the request  
And leave the invoice in Draft status.

**Scenario 4: Prepare an invoice document**

Given an authorized accounting staff member opens an existing invoice  
When the staff member requests a printable invoice document  
Then the system shall generate a document containing the client, project, invoice number, billing details, and total amount.

**Scenario 5: Record invoice delivery**

Given an Issued invoice exists  
And a valid client contact is associated with the invoice  
When an accounting staff member records the delivery method and date  
Then the system shall save the delivery information  
And associate it with the invoice.

**Scenario 6: Mark an invoice as paid**

Given an accounting staff member is authenticated  
And an Issued invoice exists  
And payment has been confirmed  
When the staff member marks the invoice as Paid  
Then the system shall update its status to Paid  
And record the payment confirmation timestamp.

**Scenario 7: Prevent invalid payment status**

Given a Draft invoice exists  
When an accounting staff member attempts to mark it as Paid  
Then the system shall reject the request  
And preserve the Draft status.

**Scenario 8: Cancel an invoice**

Given an authorized accounting staff member is authenticated  
And an eligible invoice exists  
When the staff member cancels the invoice with a valid reason  
Then the system shall update its status to Cancelled  
And record the cancellation reason and timestamp.

**Scenario 9: Prevent unauthorized invoice changes**

Given an employee is authenticated without invoice management permission  
When the employee attempts to issue, cancel, or mark an invoice Paid  
Then the system shall reject the request  
And preserve the existing invoice information.

**Scenario 10: Preserve historical invoice amounts**

Given an invoice has been issued  
When a billing rate changes after issuance  
Then the invoice shall retain its original line-item rates and total amount.

**Scenario 11: Search invoices by status**

Given an accounting staff member is authenticated  
And invoices exist with different statuses  
When the staff member filters invoices by Issued status  
Then the system shall display the permitted invoices matching that status.

**Scenario 12: Preserve invoice status history**

Given an invoice changes from Draft to Issued  
When the status change is saved  
Then the system shall record the previous status, new status, responsible user, and timestamp.

## Data Ownership

- Generate Invoices owns the initial creation of Invoice and InvoiceLineItem records.
- Manage Invoices owns InvoiceStatusHistory and InvoiceDelivery records.
- Manage Invoices controls permitted lifecycle changes to Invoice records.
- Maintain Clients owns Client and ClientContact records.
- Maintain Projects owns Project records.
- Maintain Billing Rates owns BillingRate records.
- Manage Invoices references invoice details without modifying historical approved time or billing rates.
- Authorized accounting personnel may issue, cancel, and update payment status for eligible invoices.
- Owners and authorized administrators may retrieve invoice history according to their permissions.
- Other features shall not independently modify issued invoice amounts or lifecycle history.

## API Requirements

**Proposed REST API endpoints**

| Method | Endpoint | Purpose |
|---|---|---|
| GET | /invoices | Retrieve permitted invoices |
| GET | /invoices/:id | Retrieve invoice details |
| POST | /invoices/:id/issue | Issue a Draft invoice |
| POST | /invoices/:id/cancel | Cancel an eligible invoice |
| POST | /invoices/:id/mark-paid | Record payment confirmation |
| GET | /invoices/:id/document | Retrieve a printable invoice document |
| POST | /invoices/:id/deliveries | Record invoice delivery |
| GET | /invoices/:id/deliveries | Retrieve delivery history |
| GET | /invoices/:id/status-history | Retrieve invoice status history |

- All endpoints shall require authentication.
- Invoice lifecycle changes shall require accounting authorization.
- The invoice list endpoint shall support permitted filters for client, project, date, and status.
- Issue requests shall validate that the invoice contains valid line items.
- Cancellation requests shall include a nonblank reason.
- Invalid input shall return HTTP 400.
- Unauthenticated requests shall return HTTP 401.
- Authenticated users without permission shall receive HTTP 403.
- Missing records shall return HTTP 404.
- Invalid status transitions shall return HTTP 409.
- Status updates and corresponding history records shall be saved atomically.
- Issued invoice amounts shall not be modified by ordinary status-management operations.

## Screen Requirements

**Invoice List Screen**

- Display invoice number, client, project, invoice date, total amount, and status.
- Allow authorized users to search and filter invoices.
- Allow selection of an invoice for detailed review.

**Invoice Detail Screen**

- Display client, project, recipient, billing period, line items, and total.
- Display current invoice status.
- Provide permitted issue, cancel, and mark-paid actions.
- Display validation and action-result messages.

**Invoice Document View**

- Display a printable representation of the invoice.
- Include the invoice number, client information, project, billing period, charges, and total.
- Support downloading or printing the invoice.

**Invoice Delivery Screen**

- Display the intended recipient.
- Allow accounting personnel to record delivery method and date.
- Display previous delivery records.

**Invoice History Screen**

- Display invoice status changes.
- Show the previous status, new status, responsible user, and timestamp.
- Display cancellation reasons when applicable.

## Definition of Done

- Authorized users can retrieve and review invoice records.
- Valid Draft invoices can be issued.
- Printable invoice documents can be generated.
- Invoice delivery information can be recorded.
- Payment confirmation and cancellation operations are supported.
- Invalid status transitions are rejected.
- Historical invoice amounts are preserved.
- Invoice status changes are recorded for auditing.
- Invoice search and filtering are implemented.
- Authentication and authorization checks are enforced.
- Required API endpoints and screens are implemented and tested.
- Automated tests cover issuance, delivery records, payment status, cancellation, authorization, and history.
- Feature documentation is reviewed and complete.

## Out of Scope

- Creating or modifying client master records.
- Creating employee or project records.
- Recording or approving project time.
- Maintaining billing rates.
- Generating invoice charges from approved time.
- Processing credit cards or bank transfers.
- Full accounts-receivable accounting.
- Automated payment collection.
- Automatic email or fax delivery.
- Payroll processing.
- Automatic tax calculation.
- Retroactively changing issued invoice line amounts.