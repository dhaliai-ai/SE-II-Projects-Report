# Feature: Maintain Game Officials

**Feature ID:** 7  
**Feature Name:** Maintain Game Officials  
**Status:** Draft  
**Branch:** `soccer-league`  
**Created:** 2026-10-19

---

## User Stories

### US-7.1: Add Game Official

**As a** League Owner  
**I want to** register game officials in the system  
**So that** I can maintain a list of officials who may be assigned to league games.

**Priority:** P1

### US-7.2: Update Game Official

**As a** League Owner  
**I want to** update an official's information  
**So that** contact details and other relevant information remain accurate.

**Priority:** P1

### US-7.3: View Game Officials

**As a** League Owner  
**I want to** view a list of registered officials  
**So that** I can find officials and review their information when organizing games.

**Priority:** P1

### US-7.4: Deactivate Game Official

**As a** League Owner  
**I want to** mark an official as inactive  
**So that** the official is no longer available for new game assignments while previous assignments are preserved.

**Priority:** P2

### US-7.5: View Official Assignment History

**As a** League Owner  
**I want to** view an official's previous game assignments  
**So that** I can review which games the official has worked.

**Priority:** P2

---

## Functional Requirements

- **FR-001:** The system shall allow only the League Owner to add game officials.
- **FR-002:** The system shall assign a unique identifier to each game official.
- **FR-003:** The system shall record each official's name and contact information.
- **FR-004:** The system shall allow the League Owner to update an official's information.
- **FR-005:** The system shall display a list of registered game officials.
- **FR-006:** The system shall allow the League Owner to search for officials by name.
- **FR-007:** The system shall record whether an official is Active or Inactive.
- **FR-008:** The system shall allow the League Owner to change an official's status.
- **FR-009:** The system shall exclude inactive officials from selection for new game assignments.
- **FR-010:** The system shall preserve historical game assignments when an official becomes inactive.
- **FR-011:** The system shall allow the League Owner to view games previously assigned to an official.
- **FR-012:** The system shall prevent unauthorized users from adding, modifying, or deactivating officials.
- **FR-013:** The system shall require an official's name before saving a new record.
- **FR-014:** The system shall retain official records that are referenced by historical game assignments.
- **FR-015:** The system shall make active officials available to the game assignment functionality defined in Feature 4.



---

## Key Entities

| Entity | Description |
|---|---|
| Official | Represents a registered person who may officiate league games |
| Game | Represents a scheduled or completed game |
| GameOfficial | Associates an official with a particular game |
| League | Represents the sports organization maintaining the officials |

---

## Initial Data Model

### Official

| Attribute | Type | Description |
|---|---|---|
| officialId | Integer | Unique official identifier |
| name | String | Official's full name |
| phone | String | Contact phone number |
| email | String | Contact email address |
| status | String | Active or Inactive |

### GameOfficial (Existing Entity from Feature 4)

| Attribute | Type | Description |
|---|---|---|
| gameOfficialId | Integer | Unique assignment identifier |
| gameId | Integer | Reference to Game |
| officialId | Integer | Reference to Official |
| role | String | Official's role for the game |

### Game (Existing Entity from Feature 4)

| Attribute | Type | Description |
|---|---|---|
| gameId | Integer | Unique game identifier |
| seasonId | Integer | Reference to Season |
| gameDate | Date | Scheduled date |
| startTime | Time | Scheduled start time |
| locationId | Integer | Reference to Location |
| status | String | Scheduled, Completed, or Canceled |

**Relationships:**

- An Official may be assigned to multiple Games.
- A Game may have multiple Officials.
- GameOfficial represents the many-to-many relationship between Official and Game.
- Each GameOfficial references exactly one Official and one Game.
- An inactive Official retains existing GameOfficial records.
- The League Owner manages the official registry.

**Data model note:** This feature reuses the `Official` and `GameOfficial` entities introduced in Feature 4. It does not create a second official entity.

---

## Gherkin AC

### US-7.1: Add Game Official

#### AC-7.1.1: Successfully add an official

**Given** the League Owner is authenticated  
**And** the League Owner is on the Maintain Game Officials screen  
**When** the League Owner enters the official's name and valid contact information  
**And** selects Save  
**Then** the system creates a new Official record  
**And** assigns a unique officialId  
**And** displays the official in the officials list.

