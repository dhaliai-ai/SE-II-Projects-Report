# Feature: Church Member and Class Management
**Branch pattern:** `feature/1-church-member-management`  
**Status:** Draft  
**Created:** 2026-09-10  
**Input:** Provide the church with a simple system for organizing member information, tracking member status, and supporting Bible class coordination.  
 **Related:** David North interview notes  

---

## User Stories

### US-1.1: Manage Church Member Information
**As a** church employee  
**I want to** store and update church member information  
**So that** the church can keep member records organized and accessible.

**Priority:** P1  
**Independent test:** Add a church member with the required information, save the record, and verify that the information can be retrieved and updated.  
**Acceptance scenarios:** see ### US-1.1 under Acceptance Criteria

### US-1.2: Track Member Status
**As a** church employee  
**I want to** mark members as active or inactive  
**So that** the church can keep track of people who are currently participating.

**Priority:** P1  
**Independent test:** Change a member from active to inactive and verify that the updated status is saved.  
**Acceptance scenarios:** see ### US-1.2 under Acceptance Criteria

### US-1.3: Manage Bible Class Information
**As a** church employee  
**I want to** organize Bible class information and assignments  
**So that** the church can coordinate classes when teachers or members are unavailable.

**Priority:** P1  
**Independent test:** Create a Bible class assignment and verify that the assigned information can be viewed.  
**Acceptance scenarios:** see ### US-1.3 under Acceptance Criteria

---

## Requirements

### Functional Requirements

- **FR-001**: The system MUST allow church employees to store member information.
- **FR-002**: The system MUST store a member's first name and last name.
- **FR-003**: The system MUST support storing a member's home address.
- **FR-004**: The system MUST support storing a member's phone number.
- **FR-005**: The system MUST support storing a member's email address.
- **FR-006**: Church employees MUST be able to update member information.
- **FR-007**: The system MUST allow a member to be marked as active or inactive.
- **FR-008**: The system MUST support Bible class information.
- **FR-009**: The system MUST support class assignments when a regular teacher is unavailable.
- **FR-010**: The system SHOULD support Spanish-language needs.

---

## Data Model Requirements

### `members` table

| Field | Type | Rules |
|-------|------|-------|
| `id` | INTEGER PK | Auto-increment |
| `first_name` | VARCHAR | Required |
| `last_name` | VARCHAR | Required |
| `home_address` | VARCHAR | Optional |
| `phone_number` | VARCHAR | Optional |
| `email_address` | VARCHAR | Optional |
| `status` | VARCHAR | Required; Active or Inactive |

### `bible_classes` table

| Field | Type | Rules |
|-------|------|-------|
| `id` | INTEGER PK | Auto-increment |
| `class_name` | VARCHAR | Required |
| `teacher_id` | INTEGER | References a member/person assigned to teach |

### `class_assignments` table

| Field | Type | Rules |
|-------|------|-------|
| `id` | INTEGER PK | Auto-increment |
| `class_id` | INTEGER | Required |
| `member_id` | INTEGER | Required |
| `role` | VARCHAR | Identifies the person's class role |

### Associations

- A member may participate in a Bible class.
- A Bible class may contain multiple members.
- A Bible class may have an assigned teacher.
- A class assignment connects a member with a Bible class.

---

## Acceptance Criteria

### US-1.1 — Manage Church Member Information

#### Scenario: Add a new church member
* **Given** a church employee is entering a new member
* **When** the employee enters the member's first name, last name, and available contact information
* **Then** the system saves the member record
* **And** the employee can view the saved member information

#### Scenario: Update member information
* **Given** an existing member record
* **When** a church employee changes the member's contact information
* **Then** the system saves the updated information
* **And** the updated information is displayed when the record is viewed again

#### Scenario: Missing required member information
* **Given** a church employee is creating a member
* **When** the required name information is missing
* **Then** the system does not create the member record
* **And** the employee is informed that required information is missing

---

### US-1.2 — Track Member Status

#### Scenario: Mark a member inactive
* **Given** an active church member
* **When** a church employee changes the member's status to inactive
* **Then** the system saves the member as inactive
* **And** the member's existing information remains available

#### Scenario: Reactivate a member
* **Given** an inactive church member
* **When** a church employee changes the member's status to active
* **Then** the system saves the member as active

---

### US-1.3 — Manage Bible Class Information

#### Scenario: Assign a person to a Bible class
* **Given** a Bible class exists
* **When** a church employee assigns a person to the class
* **Then** the system saves the class assignment
* **And** the assignment can be viewed by the employee

#### Scenario: Regular teacher is unavailable
* **Given** a Bible class whose regular teacher is unavailable
* **When** a church employee assigns another appropriate person to the class
* **Then** the system records the replacement assignment
* **And** the class remains assigned to a teacher