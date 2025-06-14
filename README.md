# Analysis of Early Advantages in League of Legends

## Introduction
In this analysis, we explore a [**dataset**](https://drive.google.com/drive/u/1/folders/1gLSw0RLjBbtaNy0dgnGQDAZOHIgCe-HH) collected from competitive League of Legends matches, with the goal of understanding how early advantages drive success in these matches, and predicting whether a player will win a match based on their in-game performance data. The dataset includes detailed statistics from thousands of matches, capturing each player's performance at key time intervals (10, 15, and 25 minutes into a match).

Our central question is: Can we accurately predict whether a team will win a match using only in-game data from the first 25 minutes? 

This question is especially important for game developers, esports teams, and casual players. Predictive models can help improve ranked matchmaking, inform coaching decisions, and be used to improve gameplay overall. The dataset contains around 150,000 rows of player and team data, with the following columns being particularly relevant to our analysis:

**result:** 
: Whether the player won the match (True) or lost (False). This is the target variable we aim to predict.

**golddiffat25:** 
: The difference in gold between the player’s team and the enemy team at 25 minutes.

**xpdiffat25:** 
: Experience point difference at 25 minutes.

**csdiffat25:** 
: Creep score (minion kills) difference at 25 minutes.

**position:** 
: The player's role in the match (e.g., top, jungle, mid).

**gamelength:** 
: The total length of the match

# Data Cleaning and EDA
### Univariate Analysis
<iframe
  src="assets/gamesplayed.html"
  width="400"
  height="400"
  frameborder="0"
></iframe>
Visualizing the frequency of games played by LoL players shows how professional experience is distributed among the participants. The skewedness of experience is something to take into account when predicting what can contribute to winning a match, especially without using much in-game data.

### Bivariate Analysis
<iframe
  src="assets/quadsquad.html"
  width="500"
  height="700"
  frameborder="0"
></iframe>
Observing the distributions of gold and kda metrics at the 15 and 25 minute marks, we see that the winning team typically has a clear advantage in both stats by the 15 minute mark, and this trend is even stronger after 25 minutes.

### Interesting Aggregates

| Position | Total Gold |
|-----------|-----------|
| bottom | 13648 |
| jungle | 10788 |
| mid | 12591 |
| support | 7597 |
| top | 12293 |

This table demonstrates the lesser gold-accumulating nature of jungle and support roles. The differing roles of league of legends players is something we should take into account when considering individual player performance, and gold farming will likely be best examined with respect to these roles.


# Assessment of Missingness

### NMAR analysis
The NMAR variables are most likely the ones involving some sort of "first", where matches that didn't have a first would likely not have an entry in the data. Otherwise, most other missing columns appear to be missing completely at random.

### Missingness Dependency
When golddiffat25 is missing, killsat25 is always missing as well, not because they are necessarily dependent on each other, but because the entries with "partial" completeness tend to be missing the stats taken at 10, 15, 25, etc. minutes. Thus, if one of these are missing it is generally safe to conclude that the others will be missing as well. On the other hand, the "result" variable is not dependent on the missingness of gold difference at 25 minutes.

# Hypothesis Testing
### Does an early gold lead increase win probability?
**Null Hypothesis:** Teams with a positive gold difference at 25 minutes win at the same rate as teams with a negative or zero gold difference. Any observed difference is due to random chance.

**Alternative Hypothesis:** Teams with a positive gold difference at 25 minutes win more often than those with a non-positive gold difference.

This hypothesis was tested using a permutation test, with the test statistic being a difference in observed win rates between the two groups. The result was a p value of 0.0, suggesting that a positive gold difference at 25 minutes is significantly associated with a higher chance of winning.

### Do more early kills increase win probability?
**Null Hypothesis:** There is no difference in win rate between teams with kd25 ≥ 1 and those with kd25 < 1. Any observed difference is due to random chance.

**Alternative Hypothesis:** Teams with kd25 ≥ 1 have a higher win rate than those with kd25 < 1.

This hypothesis was also tested with a permutation test using observed difference in win rate between these two groups as the test statistic. Similarly, the p value was 0.0, potentially indicating that a positive kill-death ratio at 25 minutes is statistically associated with a higher probability of winning.

These tests together provide empirical evidence that early leads in both resources (gold) and combat performance (kills) significantly increase the likelihood of winning. This supports a broader understanding of League of Legends as a game where early advantages can snowball into victory, and underscores the importance of early-game strategy and coordination.

# Framing a Prediction Problem
The goal of this project is to predict whether a team will win or lose a League of Legends match based on in-game performance metrics recorded 25 minutes into the match. Namely, we will use a binary classifier that predicts the outcome of the game (win/loss) based on team data such as experience, side, and gold, xp, kills, and deaths at 10, 15, and 25 minute marks.

# Baseline Model
We developed a binary classification model to predict whether a team will win a League of Legends match using data collected 25 minutes into the game. The model is implemented using a scikit-learn Pipeline, allowing for efficient preprocessing and model training in a single, streamlined workflow. We used a Random Forest Classifier in our model, and engineered several features to provide a good baseline.
### Features
**Numeric features**

    golddiffat25: Gold difference at 25 minutes

    xpdiffat25: Experience difference at 25 minutes

    kda25: Kill-death-assist ratio at 25 minutes

    totaldamageat25: Total damage dealt by the team at 25 minutes

    turretdiffat25: Difference in number of turrets destroyed at 25 minutes

**Transformed features**

    experienced: A binary feature indicating if the player had played more than 50 games. Created using Binarizer and included as a standalone binary feature.

    position: Role or position of a key player (e.g., Top, Jungle, Mid, ADC, Support), encoded using One-Hot Encoder

### Performance
Overall, the model recorded a score of .838 using the above features. It shows strong performance on the F1-score metric, indicating a good balance of precision and ability to generalize for unseen data. It includes well-chosen features grounded in domain knowledge, and overall is good at predicting the outcome of sample matches. The high training accuracy indicates that the model is not overfitting.


# Final Model 
For the final model, we added two new features based on domain knowledge of how League of Legends matches progress:

    goldxp: golddiffat25 × xpdiffat25

    gamesplayed: Total number of games played by members of a given team

These features align well with the data-generating process in League of Legends, where teams win by converting early/mid-game resource leads into victory. Combining indicators of those leads helps capture game state more holistically. Experience, as demonstrated earlier, is also an uneven trend that can affect a team's chance of winning, and should thus be included in the final model. The addition of these variables caused the final model to outperform the baseline by about 2% in test accuracy.

# Fairness Analysis
We then determined if our final model is fair with predicting match outcomes based on player experience level. We defined the two groups as low and high experienced, based on if the team has more than 3 "highly experienced" players. We used the difference in accuracy scores as the metric for our hypotheses.

**Null Hypothesis:** The model is fair, achieves similar precision for both experienced and inexperienced teams.

**Alternative Hypothesis:** The model is unfair, has lower precision for inexperienced teams (Group X) than for experienced teams (Group Y).

This resulted in a p-value of .07, meaning we fail to reject the null hypothesis at the .05 significance level. This suggests that there is not enough statistical evidence to conclude that the model is unfair with respect to team experience level. While there may be a small observed difference in precision, it could plausibly be due to random chance rather than a systematic bias in the model.