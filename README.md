# League of Legends Vision Score Analysis
Final Project for DSC 80 at UCSD. Comprehensive report with multiple stages of analysis, with an emphasis on analyzing "Vision Score" in League of Legends. 

By Daniel Miles


## Introduction
### General Intro
In this project, I set out to examine a dataset consisting of professional League of Legends matches throughout 2022, from Oracle's Elixer. League of Legends is one of the most played games in the world, and given the large sums of competition money available in professional games, pro game analysis can be very interesting. 

The dataset captures several gameplay statistics, split into Team and Player statistics. One of these statistics is "Vision Score". At the end of a League of Legends match, you are presented with a Vision Score. I, and most people I play with, don't know much about it except a low vision score usually means you aren't placing enough wards. 

The main question I have from this dataset is **what statistics are connected to vision score**. I will focus on team statistics, as vision score differs greatly across positions, so team statistics is a good aggregate for it. 

At the end, as an experiment, we see how close we can get to predicting Vision Score at the end of the match, using all relevant data, to see how close we can get to Riot Game's own model. 

### Introduction of Columns
There are 148992 rows in the dataframe, where 124160 of those rows correspond to individual players, and 24832 of those rows correspond to teams. 

Here are some columns we will be focusing on:
|Column                |Description|
|---                |---        |
|`'gameid'`                |A unique identifier for individual matches. Useful for full match statistics, rather than one side.|
|`'league'`                |Represents which professional league played|
|`'side'`                |League of Legends games have two teams, this column represents which side the player/team is on.|
|`'result'`                |Outcome of the match. 1 is a win, 0 is a loss.|
|`'kills'`                |The number of kills the player or team got. |
|`'visionscore'`          | The visionscore in question.                         |
| `'dragons'`            | The total number of dragons slain by the team.                 |
| `'gamelength**'`         | The length of the game in minutes.                             |
| `'teamdeaths'`         | The total number of deaths by the team.                        |
| `'ckpm'`               | Combined kills per minute, a measure of game pace.             |
| `'elders'`             | The total number of Elder dragons slain by the team.           |
| `'firstbaron'`         | Whether the team secured the first Baron Nashor (True/False).  |
| `'firstherald'`        | Whether the team secured the first Rift Herald (True/False).   |
| `'firstdragon'`        | Whether the team secured the first dragon (True/False).        |
| `'barons'`             | The total number of Baron Nashor slain by the team.            |
| `'towers'`             | The total number of towers destroyed by the team.              |
| `'dpm'`                | Damage per minute dealt by the team.                           |
| `'wardsplaced'`        | The total number of wards placed by the team.                  |
| `'wpm'`                | Wards placed per minute by the team.                           |
| `'wardskilled'`        | The total number of enemy wards destroyed by the team.         |
| `'wcpm'`               | Wards cleared (killed) per minute by the team.                          |
| `'controlwardsbought'` | The total number of control wards bought by the team.          |
| `'earnedgold'`         | The total amount of gold earned by the team.                   |
| `'minionkills'`        | The total number of minions killed by the team.                |

There are more columns, but these are the most relevant to us. 

## Data Cleaning and Exploratory Data Analysis. 
I first added a 'resultstr' column, to convert 1 to "Won Match" and 0 to "Lost Match", to make plotting easier. I converted 'league' to a 'category' type, as it is categorical.

I then created two seperate dataframes. teams_df and players_df, representing teams and player statistics respectively, then dropping any irrevelant columns. 

I added some quartiles of columns, such as 'wpmqcut' and 'gamelengthqcut', representing the wards placed per minute quartile and gamelength , to make coloring some plots later easier. 

Lastly, I added visionscorediff to calculate the WIN-LOSS visionscore, and the visionscorerelativediff column to calculate self - other team. 

