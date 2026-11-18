# ACC Reality Check
### DS2002 Fall 2026 Capstone Project

**Course:** DS2002 – Data Science Systems  
**Semester:** Fall 2026  
**Submission Deadline:** December 18, 2026, at 11:59 PM ET

## 1. Project Overview

ACC Reality Check is a data analytics project that investigates whether Atlantic Coast Conference (ACC) men's basketball teams are performing as well as their records suggest.

A team's win-loss record does not always reflect its actual performance. Some teams may have undefeated records because they played weaker opponents, while others may have faced more competitive schedules.

Our project combines basketball ratings from Bart Torvik with game results and player statistics from ESPN to evaluate team performance, strength of schedule, offensive and defensive efficiency, and individual player contributions.

The project uses Python for data collection and processing, SQLite for data storage, SQL for analysis, and FastAPI to publish the results through a REST API.

## 2. Team Members and Responsibilities

| Team Member | Responsibilities |
|---|---|
| Member 1: Safaa Mahbub | Bart Torvik CSV collection, data cleaning, team name crosswalk, ratings snapshots |
| Member 2: [Name] | ESPN API collection, scoreboard data, game summaries, player box scores, data transformation |
| Member 3: [Name] | SQLite database design, table creation, SQL queries, data analysis, Charts 1–3 |
| Member 4: [Name] | FastAPI development, API endpoints, PythonAnywhere deployment, Chart 4 |

All team members contribute to integration, testing, documentation, and the final presentation.

## 3. Data Sources

### Bart Torvik – Team Ratings
URL: https://barttorvik.com/2027_team_results.csv

Provides team-level basketball ratings for approximately 365 Division I teams.

Important variables include:
- `team`: Team name
- `conf`: Conference
- `record`: Win-loss record
- `adjoe`: Adjusted offensive efficiency
- `adjde`: Adjusted defensive efficiency
- `barthag`: Overall team strength
- `adjt`: Adjusted tempo
- `sos`: Strength of schedule

The ratings file is downloaded regularly to preserve historical snapshots for Question 6.

### ESPN – Men's College Basketball API
Scoreboard endpoint:

https://site.api.espn.com/apis/site/v2/sports/basketball/mens-college-basketball/scoreboard

Game summary endpoint:

https://site.api.espn.com/apis/site/v2/sports/basketball/mens-college-basketball/summary

ESPN provides:
- Game schedules and results
- Team identifiers and names
- Game dates and start times
- Home, away, and neutral-site information
- Team box score statistics
- Individual player statistics

Game data is collected beginning November 1, 2026.

## 4. Project Structure

```text
ACC-Reality-Check/
│
├── data/
│   ├── raw/
│   │   ├── torvik/
│   │   └── espn/
│   │
│   └── processed/
│
├── notebooks/
│   └── acc_reality_check.ipynb
│
├── api/
│   ├── main.py
│   └── requirements.txt
│
├── database/
│   └── acc.db
│
├── README.md
└── requirements.txt
```

Raw API responses and downloaded CSV files are saved locally so that subsequent pipeline runs can reuse existing data instead of repeatedly requesting the same information.

## 5. Data Pipeline

The project pipeline consists of three main stages.

### Extract
- Download Bart Torvik's team ratings.
- Retrieve ESPN scoreboard data for each date.
- Retrieve individual game summaries for completed games.
- Save raw CSV and JSON responses.

### Transform
- Clean and standardize team names.
- Build a crosswalk between ESPN and Torvik team names.
- Retain completed games.
- Convert UTC game timestamps to Eastern Time.
- Reshape games into one row per team per game.
- Split shooting statistics into made and attempted columns.
- Estimate possessions using the provided formula.
- Flatten player box scores into individual player-game records.

### Load
- Create the SQLite database.
- Load the processed data into the appropriate tables.
- Use primary keys and `INSERT OR REPLACE` to prevent duplicate records.
- Verify that repeated pipeline executions do not change row counts.

## 6. Database Schema

The SQLite database is named `acc.db` and contains at least six tables.

| Table | Description |
|---|---|
| `teams` | Team identifiers, names, and ACC membership |
| `team_crosswalk` | ESPN and Torvik team name mappings |
| `ratings` | Historical team ratings by snapshot date |
| `games` | Game dates, teams, scores, and locations |
| `team_games` | Team-level game statistics and estimated possessions |
| `player_games` | Individual player statistics for each game |

