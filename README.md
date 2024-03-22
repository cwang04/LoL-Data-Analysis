# League of Legends Champion and Role Data Analysis
### Colin Wang and Ciro Zhang

## Introduction

League of Legends (LoL) is a massively popular Multiplayer Online Battle Arena game, where players pick champions out of diverse roster who have different skills and abilities. The data set that we are working with contains data for progames played in the 2022 season. 

The question that we decided to investigate using this data set was asking "Are the champions' winrates in League of Legends balanced?" To anyone interested in LoL, balance is a constant point of contention within fans and the developers as players constantly complain how unbalanecd the game is, and our project aims to quantify how balanced champs were in 2022 pro season. We also looked into a prediction question to see "What role did a player play?" We can look at this classifier and apply it to the "Normal" game mode in league of legends where players are not assigned strict roles. We can apply our classifier trained on this data and use it to predict roles for norms players.

After selecting the relevant rows and columns for our dataset, we were left with 124,150 rows, ten rows for each match and each row representing an individual player. We only opted to keep 20 of the 131 total rows. The rows are as follows:

<table>
    <thead>
        <tr>
            <th>Column Name</th>
            <th>Description</th>
        </tr>
    </thead>
    <tbody>
        <td><code>patch</code></td>
        <td>This is the patch number the match was played on, in the format 12.XX</td>
    </tbody>
    <tbody>
        <td><code>league</code></td>
        <td>The league/tournament that the match was played in</td>
    </tbody>
    <tbody>
        <td><code>position</code></td>
        <td>The role that a player played in the match (top, jng, mid, bot, sup)</td>
    </tbody>
    <tbody>
        <td><code>result</code></td>
        <td>1 if the player won the game or 0 if they lost</td>
    </tbody>
    <tbody>
        <td><code>gamelength</code></td>
        <td>The length of the game in seconds</td>
    </tbody>
    <tbody>
        <td><code>champion</code></td>
        <td>The champion that a player played for the match</td>
    </tbody>
    <tbody>
        <td><code>kills</code></td>
        <td>The number of kills that player got in a match</td>
    </tbody>
    <tbody>
        <td><code>deaths</code></td>
        <td>The number of times a player died during a match</td>
    </tbody>
    <tbody>
        <td><code>assists</code></td>
        <td>The number of assists a player got during a match</td>
    </tbody>
    <tbody>
        <td><code>doublekills</code></td>
        <td>The number of doublekills a player got during a match</td>
    </tbody>
    <tbody>
        <td><code>dpm</code></td>
        <td>The amount of damage per minute a player dealt during the match</td>
    </tbody>
    <tbody>
        <td><code>damagetakenperminute</code></td>
        <td>The amount of damage per minute taken by a player during the match</td>
    </tbody>
    <tbody>
        <td><code>wpm</code></td>
        <td>The number of wards placed by a player per minute</td>
    </tbody>
    <tbody>
        <td><code>wcpm</code></td>
        <td>The number of wards destroyed by a player per minute</td>
    </tbody>
    <tbody>
        <td><code>vspm</code></td>
        <td>The vision score per minute of each player in the match</td>
    </tbody>
    <tbody>
        <td><code>totalgold</code></td>
        <td>The total gold a player has by the end of the match</td>
    </tbody>
    <tbody>
        <td><code>earned gpm</code></td>
        <td>The total gold earned by a player per minute in the match </td>
    </tbody>
    <tbody>
        <td><code>goldspent</code></td>
        <td>The total amount of gold spent by a player in the match </td>
    </tbody>
    <tbody>
        <td><code>minionkills</code></td>
        <td>The number of minions a player has killed in the match </td>
    </tbody>
    <tbody>
        <td><code>monsterkills</code></td>
        <td>The number of jungle monsters a player has killed in the match </td>
    </tbody>
</table>

## Data Cleaning
The first consideration that we took when doing data cleaning was analyzing which columns we would need to answer our two questions: "Are the champions of League of Legends balanced?" and "What role did a player play?" The only data columns that we kept are listed in the table above, and these were chosen since they were individual stats corresponding with each player that we could use for our classification. Some other columns were kept, `patch` was kept in order to perform a fairness analysis, `league` was chosen to factor in differences in playstyle in champion classifcation, `champion` was kept for our hypothesis question as well as an additional feature for classification, `result` was kept for our hypothesis question to calculate winrate, and finally `gamelength` was kept in order to standardize our data. 

Since our hypothesis and classification questions were mainly focused on looking at individuals, we made the decision to drop the coaching slots from our data set. There are 2 additional rows in each game dedicated to team coaches, however the stats included in these rows were not relevant for our individual centric questions. Because of this, we decided to drop all of the coaches from the data set. Another set of rows that we ended up removing was 10 rows for the game with id: `8479-8479_game_1` because we supsect that the data for this match had been corrupted since we were missing a significant amount of data from this game.

The next step of data cleaning we did was conditional probabilistic imputation on the column `doublekills`, since it seemed that a large portion of our data set, ~18,000 rows, seemed to have a missing amount for our data set. We used conditional probabilistic imputation on the NaN values of doublekills based off of the `position` and `kills` features in order to fill in this missing data while preserving variance. Another small change we made during data cleaning was converting the column `damagetakenperminute` to `dtpm` to abbreviate it and make it closer to `dpm`. We also reset the index of the cleaned data frame.

The final change we made during data cleaning was standardizing `kills`, `deaths`, `assists`, `doublekills`, `totalgold`, `goldspent`, `minionkills`, and `monsterkills`to be per minute like `dpm` so we divided by (`gamelength` / 60).

## Framing a Prediction Problem

We will be attempting to predict the role of the player by building a multiclass classification model.

In League of Legends, each teams has 5 position:
*  top: top lane player
*  mid: middle lane player
*  bot: bottom lane player
*  sup: support player
*  jng: jungle player
  
We will ignore the team coaches as they do not play the actual game.

This is a classification problem as we are trying to sort data into one of 5 possible roles or classes, which requires us to divide the entries based on certain features which are more aligned with a classification algorithm since we are not predicting a continuous value but rather one of 5 possibilities.

In League of Legend if you play "Blind Pick", the standard non-competitive game mode, players enter the game without an assigned role and must decide themselve who plays what. However this also means that we do not know who played which role after the game has ended without directly asking the players what roles they played. Thus our model aims to predict the roles of each players by looking at the characters they played and the stats they preformed.

## Baseline Model

The categorical features we will feed to our model are:
*  "Champion"
*  "League"

The quantitative features we will feed to our model are:
*  "killspm"
*  "deathspm"
*  "assistspm"
  
"champion" will probably be the strongest feature in determining the players' roles. In LOL, many champions are fixed to a role. For example, champion Caitlyn is exclusively played as an ADC. Thus, although some characters can also play in multiple lines, these nominal features will give our model a rough idea of what role is most likely for that feature.

In LOL, the play style of different regions sometimes differ greatly. For example the play styles of people in Asia is a lot different then North America. Thus using a nominal feature like "league" that gives us the regions of these games where played allows model to account for these regional differences.

In the league, "killspm," "deathspm," and "assistspm," or sometimes generally referred to just as "KDA", is often used to determine the general "score" of each player. Typically, roles like supports and jungles should have higher "assistspm" as "helpers" to the game, while roles like bot and mid should have higher "killspm" as the "carries" of the game. Thus, this quantitative feature should help our model determine these roles.

Our pipeline:
*   For the champion, we will use the OneHotEncoder to convert it into numerical data.
*   For the league, we will first convert the league into their respected region using a dictionary, then convert it into numerical data using OneHotEncoder
