# Feature: Manage Game Schedules

**Feature ID:** 4  
**Feature Name:** Manage Game Schedules  
**Status:** Draft  
**Branch:** `soccer-league`  
**Created:** 2026-10-19

---

## User Stories

### US-4.1: Create Game Schedule

**As a** League Owner  
**I want to** schedule games between participating teams  
**So that** league competitions can take place in an organized manner.

**Priority:** P1

### US-4.2: Update Game Schedule

**As a** League Owner  
**I want to** change the date, time, or location of a scheduled game  
**So that** I can accommodate necessary scheduling changes.

**Priority:** P1

### US-4.3: Assign Game Officials

**As a** League Owner  
**I want to** assign officials to scheduled games  
**So that** each game has the appropriate officials.

**Priority:** P1

### US-4.4: View Game Schedule

**As a** Public Visitor  
**I want to** view upcoming games and their locations  
**So that** I can follow the league and attend games.

**Priority:** P1

### US-4.5: Cancel Game

**As a** League Owner  
**I want to** cancel a scheduled game when necessary  
**So that** the published schedule accurately reflects the game's status.

**Priority:** P2

---

## Functional Requirements

- **FR-001:** The system shall allow only the League Owner to create game schedules.
- **FR-002:** The system shall associate each scheduled game with a specific league season.
- **FR-003:** The system shall record the home team and away team for each game.
- **FR-004:** The system shall record the scheduled game date, time, and location.
- **FR-005:** The system shall allow the League Owner to modify a game's date, time, or location.
- **FR-006:** The system shall allow only the League Owner to assign or change game officials.
- **FR-007:** The system shall prevent a team from being scheduled to play against itself.
- **FR-008:** The system shall require both teams to be participating in the selected season.
- **FR-009:** The system shall prevent a team from being assigned to two games with overlapping scheduled times.
- **FR-010:** The system shall allow the League Owner to cancel a scheduled game without deleting its record.
- **FR-011:** The system shall record a game's scheduling status, such as Scheduled or Canceled.
- **FR-012:** The system shall allow Public Visitors to view published game schedules without modifying them.
- **FR-013:** The system shall display the latest saved schedule information to authorized users and Public Visitors.
- **FR-014:** The system shall preserve scheduled game records when games are canceled.



---

## Key Entities


| Entity       | Description                                          |
| ------------ | ---------------------------------------------------- |
| Game         | Represents a scheduled competition between two teams |
| Season       | Represents the league season in which a game occurs  |
| TeamSeason   | Represents a team's participation in a season        |
| Location     | Represents the venue where a game is scheduled       |
| Official     | Represents a person assigned to officiate a game     |
| GameOfficial | Associates an official with a scheduled game         |


---

## Initial Data Model

### Game


| Attribute        | Type    | Description                  |
| ---------------- | ------- | ---------------------------- |
| gameId           | Integer | Unique game identifier       |
| seasonId         | Integer | Reference to Season          |
| homeTeamSeasonId | Integer | Reference to home TeamSeason |
| awayTeamSeasonId | Integer | Reference to away TeamSeason |
| gameDate         | Date    | Scheduled date               |
| startTime        | Time    | Scheduled start time         |
| endTime          | Time    | Scheduled end time           |
| locationId       | Integer | Reference to Location        |
| status           | String  | Scheduled or Canceled        |


### Location


| Attribute  | Type    | Description                |
| ---------- | ------- | -------------------------- |
| locationId | Integer | Unique location identifier |
| name       | String  | Venue name                 |
| address    | String  | Venue address              |


### Official


| Attribute  | Type    | Description                |
| ---------- | ------- | -------------------------- |
| officialId | Integer | Unique official identifier |
| name       | String  | Official's name            |
| phone      | String  | Contact phone number       |


### GameOfficial


| Attribute      | Type    | Description                  |
| -------------- | ------- | ---------------------------- |
| gameOfficialId | Integer | Unique assignment identifier |
| gameId         | Integer | Reference to Game            |
| officialId     | Integer | Reference to Official        |
| role           | String  | Official's assigned role     |


### Season (Existing Entity)


| Attribute | Type    | Description              |
| --------- | ------- | ------------------------ |
| seasonId  | Integer | Unique season identifier |
| leagueId  | Integer | Reference to League      |
| name      | String  | Season name              |
| startDate | Date    | Season start date        |
| endDate   | Date    | Season end date          |


**Relationships:**