#### AC-7.1.2: Reject missing official name

**Given** the League Owner is adding an official  
**When** the League Owner leaves the name field empty  
**And** selects Save  
**Then** the system displays a validation message  
**And** does not create the Official record.

### US-7.2: Update Game Official

#### AC-7.2.1: Successfully update official information

**Given** an Official record exists  
**And** the League Owner is authenticated  
**When** the League Owner selects the official  
**And** changes the official's contact information  
**And** selects Save  
**Then** the system updates the Official record  
**And** displays the updated information.

#### AC-7.2.2: Reject unauthorized update

**Given** a Team Manager is authenticated  
**When** the Team Manager attempts to modify an Official record  
**Then** the system denies the modification  
**And** preserves the existing information.

### US-7.3: View Game Officials

#### AC-7.3.1: Display registered officials

**Given** one or more Official records exist  
**And** the League Owner is authenticated  
**When** the League Owner opens the Maintain Game Officials screen  
**Then** the system displays the registered officials  
**And** shows their names, contact information, and statuses.

#### AC-7.3.2: Search for an official

**Given** multiple Official records exist  
**When** the League Owner searches using an official's name  
**Then** the system displays matching Official records.

### US-7.4: Deactivate Game Official

#### AC-7.4.1: Successfully deactivate an official

**Given** an active Official record exists  
**And** the League Owner is authenticated  
**When** the League Owner changes the official's status to Inactive  
**And** saves the changes  
**Then** the system updates the Official record  
**And** excludes the official from new game assignment selections  
**And** retains the official's previous game assignments.

#### AC-7.4.2: Reactivate an official

**Given** an inactive Official record exists  
**And** the League Owner is authenticated  
**When** the League Owner changes the official's status to Active  
**And** saves the changes  
**Then** the system updates the Official record  
**And** makes the official available for new game assignments.

### US-7.5: View Official Assignment History

#### AC-7.5.1: Display previous game assignments

**Given** an Official has been assigned to one or more Games  
**And** the League Owner is authenticated  
**When** the League Owner selects View Assignment History  
**Then** the system displays the official's recorded game assignments  
**And** includes the game date, participating teams, and assigned role.

#### AC-7.5.2: Preserve assignment history after deactivation

**Given** an Official has previous GameOfficial records  
**When** the League Owner deactivates the official  
**Then** the system retains those assignment records  
**And** allows the League Owner to view them.

---

## Screen Requirements

### SR-001: Game Officials List Screen

**Authorized user:** League Owner

**Display:**
- Official ID
- Official name
- Phone number
- Email address
- Status

**Actions:**
- Add Official
- Edit Official
- Search Official
- View Assignment History
- Close

**Behavior:**
- The system shall display registered officials.
- The League Owner shall be able to select an official for editing.
- The system shall support searching by official name.
- Unauthorized users shall not have access to maintenance actions.

### SR-002: Game Official Edit Screen

**Authorized user:** League Owner

**Fields:**
- Official ID (read-only after creation)
- Official Name
- Phone Number
- Email Address
- Status

**Actions:**
- Save
- Cancel

**Behavior:**
- The system shall require an official's name.
- The system shall validate the provided contact information.
- Save shall create or update the Official record.
- Cancel shall discard unsaved changes.
- Setting an official to Inactive shall not delete historical assignments.

### SR-003: Official Assignment History Screen

**Authorized user:** League Owner

**Display:**
- Official name
- Game date
- Home team
- Away team
- Game location
- Assigned role
- Game status

**Actions:**
- View Game Details
- Return to Officials List

**Behavior:**
- The system shall retrieve game assignments associated with the selected official.
- Historical assignments shall remain visible even when the official is inactive.
- This screen shall be read-only.

### SR-004: Active Official Selection

**Authorized user:** League Owner

**Used by:** Feature 4 — Manage Game Schedules

**Display:**
- Active official names
- Official IDs
- Assignment roles

**Actions:**
- Select Official
- Cancel

**Behavior:**
- Only Active officials shall be available for new assignments.
- Selecting an official shall return the selected official to Feature 4's assignment screen.
- The actual creation and modification of game assignments shall remain the responsibility of Feature 4.