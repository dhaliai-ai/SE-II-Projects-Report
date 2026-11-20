# Feature 1: Maintain Clients

## Header

- **Feature ID:** 1
- **Feature Name:** Maintain Clients
- **Branch:** invoicing-system
- **Status:** Draft
- **System:** Invoicing System
- **Purpose:** Allow authorized company personnel to maintain client information used for managing consulting projects and preparing invoices.

## User Stories

**US-1.1: Create a Client**

As an authorized administrator or office staff member, I want to create a client record so that the company can manage projects and billing information for that client.

**US-1.2: View Client Information**

As an authorized user, I want to view client information so that I can identify the correct client when working with projects and invoices.

**US-1.3: Update Client Information**

As an authorized administrator or office staff member, I want to update client information so that contact details and addresses remain accurate.

**US-1.4: Maintain Client Contacts**

As an authorized administrator or office staff member, I want to maintain client contacts so that project managers and accounting staff know whom to contact.

## Functional Requirements

- **FR-1.1:** The system shall allow authorized users to create client records.
- **FR-1.2:** Each client shall have a unique identifier and a name.
- **FR-1.3:** The system shall store a client's mailing address and office address.
- **FR-1.4:** The system shall maintain contact information associated with each client.
- **FR-1.5:** The system shall allow authorized users to update client information.
- **FR-1.6:** The system shall allow authorized users to retrieve and view client records.
- **FR-1.7:** The system shall prevent unauthorized users from modifying client information.
- **FR-1.8:** The system shall preserve client relationships with existing projects and invoices.

## Assumptions

- Client records are maintained by authorized administrative or office personnel.
- A client may have multiple projects.
- A client may have multiple contacts.
- Project-specific sponsors and contacts will be associated with projects through the Maintain Projects feature.
- Client records referenced by historical invoices must remain available.
- The exact authorization rules and required contact fields will be finalized during implementation.

## Edge Cases

- A user attempts to create a client without a name.
- A user enters invalid contact information.
- A user attempts to create a duplicate client record.
- An unauthorized user attempts to update client information.
- A client has multiple contacts.
- A client has existing projects or invoices when an administrator attempts to remove the client.

## Success Criteria

- Authorized users can create and update client records.
- Client records can be retrieved successfully.
- Client addresses and contacts are maintained accurately.
- Unauthorized modification attempts are rejected.
- Existing project and invoice relationships remain intact after client information is updated.

## Key Entities

**Client**
- Represents an organization receiving consulting services.

**ClientContact**
- Represents a person associated with a client.

**Project**
- Represents a consulting project belonging to a client.

**Invoice**
- Represents a billing record associated with a client.

## Initial Data Model

**Client**

| Field | Type | Description |
|---|---|---|
| clientId | Integer, PK | Unique client identifier |
| clientName | String | Client organization name |
| mailingAddress | String | Client mailing address |
| officeAddress | String | Client office address |
| createdAt | DateTime | Record creation timestamp |
| updatedAt | DateTime | Last modification timestamp |

**ClientContact**

| Field | Type | Description |
|---|---|---|
| contactId | Integer, PK | Unique contact identifier |
| clientId | Integer, FK | Associated client |
| contactName | String | Contact person's name |
| email | String | Contact email |
| phone | String | Contact phone number |

**Relationships**

- One Client may have many ClientContacts.
- One Client may have many Projects.
- One Client may be associated with many Invoices.
- Project and Invoice entities are maintained by their respective features.

## Gherkin AC

**Scenario 1: Create a client successfully**

Given an authorized user is signed in  
And the user provides valid client information  
When the user submits the new client form  
Then the system shall create a client record  
And assign a unique client identifier.

**Scenario 2: Reject a client without a name**

Given an authorized user is creating a client  
When the user submits the form without a client name  
Then the system shall reject the request  
And display a validation message.

**Scenario 3: Update client information**

Given an authorized user is signed in  
And an existing client record is available  
When the user updates the client's address  
Then the system shall save the updated information  
And preserve the client's existing project relationships.

**Scenario 4: Prevent unauthorized modification**

Given a user does not have permission to modify clients  
When the user attempts to update a client record  
Then the system shall reject the request  
And leave the client information unchanged.

**Scenario 5: Maintain a client contact**

Given an authorized user is viewing an existing client  
When the user adds a valid contact  
Then the system shall associate the contact with that client.

## Data Ownership

- The Maintain Clients feature owns the Client and ClientContact entities.
- The Maintain Projects feature references Client records through clientId.
- The Generate Invoices feature references clients but does not own client records.
- Only authorized administrative or office users may modify client information.
- Other authorized users may retrieve client information required for their responsibilities.

## API Requirements

**Proposed REST API endpoints**

| Method | Endpoint | Purpose |
|---|---|---|
| GET | /clients | Retrieve clients |
| GET | /clients/:id | Retrieve one client |
| POST | /clients | Create a client |
| PUT | /clients/:id | Update a client |
| GET | /clients/:id/contacts | Retrieve client contacts |
| POST | /clients/:id/contacts | Add a client contact |
| PUT | /clients/:id/contacts/:contactId | Update a contact |

- Requests must be authenticated.
- Write operations must enforce authorization.
- Invalid input must return an appropriate validation error.
- Missing client records must return HTTP 404.
- Unauthorized access must return an appropriate HTTP 401 or 403 response.

## Screen Requirements

**Client List Screen**

- Display existing clients.
- Allow authorized users to select a client.
- Provide access to client creation for authorized users.

**Client Detail Screen**

- Display client name, mailing address, office address, and contacts.
- Allow authorized users to edit permitted information.
- Display associated contact information.

**Client Form**

- Accept required client information.
- Validate required fields.
- Display clear validation and save-result messages.

## Definition of Done

- Client creation, retrieval, and update requirements are implemented.
- Client contact management is implemented.
- Input validation is implemented.
- Authentication and authorization are enforced.
- Client relationships with projects and invoices are preserved.
- Automated tests cover successful operations, invalid inputs, and unauthorized access.
- Required screens and API endpoints are tested.
- Feature documentation is complete and reviewed.

## Out of Scope

- Employee management.
- Project creation and employee assignments.
- Project-specific billing rate management.
- Employee time entry and approval.
- Invoice generation and payment processing.
- Automatic invoice delivery.
- Permanent deletion of clients with existing project or invoice relationships.