The database stores ratings for all Division I teams so that opponents outside the ACC can also be evaluated.

## 7. Research Questions and SQL Analysis

The project answers six questions using SQL queries executed against `acc.db`.

1. **Record vs. Rating:** How do ACC teams' winning percentages compare with their efficiency margins?
2. **Strength of Opponents:** Which ACC teams have faced the strongest opponents based on average opponent Barthag?
3. **Close Games and Recent Form:** Which teams rely on close wins, and which teams are improving or declining?
4. **Pace and Points per Possession:** How do teams compare in raw offensive and defensive efficiency versus Torvik's adjusted ratings?
5. **Who Carries Each Team:** Which players lead their teams in scoring, and what percentage of team points do they contribute?
6. **Does the Rating Predict the Winner?:** How accurately does the latest available Torvik rating before each game predict the winner?

Each answer includes supporting statistics, the number of games analyzed, and an interpretation of the findings.

## 8. Visualizations

The final notebook includes four required charts:

| Chart | Description |
|---|---|
| Chart 1 | Winning percentage vs. efficiency margin for all 18 ACC teams |
| Chart 2 | Average opponent Barthag, ranked by team |
| Chart 3 | Rolling five-game average margin for selected ACC teams |
| Chart 4 | Additional analysis based on JSON retrieved from our published API |

All charts include descriptive titles and appropriately labeled axes.

## 9. FastAPI Application

The project publishes its findings through a FastAPI application deployed using PythonAnywhere.

The API reads from the completed SQLite database and does not make external requests to ESPN or Bart Torvik.

**Live API:** [Insert PythonAnywhere URL]

**Available endpoints:**

| Endpoint | Description |
|---|---|
| `/teams` | Returns all 18 ACC teams with records, efficiency margins, and average opponent Barthag |
| `/teams/{team}` | Returns an individual team's summary and last five games |
| `/games?date=YYYY-MM-DD` | Returns games played on a specified Eastern date |
| `/matchup?a=...&b=...` | Compares two teams' ratings and identifies the favorite |
| `/leaders?stat=...` | Ranks ACC players by points, rebounds, assists, steals, or blocks per game |

Interactive API documentation is available at `/docs`.

## 10. How to Run the Project

### Requirements

- Python 3
- Jupyter Notebook
- pandas
- requests
- SQLite
- matplotlib
- FastAPI
- uvicorn

### Install dependencies

```bash
pip install -r requirements.txt
```

### Run the notebook

Open the notebook:

```bash
jupyter notebook
```

Navigate to `notebooks/acc_reality_check.ipynb` and run all cells from top to bottom.

The notebook collects and processes the source data, populates the SQLite database, executes the SQL analyses, and generates the visualizations.

### Run the API locally

```bash
cd api
pip install -r requirements.txt
uvicorn main:app --reload
```

Open the following URL in your browser:

```text
http://127.0.0.1:8000/docs
```

## 11. Key Findings

*To be completed after the SQL analysis.*

- **Record vs. Rating:** [Insert findings]
- **Strength of Opponents:** [Insert findings]
- **Close Games and Recent Form:** [Insert findings]
- **Pace and Efficiency:** [Insert findings]
- **Leading Scorers:** [Insert findings]
- **Rating Predictions:** [Insert findings]

## 12. Reflection

The following reflection questions are addressed by the team in the final notebook or in this README.

1. **The Crosswalk:** Which team names initially failed to match, how were they corrected, and how would an unmatched ACC school affect the analysis?
2. **A Pipeline Decision:** What data-processing decision did the team make, what alternatives existed, and how could those alternatives affect the results?
3. **Trust:** What could break if the pipeline ran automatically every morning, and how would the team detect the issue?
4. **Our Numbers vs. Torvik's:** Where did our calculations differ from Torvik's ratings, and which results did we trust?
5. **Teamwork:** How did the team divide responsibilities, and what would we change with another week?

## 13. Presentation

**Presentation Video:** [Insert accessible video link]

The recorded presentation is no longer than eight minutes and includes contributions from every team member.

It covers:
- The project's research question.
- Data sources and pipeline.
- Database structure and processing decisions.
- Main analytical findings.
- A live demonstration of the published API.
- One technical challenge encountered during development.

---

**Course:** DS2002 – Fall 2026  
**Project:** ACC Reality Check  
**Final Submission:** December 18, 2026

