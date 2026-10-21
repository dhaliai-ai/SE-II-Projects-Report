# Feature: Maintain League

**Feature ID:** 1  
**Feature Name:** Maintain League  
**Status:** Draft  
**Branch:** `soccer-league`  
**Created:** 2026-10-19

---

## User Stories

### US-1.1: Add League

**As a** League Owner  
**I want to** create a league with its sport and division  
**So that** teams can participate in an organized competition.

**Priority:** P1

### US-1.2: Edit League

**As a** League Owner  
**I want to** update league information  
**So that** the website displays accurate league details.

**Priority:** P1

### US-1.3: View Leagues

**As a** Public Visitor  
**I want to** view available leagues  
**So that** I can find information about competitions I am interested in.

**Priority:** P1

### US-1.4: Maintain League Status

**As a** League Owner  
**I want to** mark a league as inactive without losing its historical information  
**So that** previous competition records remain available.

**Priority:** P2

---

## Functional Requirements

- **FR-001:** The system shall allow the League Owner to create a league.
- **FR-002:** The system shall record the league name, sport, and division.
- **FR-003:** The system shall support soccer, flag football, basketball, and softball.
- **FR-004:** The system shall support men's and women's divisions.
- **FR-005:** The system shall allow the League Owner to update league information.
- **FR-006:** The system shall allow public visitors to view league information without modifying it.
- **FR-007:** The system shall associate leagues with their seasons.
- **FR-008:** The system shall preserve historical league information when a league becomes inactive.


---

## Key Entities


| Entity   | Description                                            |
| -------- | ------------------------------------------------------ |
| League   | Represents a competition organized by the League Owner |
| Sport    | Identifies the sport played                            |
| Division | Identifies the men's or women's competition            |
| Season   | Represents a period of league competition              |
| Team     | Represents a team participating in a league season     |


---

## Initial Data Model

### League


| Attribute  | Type    | Description              |
| ---------- | ------- | ------------------------ |
| leagueId   | Integer | Unique league identifier |
| name       | String  | League name              |
| sportId    | Integer | Reference to Sport       |
| divisionId | Integer | Reference to Division    |
| status     | String  | Active or inactive       |


### Sport


| Attribute | Type    | Description                                    |
| --------- | ------- | ---------------------------------------------- |
| sportId   | Integer | Unique sport identifier                        |
| name      | String  | Soccer, flag football, basketball, or softball |


### Division


| Attribute  | Type    | Description                |
| ---------- | ------- | -------------------------- |
| divisionId | Integer | Unique division identifier |
| name       | String  | Men's or women's division  |


### Season


| Attribute | Type    | Description              |
| --------- | ------- | ------------------------ |
| seasonId  | Integer | Unique season identifier |
| leagueId  | Integer | Associated league        |
| name      | String  | Season name              |
| startDate | Date    | Beginning of season      |
| endDate   | Date    | End of season            |


**Relationships:**

- A Sport can be associated with multiple Leagues.
- A Division can be associated with multiple Leagues.
- A League can have multiple Seasons.
- A Season can include multiple Teams.

---

## Gherkin AC

### US-1.1: Add League

#### AC-1.1.1: Create a league with valid information

**Given** the League Owner is authenticated  
**And** the required league information is available  
**When** the League Owner creates a league  
**Then** the system saves the league information  
**And** the league becomes available for viewing.

### US-1.2: Edit League

#### AC-1.2.1: Update league information

**Given** an existing league is recorded  
**And** the League Owner is authenticated  
**When** the League Owner updates the league information  
**Then** the system saves the changes  
**And** displays the updated information.

### US-1.3: View Leagues

#### AC-1.3.1: Public visitor views leagues

**Given** leagues are available in the system  
**When** a Public Visitor opens the league listing  
**Then** the system displays the available leagues  
**And** the visitor cannot modify league information.

### US-1.4: Maintain League Status

#### AC-1.4.1: Deactivate a league

**Given** a league exists with historical seasons  
**And** the League Owner is authenticated  
**When** the League Owner marks the league as inactive  
**Then** the system updates its status  
**And** preserves the historical season information.

---

## Screen Requirements

### SR-001: League List Screen

**Authorized users:** League Owner, Public Visitor

**Display:**

- League name
- Sport
- Division
- League status

**Actions:**

- View league information
- Add League (League Owner only)
- Edit League (League Owner only)

### SR-002: League Edit Screen

**Authorized user:** League Owner

**Fields:**

- League Name
- Sport
- Division
- Status

**Actions:**

- Save
- Cancel

**Behavior:**

- The system shall display the existing information when editing a league.
- The system shall save valid changes.
- Cancel shall return to the League List Screen without saving changes.

### SR-003: League Details Screen

**Authorized users:** League Owner, Public Visitor

**Display:**

- League name
- Sport
- Division
- Associated seasons
- League status

**Actions:**

- Return to League List Screen

