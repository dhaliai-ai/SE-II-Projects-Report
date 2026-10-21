# Feature: Maintain Players and Team Rosters

**Feature ID:** 3  
**Feature Name:** Maintain Players and Team Rosters  
**Status:** Draft  
**Branch:** `soccer-league`  
**Created:** 2026-10-19

---

## User Stories

### US-3.1: Add Player

**As a** Team Manager  
**I want to** register players in the system  
**So that** I can maintain an accurate list of players on my team.

**Priority:** P1

### US-3.2: Edit Player Information

**As a** Team Manager  
**I want to** update player information for my team  
**So that** player records remain accurate.

**Priority:** P1

### US-3.3: Manage Team Roster

**As a** Team Manager  
**I want to** add and remove players from my team's current roster  
**So that** the roster reflects the players participating in the season.

**Priority:** P1

### US-3.4: View Team Roster

**As a** Public Visitor  
**I want to** view a team's published roster  
**So that** I can see which players are participating.

**Priority:** P1

### US-3.5: Preserve Historical Rosters

**As a** League Owner  
**I want to** retain previous season rosters  
**So that** historical player participation remains available.

**Priority:** P2

---

## Functional Requirements

- **FR-001:** The system shall allow authorized Team Managers to register players.
- **FR-002:** The system shall assign a unique identifier to each player.
- **FR-003:** The system shall store each player's name and relevant roster information.
- **FR-004:** The system shall allow Team Managers to update player information for their assigned teams.
- **FR-005:** The system shall associate players with teams for a specific season.
- **FR-006:** The system shall allow authorized Team Managers to add players to their team's current season roster.
- **FR-007:** The system shall allow authorized Team Managers to remove players from the current roster without deleting historical participation records.
- **FR-008:** The system shall prevent duplicate roster entries for the same player, team, and season.
- **FR-009:** The system shall restrict Team Managers from modifying rosters belonging to other teams.
- **FR-010:** The system shall allow Public Visitors to view published team rosters without modifying them.
- **FR-011:** The system shall preserve previous season roster records.
- **FR-012:** The system shall associate roster entries with the appropriate TeamSeason record.


---

## Key Entities


| Entity      | Description                                                           |
| ----------- | --------------------------------------------------------------------- |
| Player      | Represents an individual registered in the league system              |
| Team        | Represents a participating team                                       |
| TeamManager | Represents an authorized manager responsible for a team               |
| Season      | Represents a competition period                                       |
| TeamSeason  | Represents a team's participation in a season                         |
| RosterEntry | Represents a player's membership on a team during a particular season |


---

## Initial Data Model

### Player


| Attribute | Type    | Description              |
| --------- | ------- | ------------------------ |
| playerId  | Integer | Unique player identifier |
| firstName | String  | Player's first name      |
| lastName  | String  | Player's last name       |


### RosterEntry


| Attribute     | Type    | Description                           |
| ------------- | ------- | ------------------------------------- |
| rosterEntryId | Integer | Unique roster entry identifier        |
| playerId      | Integer | Reference to Player                   |
| teamSeasonId  | Integer | Reference to TeamSeason               |
| jerseyNumber  | Integer | Player's jersey number, if applicable |
| status        | String  | Active or removed                     |


### TeamSeason


| Attribute    | Type    | Description                   |
| ------------ | ------- | ----------------------------- |
| teamSeasonId | Integer | Unique team-season identifier |
| teamId       | Integer | Reference to Team             |
| seasonId     | Integer | Reference to Season           |


### TeamManager


| Attribute | Type    | Description               |
| --------- | ------- | ------------------------- |
| managerId | Integer | Unique manager identifier |
| name      | String  | Team Manager's name       |
| email     | String  | Manager's email address   |


**Relationships:**

- A Team can have multiple TeamSeason records.
- A Season can have multiple TeamSeason records.
- A TeamSeason can have multiple RosterEntries.
- A Player can have multiple RosterEntries across seasons.
- Each RosterEntry belongs to one Player and one TeamSeason.
- A Team Manager is authorized to maintain rosters for assigned teams.

---

## Gherkin AC

### US-3.1: Add Player

#### AC-3.1.1: Successfully register a player

**Given** a Team Manager is authenticated  
**And** the Team Manager is authorized to manage the selected team  
**When** the Team Manager enters valid player information and saves  
**Then** the system creates a player record  
**And** assigns a unique player identifier.

#### AC-3.1.2: Reject missing player name

**Given** a Team Manager is registering a player  
**When** the Team Manager submits the form without the required player name  
**Then** the system displays a validation message  
**And** does not create the player record.

### US-3.2: Edit Player Information

#### AC-3.2.1: Update player information

**Given** a player belongs to a team managed by the authenticated Team Manager  
**When** the Team Manager edits and saves the player's information  
**Then** the system updates the player record  
**And** displays the updated information.

### US-3.3: Manage Team Roster

#### AC-3.3.1: Add player to roster

**Given** a Team Manager is authenticated  
**And** a TeamSeason record exists for the manager's team  
**And** the selected player is not already on that roster  
**When** the Team Manager adds the