Teams DF: 
|    | league | result | teamkills | dragons | gamelength | teamdeaths | ckpm  | elders | firstbaron | firstherald | firstdragon | barons | towers | dpm     | wardsplaced | wpm   | wardskilled | wcpm  | controlwardsbought | earnedgold | minionkills | wpmqcut          | visionscoreqcut     | gamelengthqcut      | visionscorediff |
|----|--------|--------|-----------|---------|------------|------------|-------|--------|------------|-------------|-------------|--------|--------|---------|--------------|-------|--------------|-------|--------------------|-------------|--------------|-----------------|---------------------|---------------------|-----------------|
| 35 | 0      | False  | 6         | 1       | 1365       | 13         | 0.8352| 0      | False      | True        | False       | 0      | 3      | 1337.01| 64           | 2.8132| 34           | 1.4945| 26                 | 23237       | 680          | (2.662, 3.008]  | (39.999, 177.0]     | (920.999, 1656.0]   | 7               |
| 34 | 0      | True   | 13        | 2       | 1365       | 6          | 0.8352| 0      | False      | True        | False       | 1      | 8      | 1762.02| 79           | 3.4725| 33           | 1.4505| 32                 | 30167       | 680          | (3.374, 6.372]  | (39.999, 177.0]     | (920.999, 1656.0]   | 7               |
| 59 | 0      | False  | 8         | 1       | 1444       | 22         | 1.2465| 0      | False      | True        | False       | 0      | 2      | 1459.65| 82           | 3.4072| 21           | 0.8726| 29                 | 25048       | 680          | (3.374, 6.372]  | (39.999, 177.0]     | (920.999, 1656.0]   | 21              |


### Univariate Analysis
First, lets examine the length of typical League games. 
<iframe src="assets/gamelengthhist.html" width=800 height=600 frameBorder=0></iframe>

We can see that game length is approximately normal, with a slight right skew. Nothing unexpected. 


A more interesting histogram is one of vision score: 
<iframe src="assets/teamvisionhist.html" width=800 height=600 frameBorder=0></iframe>

It looks very similar to the game length histogram. Maybe there is a relation? We will explore this later.

Lastly, vision score doesn't really mean anything on its own. What might be more interesting is Winner - Loser vision score. 

Let's plot that. 
<iframe src="assets/teamvisiondiffhist.html" width=800 height=600 frameBorder=0></iframe>

This is much more normal looking, so we know that vision score difference is nice to work with, with no outliers, one peak, etc. 


### Bivariate Analysis

First, while we are focusing on team statistics, it would be interesting to see which positions get the most vision score:
<iframe src="assets/playervisionpositionhist.html" width=800 height=600 frameBorder=0></iframe>

It is interesting to note that top, mid, and bot all have low vision scores on average, but jungle has a little more, and support really goes up, with a right skew. 


Another plot to analyze is how the distributions of visionscore under result are different.
<iframe src="assets/teamvisionmatch.html" width=800 height=600 frameBorder=0></iframe>

They are both normal, but it is clear that among teams who have won, they score higher vision score on average. (We will formalize this with a permutation test later)


For our last bivariate plot, does game length affect wards placed per minute? Perhaps in later games, there is more money to place more wards. We could plot this in a scatterplot, but I find the relationship is more clear when we quartile game length, and color wards placed per minute histograms based on their game length quartile. 
<iframe src="assets/wpmgamelength.html" width=800 height=600 frameBorder=0></iframe>


### Interesting Aggregates
Before we move on to missingness analysis, it would be interesting to see if different positions have greater degrees of vision score difference, depending on winning or losing. 

| Position | Lost Match | Won Match | Win-Lose  |
|:---------|-----------:|----------:|----------:|
| Bot      |     34.6096|    41.191 |   6.58136 |
| Jng      |     40.6635|    45.6121|   4.94857 |
| Mid      |     31.4026|    36.2236|   4.82104 |
| Sup      |     75.9363|    82.6751|   6.7388  |
| Top      |     29.1722|    32.6319|   3.45973 |

This is surprising, despite the numbers being larger for support and jungle, it seems that the win-lose vision score difference is highest in bot and support. This could be due to how different lanes play when losing, where top lane can split push, leading to a losing position not losing a lot of vision score, compared to bot and support who get pushed back, and therefore can't get good vision score.

