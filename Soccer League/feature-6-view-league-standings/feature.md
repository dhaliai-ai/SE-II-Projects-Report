# Feature: View League Standings

**Feature ID:** 6  
**Feature Name:** View League Standings  
**Status:** Draft  
**Branch:** `soccer-league`  
**Created:** 2026-10-19

---

## User Stories

### US-6.1: View League Standings

**As a** Public Visitor  
**I want to** view the standings for a league season  
**So that** I can see how teams are performing in the competition.

**Priority:** P1

### US-6.2: Calculate Team Standings

**As a** League Owner  
**I want to** have team standings calculated from recorded game results  
**So that** league rankings remain accurate without manual calculations.

**Priority:** P1

### US-6.3: View Team Performance

**As a** Team Manager  
**I want to** view my team's wins, losses, ties, and games played  
**So that** I can understand my team's performance during the season.

**Priority:** P1

### US-6.4: Update Standings After Results Change

**As a** League Owner  
**I want to** have standings updated when game results are entered or corrected  
**So that** the rankings reflect the latest recorded results.

**Priority:** P1

### US-6.5: View Historical Standings

**As a** Public Visitor  
**I want to** view standings from previous seasons  
**So that** I can compare team performance over time.

**Priority:** P2

---

## Functional Requirements

- **FR-001:** The system shall display standings for a selected league season.
- **FR-002:** The system shall calculate standings using recorded results from completed games.
- **FR-003:** The system shall associate each standing with a TeamSeason record.
- **FR-004:** The system shall calculate the number of games played by each team.
- **FR-005:** The system shall calculate each team's wins, losses, and ties where applicable to the sport.
- **FR-006:** The system shall calculate ranking points according to the configured rules for the league.
- **FR-007:** The system shall rank teams according to the league's configured ranking and tie-breaking rules.
- **FR-008:** The system shall exclude Scheduled and Canceled games from standings calculations.
- **FR-009:** The system shall recalculate standings when a completed game result is added or corrected.
- **FR-010:** The system shall display standings to Public Visitors without requiring them to modify league information.
- **FR-011:** The system shall prevent Public Visitors and Team Managers from directly changing calculated standings.
- **FR-012:** The system shall allow users to select a league and season when viewing standings.
- **FR-013:** The system shall preserve the ability to view historical standings using retained game results.
- **FR-014:** The system shall display teams with no completed games with zero games played, wins, losses, and ties.
- **FR-015:** The system shall not count a completed game more than once in the standings calculation.


---

## Key Entities

| Entity | Description |
|---|---|
| League | Represents an organized sports competition |
| Season | Represents a league competition period |
| Team | Represents a participating team |
| TeamSeason | Associates a team with a specific season |
| Game | Represents a scheduled or completed competition |
| GameResult | Contains the recorded final scores |
| Standing | Represents a team's calculated performance for a season |
| RankingRule | Defines how league points and ranking order are determined |

---

## Initial Data Model

### Standing

| Attribute | Type | Description |
|---|---|---|
| teamSeasonId | Integer | Reference to TeamSeason |
| gamesPlayed | Integer | Number of completed games |
| wins | Integer | Number of wins |
| losses | Integer | Number of losses |
| ties | Integer | Number of tied games |
| pointsFor | Integer | Total points or goals scored |
| pointsAgainst | Integer | Total points or goals conceded |
| rankingPoints | Integer | Points used to calculate league ranking |
| rank | Integer | Calculated position in standings |

**Note:** Standing is a calculated data model derived from completed Game and GameResult records. It does not necessarily require a separate database table.

### RankingRule

| Attribute | Type | Description |
|---|---|---|
| rankingRuleId | Integer | Unique ranking rule identifier |
| leagueId | Integer | Reference to League |
| pointsForWin | Integer | Ranking points awarded for a win |
| pointsForTie | Integer | Ranking points awarded for a tie |
| pointsForLoss | Integer | Ranking points awarded for a loss |
| tieBreakerMethod | String | Configured tie-breaking method |

### GameResult (Existing Entity)

| Attribute | Type | Description |
|---|---|---|
| gameResultId | Integer | Unique game result identifier |
| gameId | Integer | Reference to Game |
| homeScore | Integer | Final score of the home team |
| awayScore | Integer | Final score of the away team |
| lastUpdated | DateTime | Most recent result update |

