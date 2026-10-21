# Feature: Record Game Results and Player Statistics

**Feature ID:** 5  
**Feature Name:** Record Game Results and Player Statistics  
**Status:** Draft  
**Branch:** `soccer-league`  
**Created:** 2026-10-19

---

## User Stories

### US-5.1: Record Game Results

**As a** Team Manager  
**I want to** enter game scores for my team's completed games  
**So that** the league can maintain accurate game results.

**Priority:** P1

### US-5.2: Record Player Statistics

**As a** Team Manager  
**I want to** record individual player statistics for a completed game  
**So that** player performance can be tracked throughout the season.

**Priority:** P1

### US-5.3: Correct Game Results

**As a** Team Manager  
**I want to** correct incorrectly entered game results for my team  
**So that** the published information remains accurate.

**Priority:** P1

### US-5.4: View Game Results

**As a** Public Visitor  
**I want to** view completed game results  
**So that** I can follow the league's competitions.

**Priority:** P1

### US-5.5: View Player Statistics

**As a** Public Visitor  
**I want to** view published player statistics  
**So that** I can follow player performance during a season.

**Priority:** P2

---

## Functional Requirements

- **FR-001:** The system shall allow authorized Team Managers to enter scores for completed games involving their teams.
- **FR-002:** The system shall associate each game result with an existing Game record.
- **FR-003:** The system shall record the home team's score and away team's score.
- **FR-004:** The system shall prevent negative game scores.
- **FR-005:** The system shall prevent recording a result for a canceled game.
- **FR-006:** The system shall allow authorized Team Managers to correct previously entered scores for games involving their teams.
- **FR-007:** The system shall record individual player statistics associated with a specific game and player.
- **FR-008:** The system shall allow authorized Team Managers to update player statistics for their teams.
- **FR-009:** The system shall associate player statistics with players registered on the appropriate team's season roster.
- **FR-010:** The system shall prevent Team Managers from modifying results or player statistics for games in which their teams are not participating.
- **FR-011:** The system shall allow Public Visitors to view published game results.
- **FR-012:** The system shall allow Public Visitors to view published player statistics.
- **FR-013:** The system shall identify games with recorded results as Completed.
- **FR-014:** The system shall retain game results and player statistics as part of the league's historical records.
- **FR-015:** The system shall record the date and time of the most recent result update.



---

## Key Entities


| Entity              | Description                                                          |
| ------------------- | -------------------------------------------------------------------- |
| Game                | Represents a scheduled competition between two teams                 |
| GameResult          | Stores the final score and result information for a game             |
| Player              | Represents a registered player                                       |
| RosterEntry         | Represents a player's membership on a team during a season           |
| PlayerGameStatistic | Stores an individual player's statistics for a game                  |
| StatisticType       | Identifies the kind of statistic being recorded                      |
| TeamManager         | Represents a manager authorized to enter team results and statistics |


---

## Initial Data Model

### GameResult


| Attribute    | Type     | Description                      |
| ------------ | -------- | -------------------------------- |
| gameResultId | Integer  | Unique result identifier         |
| gameId       | Integer  | Reference to Game                |
| homeScore    | Integer  | Home team's final score          |
| awayScore    | Integer  | Away team's final score          |
| lastUpdated  | DateTime | Most recent update date and time |


### PlayerGameStatistic


| Attribute             | Type    | Description                        |
| --------------------- | ------- | ---------------------------------- |
| playerGameStatisticId | Integer | Unique statistic record identifier |
| gameId                | Integer | Reference to Game                  |
| rosterEntryId         | Integer | Reference to RosterEntry           |
| statisticTypeId       | Integer | Reference to StatisticType         |
| value                 | Integer | Recorded statistic value           |


### StatisticType


| Attribute       | Type    | Description                      |
| --------------- | ------- | -------------------------------- |
| statisticTypeId | Integer | Unique statistic type identifier |
| sportId         | Integer | Reference to Sport               |
| name            | String  | Statistic name                   |
| description     | String  | Explanation of the statistic     |


### Game (Existing Entity)


| Attribute        | Type    | Description                       |
| ---------------- | ------- | --------------------------------- |
| gameId           | Integer | Unique game identifier            |
| seasonId         | Integer | Reference to Season               |
| homeTeamSeasonId | Integer | Reference to home TeamSeason      |
| awayTeamSeasonId | Integer | Reference to away TeamSeason      |
| status           | String  | Scheduled, Completed, or Canceled |


**Relationships:**

- A Game may have one GameResult.
- Each GameResult belongs to one Game.
- A Game can have multiple PlayerGameStatistic records.
- Each PlayerGameStatistic belongs to one Game and one RosterEntry.
- A RosterEntry can have multiple PlayerGameStatistic records.
- Each PlayerGameStatistic references one StatisticType.
- A StatisticType belongs to one Sport.
- A Team Manager may enter results and statistics only for authorized teams.

