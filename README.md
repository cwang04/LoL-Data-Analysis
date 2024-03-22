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


## Assessment of Missingness

The columns that we identified as likely being not missing at random (NMAR) were the columns for bans 1-5. In a league of legends game, champions are banned so that neither team can pick them in the game, however they also have the option of opting to not ban. In this case their ban would be considered as a missing data point. Because they only way we would know whether or not a player skipped their ban is based on the outcome of their ban pick, and since that is the data that we are missing we can not verifiably conclude whether or not the data will be missing based on other columns. One additional piece of information that might help us predict this would be adding the column of "prize pool" or some other value that would indicate the stakes of the match. At higher stakes matches players are less likely to skip their ban since it gives them a competitive advantage.

## Hypothesis Testing

Question: Are the Champions in League of Legends balanced?

Prehabs one of the most long running question in the history of League of Legends. The ultimate goal of good balancing team is that every character in the game is equally playable. This means every character should have roughly the same win and lose rate with one and other. To answer this we will preform a permutation test with a significance level of 0.05


Clearly state your null and alternative hypotheses, your choice of test statistic and significance level, the resulting 
p
-value, and your conclusion. Justify why these choices are good choices for answering the question you are trying to answer.

Null Hypothese: The champions in League of Legends are balanced. TVD = 0
Alternative Hypotheses: The champions in League of Legends are not balanced. TVD =\= 0

Test Statistic = Total Variance Distance of winrate vs lossrate 

We want to use TVD here since we are comparing the numerical values of categorical values, champions vs win + loss rate



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

**The categorical features we will feed to our model are:**
*  "Champion"
*  "League"

**The quantitative features we will feed to our model are:**
*  "killspm"
*  "deathspm"
*  "assistspm"
  
"champion" will probably be the strongest feature in determining the players' roles. In LOL, many champions are fixed to a role. For example, champion Caitlyn is exclusively played as an ADC. Thus, although some characters can also play in multiple lines, these nominal features will give our model a rough idea of what role is most likely for that feature.

In LOL, the play style of different regions sometimes differ greatly. For example the play styles of people in Asia is a lot different then North America. Thus using a nominal feature like "league" that gives us the regions of these games where played allows model to account for these regional differences.

In the league, "killspm," "deathspm," and "assistspm," or sometimes generally referred to just as "KDA", is often used to determine the general "score" of each player. Typically, roles like supports and jungles should have higher "assistspm" as "helpers" to the game, while roles like bot and mid should have higher "killspm" as the "carries" of the game. Thus, this quantitative feature should help our model determine these roles.

**Our pipeline:**
*   For the champion, we will use the OneHotEncoder to convert it into numerical data.
*   For the league, we will first convert the league into their respected region using a dictionary, then convert it into numerical data using OneHotEncoder

This model preforms an accuracy ~ 0.933, on the hidden test_sample, which isn't bad but still has many places we can improve on. 
*   Issue 1: Our data compares the actual values to one and other, rather then its preformance compared to others. Which may protentially create a lot of unnecessary noise in our predictions
*   Issue 2: Our model cannot differentiate edge cases. For example some champions are mostly played as bot but sometime also played as top, or some strategies involve having the jungler as the carry rather then the mid or bot.

Solution: added more feature and standardize them. 

## Final Model

For our final model we will also include all the players stats:

**Kill_stats: Quantitative**
*   "doublekillspm"

doublekillspm, which is a quantitative value could help use determine bot roles. In LOL since the bottem lane is usually played by two players, bot and sup, this means that bot are more common to get doubles kills compared to any other role. 

**Econ_stats: Quantitative**
*   "earned gpm"
*   "goldspentpm"
*   "minionkillspm"
*   "monsterkillspm"

Econ_stats like "earned gpm", "goldspentpm", "minionkillspm" and "monsterkillspm" helps us differentiate each roles from one and other. In league, supports do not earn a lot of gold. Thus a low "earned gpm" will indentify support. In the other hand, mid has the most expensive items, thus a high goldspentpm should lean towards mid. One unique properties of jng is that it gains it gold from monsters rather then minion, so a high "monsterkillspm" should guarantee jng. And on the other side, mid, bot and top gains their gold from minions, thus a high "minionkillspm".

**Vision_stats: Quantitative**
*   "wpm"
*   "vspm"

Vision_stats like "wpm", "vspm", helps us differentiate support and jungler from the rest. In LOL both roles tends to move around the map to help other line. And because of this these two roles tend to have high vision score or "vspm". In addition support are unique as they are given extras wards, an item that increases vision scores, resulting in not only have a high "vspm" but also a high "wpm". 

**Damage_stats: Quantitative**
*   "dpm"
*   "dtpm"

Damage_stats like "dpm" and "dtpm" helps us differentiate top from both bot and mid more effectively. In LOL, although many games can result in both the bot, top and mid players all becoming the carries, meaning high KDA, of their team. However, when this happens, "dpm" and "dtpm" help us identify who si who. Since top tends to have the tankiest champions and fight in a short range, when they carry they tend to have both a high "dpm" and "dtpm". While in the hand when bot and mid carries, since they fight in a long range, they then to have a high "dpm" but low "dtpm". Thus this stats will help us differentiate in many of these edge cases. 


**Designing Our New pipeline:**

Our goal here is to standardized  everything. Since we dont actually care about the actual value just how much it differs to others. In addition, this process will also help reduce much of the unwanted noise. 