### TeamSeason (Existing Entity)

| Attribute | Type | Description |
|---|---|---|
| teamSeasonId | Integer | Unique team-season identifier |
| teamId | Integer | Reference to Team |
| seasonId | Integer | Reference to Season |

**Relationships:**

- A League can have multiple Seasons.
- A Season can contain multiple TeamSeason records.
- Each TeamSeason represents one participating team in one season.
- A TeamSeason has one calculated Standing for the selected season.
- A Season can contain multiple Games.
- A completed Game has one GameResult.
- Standings are calculated from GameResult records associated with the selected season.
- A League has an associated set of RankingRules.

---

## Gherkin AC

### US-6.1: View League Standings

#### AC-6.1.1: Display standings for a selected season

**Given** a league season contains participating teams  
**And** completed game results are available  
**When** a Public Visitor selects the league season and opens standings  
**Then** the system displays the participating teams  
**And** shows their games played, wins, losses, ties, and ranking points  
**And** displays teams in the calculated ranking order.

#### AC-6.1.2: Display standings before games are completed

**Given** a league season contains registered teams  
**And** no games have been completed  
**When** a Public Visitor opens standings  
**Then** the system displays all participating teams  
**And** shows zero for games played, wins, losses, ties, and ranking points.

### US-6.2: Calculate Team Standings

#### AC-6.2.1: Calculate standings from completed games

**Given** a league season contains completed games with valid results  
**And** ranking rules have been configured  
**When** the system calculates standings  
**Then** it counts each completed game once  
**And** calculates wins, losses, and ties according to the results  
**And** calculates ranking points using the configured rules.

#### AC-6.2.2: Exclude games that have not been completed

**Given** a league season contains Scheduled, Completed, and Canceled games  
**When** the system calculates standings  
**Then** only Completed games with recorded results contribute to standings.

### US-6.3: View Team Performance

#### AC-6.3.1: View team performance details

**Given** standings have been calculated for a season  
**When** a Team Manager selects a participating team  
**Then** the system displays the team's games played, wins, losses, and ties  
**And** displays its points for, points against, and ranking points.

### US-6.4: Update Standings After Results Change

#### AC-6.4.1: Recalculate after a new result

**Given** a scheduled game has been completed  
**And** a valid GameResult is saved  
**When** the standings are next requested  
**Then** the system includes the new result  
**And** displays the updated team rankings.

#### AC-6.4.2: Recalculate after a score correction

**Given** a completed game result has contributed to standings  
**When** an authorized Team Manager corrects the result  
**Then** the system uses the corrected result in its calculations  
**And** does not count the previous result separately.

### US-6.5: View Historical Standings

#### AC-6.5.1: View standings from a previous season

**Given** a previous season contains recorded game results  
**When** a Public Visitor selects that season  
**Then** the system calculates and displays standings using that season's results  
**And** does not include games from other seasons.

---

## Screen Requirements

### SR-001: League Standings Screen

**Authorized users:** League Owner, Team Manager, Public Visitor

**Display:**
- League name
- Season
- Team rank
- Team name
- Games played
- Wins
- Losses
- Ties
- Points for
- Points against
- Ranking points

**Actions:**
- Select League
- Select Season
- View Team Details
- Close

**Behavior:**
- The system shall display standings in ranking order.
- The system shall calculate standings using completed game results.
- Public Visitors shall have read-only access.
- The screen shall not provide direct editing of calculated standings.

### SR-002: Team Standing Details Screen

**Authorized users:** League Owner, Team Manager, Public Visitor

**Display:**
- Team name
- League and season
- Current rank
- Games played
- Wins
- Losses
- Ties
- Points for
- Points against
- Ranking points
- Completed game results for the selected season

**Actions:**
- View Game Result
- Return to Standings

**Behavior:**
- The system shall display statistics calculated from the selected season's completed games.
- The screen shall not permit modification of calculated values.

### SR-003: League Ranking Rules Screen

**Authorized user:** League Owner

**Display:**
- League name
- Points for win
- Points for tie
- Points for loss
- Tie-breaking method

**Actions:**
- Edit Ranking Rules
- Save
- Cancel

**Behavior:**
- The system shall allow only the League Owner to modify ranking rules.
- Changes to ranking rules shall affect subsequent standings calculations.
- Cancel shall discard unsaved changes.
- The system shall validate required ranking rule information before saving.