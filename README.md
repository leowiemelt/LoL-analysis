# Analysis of Early Advantages in League of Legends

# Introduction
In this project, we explore a dataset collected from competitive League of Legends matches, with the goal of understanding how early advantages drive success in these matches, and predicting whether a player will win a match based on their in-game performance data. The dataset includes detailed statistics from thousands of matches, capturing each player's performance at key time intervals (10, 15, and 25 minutes into a match).

Our central question is: Can we accurately predict whether a player will win a match using only in-game data from the first 25 minutes? And does our model perform fairly across players with different levels of experience? 

This question is especially important for game developers, esports analysts, and users. Predictive models can help improve ranked matchmaking, inform coaching decisions, and be used to improve gameplay overall. The dataset contains around 150,000 rows of player and team data, with the following columns being particularly relevant to our analysis:

result: Whether the player won the match (True) or lost (False). This is the target variable we aim to predict.

golddiffat25: The difference in gold between the player’s team and the enemy team at 25 minutes.

xpdiffat25: Experience point difference at 25 minutes.

csdiffat25: Creep score (minion kills) difference at 25 minutes.

position: The player's role in the match (e.g., top, jungle, mid).

gamelength: The total length of the match

By building a classification model based on these features, we aim not only to make accurate predictions, but also to evaluate whether the model is fair—for example, whether its precision is consistent across experienced and inexperienced players.


# Data Cleaning and EDA
### Univariate Analysis
<iframe
  src="assets/gamesplayed.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>
Visualizing the frequency of games played by LoL players shows how professional experience is distributed among the participants. The skewedness of experience is something to take into account when predicting what can contribute to winning a match, especially without using much in-game data.

### Bivariate Analysis
<iframe
  src="assets/quadsquad.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>
Observing the distributions of gold and kda metrics at the 15 and 25 minute marks, we see that the winning team typically has a clear advantage in both stats by the 15 minute mark, and this trend is even stronger after 25 minutes.

### Interesting Aggregates
| Position | Total Gold |
| ----------- | ----------- |
| bottom | 13648 |
| jungle | 10788 |
| mid | 12591 |
| support | 7597 |
| top | 12293 |

This table demonstrates the lesser gold-accumulating nature of jungle and support roles. The differing roles of league of legends players is something we should take into account when considering individual player performance, and gold farming will likely be best examined with respect to these roles.


# Assessment of Missingness

# Hypothesis Testing

# Framing a Prediction Problem

# Baseline Model

# Final Model 

# Fairness Analysis