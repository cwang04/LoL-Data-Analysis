# League of Legends Champion and Role Data Analysis
### Colin Wang and Ciro Zhang

## Introduction

League of Legends (LoL) is a massively popular Multiplayer Online Battle Arena game, where players pick champions out of diverse roster who have different skills and abilities. The data set that we are working with contains data for progames played in the 2022 season. 

The question that we decided to investigate using this data set was asking "Are the champions' winrates in League of Legends balanced?" To anyone interested in LoL, balance is a constant point of contention within fans and the developers as players constantly complain how unbalanecd the game is, and our project aims to quantify how balanced champs were in 2022 pro season. We also looked into a prediction question to see "What role did a player play?" We can look at this classifier and apply it to the "Normal" game mode in league of legends where players are not assigned strict roles. We can apply our classifier trained on this data and use it to predict roles for norms players.

After selecting the relevant rows and columns for our dataset, we were left with 124,150 rows, ten rows for each match and each row representing an individual player. We only opted to keep 20 of the 131 total rows. The rows are as follows:
| Column Name | Description |
|-------------|------------ |
| `patch` | This is the patch number the match was played on, in the format 12.XX |
| `league` | The league/tournament that the match was played in |
| `position` | The role that a player played in the match (top, jng, mid, bot, sup)|
| `result` | 1 if the player won the game or 0 if they lost |
| `gamelength` | The length of the game in seconds |
| `champion` | The champion that a player played for the match |
| `kills` | The number of kills that player got in a match |
| `deaths` | The number of times a player died during a match |
| `assists` | The number of assists a player got during a match |
| `doublekills` | The number of doublekills a player got during a match |
| `dpm` | The amount of damage per minute a player dealt during the match |
| `damagetakenperminute` | The amount of damage per minute taken by a player during the match |
| `wpm` | The number of wards placed by a player per minute |
| `wcpm` | The number of wards destroyed by a player per minute |
| `vspm` | The vision score per minute of each player in the match |
| `totalgold` | The total gold a player has by the end of the match |
| `earned gpm` | The total gold earned by a player per minute in the match |
| `goldspent` | The total amount of gold spent by a player in the match |
| `minionkills` | The number of minions a player has killed in the match |
| `monsterkills` | The number of jungle monsters a player has killed in the match |

## Data Cleaning
The first consideration that we took when doing data cleaning was analyzing which columns we would need to answer our two questions: "Are the champions of League of Legends balanced?" and "What role did a player play?" The only data columns that we kept are listed in the table above, and these were chosen since they were individual stats corresponding with each player that we could use for our classification. Some other columns were kept, `patch` was kept in order to perform a fairness analysis, `league` was chosen to factor in differences in playstyle in champion classifcation, `champion` was kept for our hypothesis question as well as an additional feature for classification, `result` was kept for our hypothesis question to calculate winrate, and finally `gamelength` was kept in order to standardize our data. 

Since our hypothesis and classification questions were mainly focused on looking at individuals, we made the decision to drop the coaching slots from our data set. There are 2 additional rows in each game dedicated to team coaches, however the stats included in these rows were not relevant for our individual centric questions. Because of this, we decided to drop all of the coaches from the data set. Another set of rows that we ended up removing was 10 rows for the game with id: `8479-8479_game_1` because we supsect that the data for this match had been corrupted since we were missing a significant amount of data from this game.

The next step of data cleaning we did was conditional probabilistic imputation on the column `doublekills`, since it seemed that a large portion of our data set, ~18,000 rows, seemed to have a missing amount for our data set. We used conditional probabilistic imputation on the NaN values of doublekills based off of the `position` and `kills` features in order to fill in this missing data while preserving variance. Another small change we made during data cleaning was converting the column `damagetakenperminute` to `dtpm` to abbreviate it and make it closer to `dpm`. We also reset the index of the cleaned data frame.

The final change we made during data cleaning was standardizing `kills`, `deaths`, `assists`, `doublekills`, `totalgold`, `goldspent`, `minionkills`, and `monsterkills`to be per minute like `dpm` so we divided by (`gamelength` / 60).