## Assessment of Missingness
### NMAR
I believe that the `'ban'` columns are NMAR. From my own experience in playing League of Legends, banning an opposing champion is optional, so it could be missing simply due to the fact that the player forgot to ban an opposing champion. Some additional data that might make it MAR could be AFK during champion select boolean, as typically no ban is because you were away from your computer after locking in your champion. 

### Missingness Dependency
The `'monsterkillsownjungle'` column could be interesting to use, but it is missing too many values for me to want to analyze it. One hypothesis I have as to why it is missing is that it is a difficult statistic to capture, and is therefore only captured for "interesting" games. One column that I believe could be highly correlated with this mystery "interestingness" variable is `'teamkills'`, so I will run a permutation test to evaluate if `'monsterkillsownjungle'` missingness depends on `'teamkills'`, with a significance level of 0.01, given the large dataset size.

**Null Hypothesis:** Distribution of `'teamkills'` when `'monsterkillsownjungle'` is missing is the same as the distribution of `'teamkills'` when `'monsterkillsownjungle'` is not missing. 


**Alternative Hypothesis:** Distribution of `'teamkills'` when `'monsterkillsownjungle'` is missing is ***not*** the same as the distribution of `'teamkills'` when `'monsterkillsownjungle'` is not missing.


<iframe src="assets/teamkillsmissing.html" width=800 height=600 frameBorder=0></iframe>

From this plot, we can already tell that the distributions look different (but actually, in the other direction than I hypothesized!)

<iframe src="assets/teamkillmissinght.html" width=800 height=600 frameBorder=0></iframe>

The above plot comes from permuting `'missing'`, and calculating a difference in means. 

With an observed difference of over 2.1, we can say that p=1/3001 (the number of repetitions I chose, as zero permuted mean differences surpassed 2.1, but the p value must still show that we only ran 3000 permutations).

Since p-value is less than 0.05 significance level, we reject the null hypothesis, suggesting that there is a statistically significant dependence between the missing data in `'monsterkillsownjungle'` and `'teamkills'`



I picked `'elders'` as the other column to check, as it feels like games with high elders might be very long games AND be jungle intensive games, where it is difficult to count up all of the jungle monsters killed. I choose a significance level of 0.01, given the large dataset size.


<iframe src="assets/eldersmissing.html" width=800 height=600 frameBorder=0></iframe>

This time, it looks like those two distributions are equal, but let's run a permutation test to make sure.

**Null Hypothesis:** Distribution of `'elders'` when `'monsterkillsownjungle'` is missing is the same as the distribution of `'elders'` when `'monsterkillsownjungle'` is not missing. 


**Alternative Hypothesis:** Distribution of `'elders'` when `'monsterkillsownjungle'` is missing is ***not*** the same as the distribution of `'elders'` when `'monsterkillsownjungle'` is not missing.

<iframe src="assets/eldersmissinght.html" width=800 height=600 frameBorder=0></iframe>

We get a p value of 0.427, leading us to fail to reject the null hypothesis. There is not enough data to say that there is a statistically significant dependence between the missing data in  `'monsterkillsownjungle'` and `'elders'`.



## Hypothesis Testing

A question I have always wondered is, how much does vision score affect whether you won or lost? To start to answer this, we need to first see whether a relationship exists at all. To answer this, I will run a permutation test on the following hypotheses:

**NULL:** There is no effect of team vision score on win rate. -> vision score comes from one distribution

**ALT:** There is an effect of team vision score on win rate. -> vision score comes from two different distributions

We saw in Data Exploration that it is almost certain that these distributions are different, and we will now formalize that with a permutation test. The test statistic I will be using is difference in group means, as we saw in Data Exploration that the distributions are mostly normal, so this is a fine way to compare distributions. I chose a significance level of 0.01, given the large dataset size.

|    |   Lost Match |   Won Match |
|:---|-------------:|------------:|
| visionscore |  211.789 |  238.336 |

