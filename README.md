# Fifa World Cup
A summary dashboard for the world cup taking place in USA, Canada and Mexico. This dashboard includes some of the key metrics and statistics associated with the world cup. I initially used python and SQL tools to perform some queries on the dataset. 

### Use of python to do some initial data analysis and querying. Initially pulled the file in and gathered a snapshot of the data

import pandas as pd
df = pd.read_csv('players-selected-columns.csv')
df.head()

#### Cleaning the data, so the age column is converted from years and days, to a decimal number

def parse_age(age_str):
    if pd.isna(age_str):
        return None
    years, days = str(age_str).split('-')
    return round(int(years) + int(days) / 365, 2)

df['age'] = df['age'].apply(parse_age)
df[['age']].head()

#### Using python to convert null values for the no club column

df['club'] = df['club'].fillna('No Club')
df['club'] = df['club'].replace('', 'No Club')
df['club'].value_counts().head()

#### Using python to drop a duplicate column

df = df.drop(columns=['team_country'])
df.columns

#### Generating a new cleaned CSV file in anticpation of being used with Power Bi

df.to_csv('players_cleaned.csv', index=False)
df = pd.read_csv('players_cleaned.csv')
df.head()

#### Using python to sort countries by  age

df.groupby('team')['age'].mean().sort_values(ascending=False)

#### Python to found out how many unique clubs are represented at the Fifa World Cup

df['club'].nunique()

#### Ascertaining the average age and standard deviations

df.groupby('team')['age'].agg(['mean', 'std']).sort_values('mean', ascending=False)

#### Using python to identify the top 15 most represented 'clubs' at the Fifa World Cup

df['club'].value_counts().head(15)

#### Then did some further SQl queries to do some initial analysis on the data set

#### Create the database to query from
import sqlite3
import pandas as pd

#### Load your cleaned CSV
df = pd.read_csv('players_cleaned.csv')

#### Create the SQLite database file and write the table into it
conn = sqlite3.connect('fifa_players.db')
df.to_sql('players', conn, if_exists='replace', index=False)
conn.close()

print("Done — fifa_players.db created")

#### Used SQL to find the average age of each squad alongside the squad size
conn = sqlite3.connect('fifa_players.db')

query = """
SELECT team, ROUND(AVG(age), 2) AS avg_age, COUNT(*) AS squad_size
FROM players
GROUP BY team
ORDER BY avg_age DESC
"""

result = pd.read_sql(query, conn)
print(result)

#### Distinct club count
pd.read_sql("SELECT COUNT(DISTINCT club) AS distinct_clubs FROM players", conn)

#### Top clubs supplying players
pd.read_sql("""
SELECT club, COUNT(*) AS player_count
FROM players
GROUP BY club
ORDER BY player_count DESC
LIMIT 15
""", conn)

### Project Overview

- The goal of the project is to track, monitor and analyse the different data assocated with the Fifa world cup.

<br>

---

#### Business Objecitves:

##### The objective of this dashboard is to provide a snap shot summary of some of the key metrics and statistics associated with the Fifa World cup. These include:

- Monitor KPIs in real time 
- Identify high-performing players, that have scored the most goals
- Identify the average age of the different teams at the world
- Identify the different clubs which have the highest representation at the world cup
- Identify the different stadium capacity and the average stadium capacity for all the venues at the world cup
  
###### The dashboard should allow users to dynamically filter data by:

- Each team/country

<br>

---

#### Tools
- Power Bi
- Power Query
- Dax
- Excel
- SQL
- Python

<br>

---

#### Key Metrics
Main KPIs
- Top goal scorer
- Average age per team/country

<br>

---


#### Insights
- Lionel Messi is currently leading the goalscoring charts after the group stage of the World Cup.
- Panama is the country with the highest average age, and Cote d'Ivoire have the lowest average.
- Aside from players without a club, Man City is the most represented club at the tournament
- The average stadium capacity for the tournamet is 64.61k with the Estadio Azteca having the highest capacity
- 278 distinct clubs are represented by players at the world cup
- There are 1242 players at the world cup at a combined average age of 27.97 

<br>


---


#### PowerBI-Fifa World Cup

![Fifa_World_Cup](Images/world-Cup-dashboard.png)