- A Season can contain multiple Games.
- Each Game belongs to one Season.
- Each Game has one home TeamSeason and one away TeamSeason.
- Each Game takes place at one Location.
- A Location can host multiple Games.
- A Game can have multiple Officials.
- An Official can officiate multiple Games.
- GameOfficial represents the many-to-many relationship between Game and Official.

---

## Gherkin AC

### US-4.1: Create Game Schedule

#### AC-4.1.1: Successfully schedule a game

**Given** the League Owner is authenticated  
**And** two different teams are registered for the selected season  
**And** a location is available  
**When** the League Owner enters the game date, time, teams, and location  
**And** saves the game  
**Then** the system creates a scheduled Game record  
**And** displays the game on the schedule.

#### AC-4.1.2: Reject a game involving the same team

**Given** the League Owner is creating a game  
**When** the same team is selected as both the home team and away team  
**Then** the system rejects the game  
**And** displays a validation message.

#### AC-4.1.3: Reject overlapping team schedules

**Given** a team already has a game scheduled during a particular time period  
**When** the League Owner attempts to schedule another game for that team during an overlapping period  
**Then** the system rejects the conflicting schedule  
**And** displays a scheduling conflict message.

### US-4.2: Update Game Schedule

#### AC-4.2.1: Successfully reschedule a game

**Given** a scheduled game exists  
**And** the League Owner is authenticated  
**When** the League Owner changes the game's date, time, or location  
**And** saves the changes  
**Then** the system updates the Game record  
**And** displays the updated schedule.

#### AC-4.2.2: Reject unauthorized schedule modification

**Given** a Team Manager is authenticated  
**When** the Team Manager attempts to change a game's schedule  
**Then** the system denies the modification  
**And** preserves the existing schedule.

### US-4.3: Assign Game Officials

#### AC-4.3.1: Successfully assign an official

**Given** a scheduled game exists  
**And** an official is registered in the system  
**And** the League Owner is authenticated  
**When** the League Owner assigns the official to the game  
**Then** the system creates a GameOfficial record  
**And** displays the official on the game's details.

#### AC-4.3.2: Change an official assignment

**Given** an official is assigned to a game  
**And** the League Owner is authenticated  
**When** the League Owner replaces that official with another registered official  
**Then** the system updates the assignment  
**And** displays the new official.

### US-4.4: View Game Schedule

#### AC-4.4.1: Public visitor views upcoming games

**Given** scheduled games are published  
**When** a Public Visitor opens the game schedule  
**Then** the system displays the scheduled dates, times, teams, and locations  
**And** does not provide schedule modification controls.

### US-4.5: Cancel Game

#### AC-4.5.1: Cancel a scheduled game

**Given** a scheduled game exists  
**And** the League Owner is authenticated  
**When** the League Owner cancels the game  
**Then** the system changes its status to Canceled  
**And** retains the Game record  
**And** displays the canceled status on the schedule.

---

## Screen Requirements

### SR-001: Game Schedule List Screen

**Authorized users:** League Owner, Team Manager, Public Visitor

**Display:**

- Game date
- Start time
- Home team
- Away team
- Location
- Game status

**Actions:**

- View Game
- Filter by League or Season
- Add Game (League Owner only)
- Edit Game (League Owner only)
- Cancel Game (League Owner only)
- Close

**Behavior:**

- The system shall display published game schedules.
- Public Visitors and Team Managers shall have read-only access to schedule information.

### SR-002: Game Edit Screen

**Authorized user:** League Owner

**Fields:**

- League Season
- Home Team
- Away Team
- Game Date
- Start Time
- End Time
- Location
- Game Status

**Actions:**

- Save
- Cancel

**Behavior:**

- The system shall validate required game information.
- The system shall prevent a team from playing itself.
- The system shall reject overlapping team schedules.
- Save shall store valid changes.
- Cancel shall discard unsaved changes.

### SR-003: Game Official Assignment Screen

**Authorized user:** League Owner

**Display:**

- Game details
- Assigned officials
- Available officials

**Fields:**

- Official
- Official Role

**Actions:**

- Assign Official
- Change Assignment
- Remove Assignment
- Close

**Behavior:**

- Only the League Owner shall be able to change official assignments.
- Changes shall be reflected in the game details.

### SR-004: Game Details Screen

**Authorized users:** League Owner, Team Manager, Public Visitor

**Display:**

- League and season
- Home team
- Away team
- Game date
- Start time
- End time
- Location
- Assigned officials
- Game status

**Actions:**

- Return to Schedule
- Edit Game (League Owner only)