To reiterate, this is the mean vision score across losing and winning we observe. 

Our permutation test results in the following empirical distribution of mean differences plot, with the observed difference we have marked in red.

<iframe src="assets/empiricalvisionscore.html" width=800 height=600 frameBorder=0></iframe>

With a p value of 1/5001 (we cannot say 0, as it could be the fact that we did not run enough simulations), we have enough data to suggest that we should reject the null hypothesis, and given the direction seen on the empirical distribution, there seems to be a statistically significant increase in vision score for winning teams. 


## Framing a Prediction Problem

The prediction problem I want to solve is: *Can we predict team vision score, given all the information available to us at the end of a match?*

Specifically, I do not care about interpreting results, I simply want to find out if it is possible to match how League of Legends calculates vision score, given the data we have at the end of the match. Because this is a regression problem, I will be using Root Mean Squared Error, as this is an easily interpretable error method. 

Because we are trying to match the vision score algorithm calculated at the end of a match, we can use every column in the dataset. 

## Baseline Model

The baseline model I have selected is a Decision Tree Regressor, as that is the same way I would solve this problem by hand (look at result mean averages, then do an approximate linear regression on gamelength after that). The features I am giving it are `'result'` and `'gamelength'`, as from data exploration these seemed to both highly influence vision score. No encodings were done.

Result is nominal, and gamelength is quantitative. 

Running this decision tree regressor, we get a test RMSE of 33.4, which is very good already. Vision score is approximately normal, with a standard deviation of 67.66, so we are already able to predict to within around half a standard deviation. 

## Final Model

The features my model uses are the following:
`'league','result','teamkills','dragons',
'gamelength','teamdeaths','ckpm','dragons','elders',
'firstbaron','firstherald','firstdragon','barons','towers',
'dpm', 'wardsplaced', 'wpm', 'wardskilled', 
'wcpm', 'controlwardsbought', 'earnedgold',
'minionkills', 'wpmqcut', 'gamelengthqcut'`

It would take too long to go into each one individually, but each one was chosen to have some theoretical framework in affecting vision score. Different Leagues might value wards differently, teamkills can show how well a team is using vision, as can all jungle information. Creep kills per minute influences how safe laners feel, which is increased by good vision. Earned gold is another metric of safeness, where you can increase your farm if you know what the opposing team is up to. Wards and control wards are simple additions as they are what actually give vision score. 

I added the quartiles for wpm and gamelength as a way to increase robustness against overfitting, while still keeping raw wpm and gamelength to preserve feature information. 


Because of the >10,000 number of rows, I chose `HistGradientBoostingRegressor`, as I knew my columns would be full of multicollinearity, and I wanted my model to handle that for me, while also finding complex nonlinear relationships between data that boosted regressors excell at. The hyperparameters that ended up peforming the best are:

`'learning_rate': 0.05,
 'max_depth': 4,
 'max_iter': 1000,
 'max_leaf_nodes': 5,
 'min_samples_leaf': 1
 `

 I don't have any theoretical framework to back this up, it was found using a GridSearchCV on small subsets, and narrowing it down while increasing cross validation fold size, with mean squared error as by scoring metric. 

This model ended up getting a RMSE of 17.39 on unseen data (not train or validate), so we have halfed the error from baseline, and now represent 0.25 of a standard deviation. I would say that this is a success, as 17 is a small enough value to be insignificant when looking at the vision score histogram. 


## Fairness Analysis
I want to see if my model peforms better at predicting a teams vision score depending on if they won or lost. To do this, I will run a permutation test with the following hypotheses:

NH: Model is fair. Its RMSE for losers and winners are roughly the same, and any differences are due to random chance

AH: The model is unfair. Its RMSE for losers is different than its RMSE for winners

I am choosing a significance level of 0.01 given how much data I have. 

After running the test, I have a p-value of 0.96, so I lack data to suggest rejecting the null hypothesis. The given data suggests that I fail to reject the null hypothesis, and that my model is fair across winning and losing. 

