# Feature: Maintain Teams

**Feature ID:** 2  
**Feature Name:** Maintain Teams  
**Status:** Draft  
**Branch:** `soccer-league`  
**Created:** 2026-10-19

---

## User Stories

### US-2.1: Add Team

**As a** League Owner  
**I want to** add a team to the league system  
**So that** the team can participate in league competitions.

**Priority:** P1

### US-2.2: Edit Team

**As a** League Owner  
**I want to** update a team's information  
**So that** the system maintains accurate team records.

**Priority:** P1

### US-2.3: Assign Team to Season

**As a** League Owner  
**I want to** assign an existing team to a league season  
**So that** the team can participate in scheduled competitions.

**Priority:** P1

### US-2.4: Deactivate Team

**As a** League Owner  
**I want to** deactivate a team that is no longer participating  
**So that** the team is excluded from future competitions while its historical records remain available.

**Priority:** P1

### US-2.5: View Teams

**As a** Public Visitor  
**I want to** view teams participating in a league  
**So that** I can follow their participation and results.

**Priority:** P1

---

## Functional Requirements

- **FR-001:** The system shall allow the League Owner to add a team.
- **FR-002:** The system shall maintain a unique identifier for each team.
- **FR-003:** The system shall record the team's name and status.
- **FR-004:** The system shall allow the League Owner to edit team information.
- **FR-005:** The system shall allow a team to participate in multiple seasons without creating duplicate team records.
- **FR-006:** The system shall associate each season participation record with a specific league season.
- **FR-007:** The system shall allow the League Owner to mark a team as inactive.
- **FR-008:** The system shall preserve historical games, scores, and season participation records when a team becomes inactive.
- **FR-009:** The system shall prevent inactive teams from being assigned to new seasons unless they are reactivated.
- **FR-010:** The system shall allow Public Visitors to view participating teams without modifying team information.
- **FR-011:** The system shall restrict team creation, editing, and deactivation to authorized users.

---

## Key Entities


| Entity      | Description                                           |
| ----------- | ----------------------------------------------------- |
| Team        | Represents a team registered in the league system     |
| League      | Represents an organized sports competition            |
| Season      | Represents a competition period                       |
| TeamSeason  | Associates a team with a particular league season     |
| TeamManager | Represents the person responsible for managing a team |


---

## Initial Data Model

### Team


| Attribute | Type    | Description                     |
| --------- | ------- | ------------------------------- |
| teamId    | Integer | Unique team identifier          |
| name      | String  | Team name                       |
| status    | String  | Active or inactive              |
| managerId | Integer | Reference to the team's manager |


### TeamManager


| Attribute | Type    | Description               |
| --------- | ------- | ------------------------- |
| managerId | Integer | Unique manager identifier |
| name      | String  | Manager's name            |
| email     | String  | Manager's contact email   |
| phone     | String  | Manager's phone number    |


### TeamSeason


| Attribute    | Type    | Description                     |
| ------------ | ------- | ------------------------------- |
| teamSeasonId | Integer | Unique participation identifier |
| teamId       | Integer | Reference to Team               |
| seasonId     | Integer | Reference to Season             |


### Season


| Attribute | Type    | Description              |
| --------- | ------- | ------------------------ |
| seasonId  | Integer | Unique season identifier |
| leagueId  | Integer | Reference to League      |
| name      | String  | Season name              |
| startDate | Date    | Season start date        |
| endDate   | Date    | Season end date          |


**Relationships:**

- A Team can participate in multiple Seasons.
- A Season can contain multiple Teams.
- TeamSeason represents the many-to-many relationship between Team and Season.
- A Team has an assigned Team Manager.
- A Season belongs to one League.
- Historical TeamSeason records remain associated with their Team even after deactivation.

---

## Gherkin AC

### US-2.1: Add Team

#### AC-2.1.1: Successfully add a team

**Given** the League Owner is authenticated  
**And** valid team information is provided  
**When** the League Owner saves a new team  
**Then** the system creates the team record  
**And** assigns a unique team identifier  
**And** makes the team available for season assignment.

#### AC-2.1.2: Reject missing team name

**Given** the League Owner is adding a team  
**When** the League Owner attempts to save without a team name  
**Then** the system displays a validation message  
**And** does not create the team.

### US-2.2: Edit Team

#### AC-2.2.1: Successfully update team information

**Given** an existing team is recorded  
**And** the League Owner is authenticated  
**When** the League Owner updates the team's information  
**Then** the system saves the changes  
**And** displays the updated team information.

### US-2.3: Assign Team to Season

#### AC-2.3.1: Assign an active team to a season

**Given** an active team exists  
**And** a league season exists  
**When** the League Owner assigns the team to that season  
**Then** the system creates a TeamSeason record  
**And** displays the team as participating in the season.

#### AC-2.3.2: Prevent duplicate season assignment

**Given** a team is already assigned to a particular season  
**When** the League Owner attempts to assign the same team again  
**Then** the system rejects the duplicate assignment  
**And** preserves the existing participation record.

### US-2.4: Deactivate Team

#### AC-2.4.1: Deactivate a team without deleting history

**Given** a team has participated in previous seasons  
**And** the League Owner is authenticated  
**When** the League Owner deactivates the team  
**Then** the system marks the team as inactive  
**And** preserves its previous game results and participation records.

#### AC-2.4.2: Prevent inactive team assignment

**Given** a team is inactive  
**When** the League Owner attempts to assign the team to a new season  
**Then** the system prevents the assignment  
**And** indicates that the team must first be reactivated.

### US-2.5: View Teams

#### AC-2.5.1: Public visitor views participating teams

**Given** a league season contains registered teams  
**When** a Public Visitor opens the team list  
**Then** the system displays the participating teams  
**And** does not provide team modification controls.

---

## Screen Requirements

### SR-001: Team List Screen

**Authorized users:** League Owner, Public Visitor

**Display:**

- Team name
- Team manager
- Team status
- Participating season or seasons

**Actions:**

- View Team
- Add Team (League Owner only)
- Edit Team (League Owner only)
- Deactivate Team (League Owner only)
- Close

### SR-002: Team Edit Screen

**Authorized user:** League Owner

**Fields:**

- Team Name
- Team Manager
- Team Status

**Actions:**

- Save
- Cancel

**Behavior:**

- The system shall display existing team information when editing.
- Required fields shall be validated before saving.
- Cancel shall discard unsaved changes.

### SR-003: Season Team Assignment Screen

**Authorized user:** League Owner

**Display:**

- League name
- Season name
- Available active teams
- Teams already assigned to the season

**Actions:**

- Assign Team
- Remove Team from Season (subject to historical record rules)
- Close

**Behavior:**

- The system shall prevent duplicate team assignments.
- Inactive teams shall not be available for new assignments.

### SR-004: Team Details Screen

**Authorized users:** League Owner, Public Visitor

**Display:**

- Team name
- Team manager
- Team status
- Season participation history
- Available historical results

**Actions:**

- Return to Team List