---

## Gherkin AC

### US-5.1: Record Game Results

#### AC-5.1.1: Successfully record a game result

**Given** a scheduled game has been played  
**And** the Team Manager is authenticated  
**And** the Team Manager manages one of the participating teams  
**When** the Team Manager enters valid home and away scores  
**And** saves the result  
**Then** the system creates a GameResult record  
**And** associates it with the selected Game  
**And** marks the game as Completed.

#### AC-5.1.2: Reject negative scores

**Given** a Team Manager is entering a game result  
**When** the Team Manager enters a negative score  
**Then** the system displays a validation error  
**And** does not save the invalid result.

#### AC-5.1.3: Reject results for canceled games

**Given** a game has a Canceled status  
**When** a Team Manager attempts to enter a result  
**Then** the system rejects the submission  
**And** preserves the game's Canceled status.

### US-5.2: Record Player Statistics

#### AC-5.2.1: Successfully record player statistics

**Given** a completed game exists  
**And** the player belongs to the Team Manager's team roster for that season  
**When** the Team Manager enters a valid statistic type and value  
**And** saves the information  
**Then** the system creates a PlayerGameStatistic record  
**And** associates it with the player and game.

#### AC-5.2.2: Reject statistics for an unrelated player

**Given** a Team Manager is entering player statistics  
**And** the selected player is not on a participating team's season roster  
**When** the Team Manager attempts to save the statistics  
**Then** the system rejects the submission  
**And** displays an appropriate validation message.

### US-5.3: Correct Game Results

#### AC-5.3.1: Correct an existing score

**Given** a game result already exists  
**And** the authenticated Team Manager is authorized to update the result  
**When** the Team Manager changes the score and saves  
**Then** the system updates the GameResult  
**And** records the latest update date and time  
**And** displays the corrected score.

#### AC-5.3.2: Reject unauthorized correction

**Given** a Team Manager is authenticated  
**And** neither participating team is managed by that Team Manager  
**When** the Team Manager attempts to modify the result  
**Then** the system denies the modification  
**And** leaves the existing result unchanged.

### US-5.4: View Game Results

#### AC-5.4.1: Public visitor views completed results

**Given** completed games have published results  
**When** a Public Visitor opens the results screen  
**Then** the system displays the game dates, teams, and final scores  
**And** does not provide result editing controls.

### US-5.5: View Player Statistics

#### AC-5.5.1: Public visitor views player statistics

**Given** published player statistics exist for a season  
**When** a Public Visitor opens the player statistics screen  
**Then** the system displays the permitted statistics  
**And** does not allow the visitor to modify them.

---

## Screen Requirements

### SR-001: Game Results List Screen

**Authorized users:** League Owner, Team Manager, Public Visitor

**Display:**

- Game date
- Home team
- Away team
- Home score
- Away score
- Game status

**Actions:**

- View Result
- Filter by League or Season
- Enter Result (authorized Team Manager)
- Edit Result (authorized Team Manager)
- Close

**Behavior:**

- Public Visitors shall have read-only access.
- Team Managers shall only be able to modify results for authorized teams.

### SR-002: Game Result Edit Screen

**Authorized user:** Team Manager

**Fields:**

- Game (selected from scheduled games)
- Home Score
- Away Score

**Actions:**

- Save
- Cancel

**Behavior:**

- The system shall validate both scores.
- Negative scores shall be rejected.
- Canceled games shall not accept results.
- Saving a valid result shall mark the game as Completed.
- Cancel shall discard unsaved changes.

### SR-003: Player Statistics Screen

**Authorized users:** League Owner, Team Manager, Public Visitor

**Display:**

- Player name
- Team
- Game
- Statistic type
- Statistic value

**Actions:**

- View Statistics
- Filter by Player, Team, or Season
- Add Statistic (authorized Team Manager)
- Edit Statistic (authorized Team Manager)
- Close

**Behavior:**

- Public Visitors shall have read-only access.
- Team Managers shall only modify statistics for authorized teams.

### SR-004: Player Statistic Edit Screen

**Authorized user:** Team Manager

**Fields:**

- Game
- Player
- Statistic Type
- Statistic Value

**Actions:**

- Save
- Cancel

**Behavior:**

- The system shall display only players eligible for the selected game.
- The system shall validate the statistic value according to its type.
- Save shall store valid information.
- Cancel shall discard unsaved changes.

### SR-005: Game Result Details Screen

**Authorized users:** League Owner, Team Manager, Public Visitor

**Display:**

- League and season
- Game date
- Home team and away team
- Final scores
- Game status
- Published player statistics

**Actions:**

- Return to Results List
- Edit Result (authorized Team Manager)

