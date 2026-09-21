# matches
# IPL Matches Dataset (2008–2024)

This repository contains the complete dataset of Indian Premier League (IPL) matches played from the inaugural season in 2008 through the 2024 season (`matches.csv`).

## Dataset Summary

* **Filename**: `matches.csv`
* **Total Records**: 1,095 matches
* **Total Columns**: 20
* **Date Range**: April 18, 2008 – May 26, 2024
* **Duplicates**: 0 duplicate rows

## Column Schema

| Column | Data Type | Description |
| :--- | :--- | :--- |
| `id` | Integer | Unique match identifier |
| `season` | String | IPL season (e.g., `2007/08`, `2024`) |
| `city` | String | City where match was played |
| `date` | String (YYYY-MM-DD) | Date of the match |
| `match_type` | String | Match stage (`League`, `Final`, `Qualifier 1`, etc.) |
| `player_of_match` | String | Player awarded Man of the Match |
| `venue` | String | Stadium/Venue name |
| `team1` | String | Name of Team 1 |
| `team2` | String | Name of Team 2 |
| `toss_winner` | String | Team that won the toss |
| `toss_decision` | String | Toss choice (`bat` or `field`) |
| `winner` | String | Match winning team |
| `result` | String | Result type (`runs`, `wickets`, `no result`, `tie`) |
| `result_margin` | Float | Victory margin (runs or wickets) |
| `target_runs` | Float | Target score set for the 2nd innings |
| `target_overs` | Float | Maximum overs allocated for 2nd innings |
| `super_over` | String | Super Over played (`Y`/`N`) |
| `method` | String | Rain/DLS method indicator (`D/L`) |
| `umpire1` | String | First standing umpire |
| `umpire2` | String | Second standing umpire |

## Missing Values Overview

| Column | Missing Count | Context |
| :--- | :--- | :--- |
| `method` | 1,074 | Only populated when Duckworth-Lewis-Stern (DLS) method was used |
| `city` | 51 | Unspecified or neutral venues (e.g., UAE venues) |
| `result_margin` | 19 | Super Over ties or No Result matches |
| `player_of_match` | 5 | Abandoned matches with No Result |
| `winner` | 5 | Abandoned matches with No Result |
| `target_runs` | 3 | Incomplete or unrecorded match details |
| `target_overs` | 3 | Incomplete or unrecorded match details |

## Quick Start & Preprocessing

```python
import pandas as pd

# Load dataset
df = pd.read_csv('matches.csv')

# 1. Parse date column
df['date'] = pd.to_datetime(df['date'])

# 2. Impute missing values
df['winner'] = df['winner'].fillna('No Result')
df['player_of_match'] = df['player_of_match'].fillna('None')
df['result_margin'] = df['result_margin'].fillna(0)
df['method'] = df['method'].fillna('Standard')

# Fill missing city from venue name where possible
df['city'] = df['city'].fillna(
    df['venue'].apply(lambda x: x.split(',')[-1].strip() if ',' in str(x) else 'Unknown')
)

# 3. Standardize franchise rebrands
team_mapping = {
    'Royal Challengers Bangalore': 'Royal Challengers Bengaluru',
    'Kings XI Punjab': 'Punjab Kings',
    'Delhi Daredevils': 'Delhi Capitals',
    'Deccan Chargers': 'Sunrisers Hyderabad'
}

team_cols = ['team1', 'team2', 'toss_winner', 'winner']
for col in team_cols:
    df[col] = df[col].replace(team_mapping)

print(f"Cleaned dataset shape: {df.shape}")