Here something we must watch out for when deciding StandardScaler() or QuantileTransformer() to standarize. While StandardScaler is effected by outlier and QuantileTransformer only work under an assumption of an uniform distribution. 

So the main strategy we choose was: 
less likely for an outlier to occur -> use StandardScaler
likely for an extreme outliner to occur -> use QuantileTransformer

**The pipeline:**
*   We will keep the same encoding for "Champion" and "League" in our previous model

*   QuantileTransformer_Damage_stats: "dpm", "dtpm"

Although we standized everything by time, "dpm", "dtpm" can still get extremely high when the game get extremely long. This is because the character themself scale over time, some forever. So as time passes in some outlier game, "dpm", "dtpm" will get unreasonably high, thus we cannot use StandardScaler and must use QuantileTransformer.
  
*   StandardScaler_Kill_stats: "killspm", "deathspm", "assistspm", "doublekillspm"
*   StandardScaler_Econ_stats: "earned gpm", "goldspentpm", "minionkillspm", "monsterkillspm"
*   StandardScaler_Vision_stats: "wpm",	"vspm"

Kill stats are hard to get extreme values because of league's respawn time, the time that take a player to get back into the game after getting killed. In league as character get stronger, so do the time they take to responed. Thus even if hypothetically one game get super long, the maximum "killspm", "deathspm", "assistspm" or "doublekillspm" will cap at some point. 
  
Econ stats have the same property. Although stronger character can kill minions or monster faster, there is a maximum amount of gold a player can hypothetically get at some point. Thus the chance of a extremely outlier is low. 

Vision stats are even more likely to have outliers as they have no connection with the strength of the character. As they are based on how much the character spots an enemy not their actual strength

*   Binarizer_Monsterkillspm: "monsterkillspm"

"monsterkillspm" is very unique because thoughout our training we realized that this feature is super consistent at predicting jng, but not so good for any other role. This makes sense, as typically only the jng takes the camps. Thus inorder to reduce the unwanted noices, aka using this feature to predict other roles. Thus we used a Binarizer with a threshold of the average "monsterkillspm" to just seperate jng from the rest. 

**Hyperparameters:**

The hyperparameters we want to find is max_depth and n_estimators for our RandomForestClassifier. The main goal is to find these parameter that can correct predict the roles of the character in our train_sample and test_sample, not under or overfitting. 

For this we ran a GridSearchCV that searched max_depth from 10 to 80 and n_estimators from 40 to 160. The resulting hyperparameters we located was 
*   max_depth=76
*   n_estimators=160

Our new model preforms an accuracy ~ 0.965 on the hidden test_sample, which was great inprovement from last time.

## Fairness Analysis


**Confusion Matrix**
<table>
  <tr>
    <th>role</th>
    <th>precision</th>
    <th>recall</th>
  </tr>
  <tr>
    <td>top</td>
    <td>0.947379</td>
    <td>0.939484</td>
  </tr>
  <tr>
    <td>mid</td>
    <td>0.927455</td>
    <td>0.934006</td>
  </tr>
  <tr>
    <td>bot</td>
    <td>0.967310</td>
    <td>0.961065</td>
  </tr>
  <tr>
    <td>jng</td>
    <td>0.998580</td>
    <td>0.998783</td>
  </tr>
  <tr>
    <td>sup</td>
    <td>0.983153</td>
    <td>0.990703</td>
  </tr>
</table>

Here we can see that our model is better at predicting supports and junglers. 

<br>

Now let's look more into the fairness of our model over certain groups

One group that we want to check for fairness in our model would be over different patches. In LoL the meta tends to shift from patch to patch as balance changes are made to champions. This causes meta changes that could impact the way that the roles are played, meaning that our model might make some mistakes if the meta shifts in a way such that mid lane is more impactful in terms of damage or another change in the way that the game is played.

In order to check the fairness of our model, we need to run a permutation test over predictions from each patch in order to determine if our model has the same precision and recall over all of the patches.

Here we will do two separate Permutation tests, one judging the fairness of our model by using accuracy parity and one using precision parity. 
Here we will also look at patch 12.21 since it had the lowest average precision and recall out of all the other patches and use it to compare to the overall average precision and recall. For both test We will a significant value of 0.05. 

Hypothesis 1: Precision
*   Null Hypothesis: Our model is fair, and its precision for each patch should be roughly the same, and any differences can be attributed to random chance.
*   Alternative Hypothesis: Our model is biased, its precision for patch 12.21 will be lower than the rest of the patches, since patch 12.21 had the most extreme outlier for being lower than the rest of the data.
*   The Test Statistic: the average precision
  
<iframe
  src="assets/q8graph1.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Hypothesis 2: Recall
*   Null Hypothesis: Our model is fair, and its recall for each patch should be roughly the same, and any differences can be attributed to random chance.
*   Alternative Hypothesis: Our model is biased, its recall for patch 12.21 will be lower than the rest of the patches, since patch 12.21 had the most extreme outlier for being lower than the rest of * the data.
*   The Test Statistic: the average recall 

<iframe
  src="assets/q8graph2.html"
  width="800"
  height="600"
  frameborder="0"
></iframe></iframe>

From the p-value of 0.0 for both recall and precision we can see that the observed precision and recall are much lower than we can attribute to simply random chance and we can see that our model seems to do worse at predicting for 12.21 compared to other patches. However, since our model has a very high precision and recall normally, this is not the worst thing in the world. 
One possible reason for this patch being biased against our model is the introduction of the new top lane champion K'Sante, who greatly shifted the meta for top laners that patch. 
This may be the reason that our model suffered in recall and precisio



