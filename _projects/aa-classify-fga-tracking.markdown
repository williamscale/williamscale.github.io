---
layout: projects
title: Classifying FGA Within Tracking Data
---

Code for this project can be found in my [forked repo](https://github.com/williamscale/NBA-Player-Movements).

## Problem

In this project, I will use the Spurs vs. Thunder game on October 28, 2015 for examples and training the model.

I stumbled on some [NBA tracking data](https://github.com/linouk23/NBA-Player-Movements) recently and quickly realized that unlabelled tracking data would be significantly more difficult to work with than expected. As many people have done, I attempted to join the tracking data with play-by-play (PBP) data from the NBA. That works okay, but many of the PBP labels are a few seconds off. For example, in the Spurs' 2015-16 season opener against Oklahoma City, the [PBP log](https://www.nba.com/game/sas-vs-okc-0021500013/play-by-play?period=Q1) shows Manu Ginobili makes a jump shot with 4:05 remaining in the 1st quarter.

![PBP](https://williamscale.github.io/attachments/classify-fga-tracking/ex3.PNG)

However, in the screenshots below, you can see that the field goal attempt (FGA) happens earlier than that.

![FGA Start](https://williamscale.github.io/attachments/classify-fga-tracking/ex1.PNG)
![FGA End](https://williamscale.github.io/attachments/classify-fga-tracking/ex2.PNG)

This is fine for limited, small sample size analysis. The shot has been made and logged with 4:05 on the game clock. But if you wanted to extract tracking information on this field goal (or on all of the attempts in a game/season/career!), your data would consistently be off by a bit. I found that after joining the PBP data with the tracking data, I was viewing more rebounds and inbounds passes than actual field goal attempts. 

Others have mitigated this issue by using matching algorithms to align the data more accurately or taking larger time windows in their analyses. Instead, in this project, I attempt to classify field goals using unsupervised methods.

## Data Preparation

Sorting through the data took some time, but essentially, it contains ball and player locations measured at 25 Hz and separated into "events". These events have IDs that align with the NBA's PBP data (hence the common joining approach). PBP data can be scraped via the [nba_api package](https://pypi.org/project/nba_api/). Tracking data corresponding to a single event ID sometimes includes field goal attempts by both teams. Additionally, tracking data includes information when the game clock isn't moving, like free throws or inbounds passes. If the referees add time back on the clock, timestamps are repeated with different spatial data.

I combined all the tracking data that was separated by event into a single dataframe and then removed duplicated timestamp rows. Below is a snippet of the dataset. Note that units are seconds and feet.

| Quarter | Game Clock | Ball Position [X, Y, Z] | Player 1 Position [X, Y] | &#8230; | Player 10 Position [X, Y] |
|:-:|:-:|:-:|:-:|:-:|:-:|
| 1 | 720.00 | [46.454, 27.131, 6.186] | [48.731, 32.787] | &#8230; | [45.830, 32.779] |
| 1 | 719.98 | [46.669, 25.544, 5.205] | [48.802, 32.764] | &#8230; | [45.896, 32.732] |
| 1 | 719.94 | [46.549, 24.963, 6.732] | [48.731, 32.787] | &#8230; | [45.955, 32.666] |
| &#8942; | &#8942; | &#8942; | &#8942; | &#8942; | &#8942; |
| 4 | 0.10 | [45.589, 25.933, 4.887] | [37.329, 7.833] | &#8230; | [39.873, 5.071] |
| 4 | 0.06 | [45.462, 26.362, 5.141] | [37.191, 7.803] | &#8230; | [39.635, 5.071] |
| 4 | 0.02 | [44.893, 25.914, 4.796] | [37.061, 7.769] | &#8230; | [39.394, 5.078] |

Here is the data superimposed on the court.

![Court Scatter](https://williamscale.github.io/attachments/classify-fga-tracking/ex4.PNG)

Although this data isn't labelled, it is evident the Thunder have possession and the Spurs are in standard halfcourt defensive positioning. Here is a similar scatter plot:

![Court Scatter 2](https://williamscale.github.io/attachments/classify-fga-tracking/ex5.PNG)

It appears the Spurs have gained possession, but actually this is in the middle of a [Kevin Durant jumper](https://www.nba.com/stats/events?CFID=&CFPARAMS=&GameEventID=43&GameID=0021500013&Season=2015-16&flag=1&title=Durant%2015%27%20Jump%20Shot%20(4%20PTS)%20(Westbrook%202%20AST)) and the ball is going over the head of LaMarcus Aldridge. Ball Z position could inform of field goal attempts better than solely X or Y positions and is already available from the tracking data. A few more variables may be helpful too. 

## Feature Creation

### Ball Speed

Firstly, I created a distance travelled variable using Euclidean distance. Dividing this distance by the time between measurements then gives ball speed. Following is an example calculation.

| Quarter | Game Clock | X       | Y       | Z       |
|:-------:|:----------:|:-------:|:-------:|:-------:|
| &#8942; | &#8942;    | &#8942; | &#8942; | &#8942; |
| 4       | 43.97      | 29.638  | 29.903  | 2.096   |
| 4       | 43.93      | 29.434  | 30.199  | 1.994   |
| &#8942; | &#8942;    | &#8942; | &#8942; | &#8942; |

$$
\begin{aligned}
\Delta t &= t_{2} - t_{1} \\
&= 43.97 - 43.93 \\
&= 0.04 \text{ seconds}
\end{aligned}
\qquad
\begin{aligned}
\Delta d &= \sqrt{\left( x_{2} - x_{1} \right)^{2} + \left( y_{2} - y_{1} \right)^{2} + \left( z_{2} - z_{1} \right)^{2}} \\
&= \sqrt{\left( 29.434 - 29.638 \right)^{2} + \left( 30.199 - 29.903 \right)^{2} + \left( 1.994 - 2.096 \right)^{2}} \\
&= 0.374 \text{ feet}
\end{aligned}
$$

$$
\begin{aligned}
v &= \frac{\Delta d}{\Delta t} \\
&= \frac{0.374}{0.04} \\
&= 9.342 \frac{\text{ft}}{\text{s}}
\end{aligned}
$$

There were some outliers, probably due to missing data, so rows with ball speeds $> 50 \frac{ft}{s}$ were removed from the dataset. This value was chosen arbitrarily and perhaps should be scrutinized more. The distribution is shown below.

![Ball Speed Hist](https://williamscale.github.io/attachments/classify-fga-tracking/ballspeed_hist1.png)

### Ball Trajectory

Next, I calculated the ball's trajectory relative to the direction of the closest basket as:

$$
\theta = \arccos \left( \frac{a \cdot b}{|a| \left| b \right| } \right)
$$

where $a$ is the vector from the ball's current position to the basket and $b$ is the vector between the ball and its next position.

![Ball Traj](https://williamscale.github.io/attachments/classify-fga-tracking/traj1.png)

Any ball movement towards the basket, whether it be shots, passes, or dribbles, has a low $\theta$.

## Classification

I know there were 179 attempted field goals in the game and roughly when they occured via PBP data. I'm ignoring the PBP data though and treating this as an unlabelled dataset and, thus, using unsupervised methods to label the data. Additionally, the tracking data may include information that isn't a FGA in the box score, but looks like one (like a free throw or a missed jump shot in which the shooter drew a foul). I built a k-means classification model with the following variables:

- ball z position,
- ball speed,
- and ball trajectory.

All metrics were standardized to have a unit variance. After testing multiple values of $k$, I moved forward with $k=4$, as the elbow plot showed diminishing returns with more clusters.

![Elbow](https://williamscale.github.io/attachments/classify-fga-tracking/elbow.png)

The ball locations by cluster are shown below.

![Cluster0](https://williamscale.github.io/attachments/classify-fga-tracking/cluster_0.png)
![Cluster1](https://williamscale.github.io/attachments/classify-fga-tracking/cluster_1.png)
![Cluster2](https://williamscale.github.io/attachments/classify-fga-tracking/cluster_2.png)
![Cluster3](https://williamscale.github.io/attachments/classify-fga-tracking/cluster_3.png)

It is evident that the model does a fairly good job of identifying field goal attempts. Cluster 1 is obviously made up of many shots, in addition to some non-shot datapoints. Further inspection is necessary.

Metrics by cluster are shown below.

![Box Ball Speed](https://williamscale.github.io/attachments/classify-fga-tracking/box_ball_speed.png)
![Box Ball Z](https://williamscale.github.io/attachments/classify-fga-tracking/box_ball_z.png)
![Box FGA Theta](https://williamscale.github.io/attachments/classify-fga-tracking/box_fga_theta.png)

Instead of simply classifying all cluster 1 points as attempted field goals, I then filtered them further by taking only streaks of 15 consecutive points labelled as cluster 1. The streak length was chosen arbitrarily and could be tuned further. Then, I assigned a unique identifier to each streak and extracted the start and end times of each classified FGA. A snippet of the dataset is shown below.

| FGA ID  | Game Clock Start | Game Clock End |
|:-------:|:----------------:|:--------------:|
| 0       | 717.38           | 716.53         |
| 1       | 693.62           | 692.56         |
| 2       | 513.50           | 512.58         |
| &#8942; | &#8942;          | &#8942;        |
| 167     | 25.50            | 23.41          |
| 168     | 15.51            | 13.71          |
| 169     | 8.01             | 5.94           |

I've randomly selected five FGA IDs (time periods classified as FGAs) to evaluate. 

### FGA ID 71: Q1 5:43

![ID 71](https://williamscale.github.io/attachments/classify-fga-tracking/fga_71.png)

[Durant 18' Pullup Jump Shot](https://www.nba.com/stats/events?CFID=&CFPARAMS=&GameEventID=71&GameID=0021500013&Season=2015-16&flag=1&title=Durant%2018%27%20Pullup%20Jump%20Shot%20(6%20PTS))

Result: Successful classification

### FGA ID 35: Q3 5:25

![ID 35](https://williamscale.github.io/attachments/classify-fga-tracking/fga_35.png)

Nothing is listed in the PBP data and this looks more like a post entry pass than a FGA.

Result: Unsuccessful classification

### FGA ID 128: Q3 1:32

![ID 128](https://williamscale.github.io/attachments/classify-fga-tracking/fga_128.png)

[Adams 9' Jump Shot](https://www.nba.com/stats/events?CFID=&CFPARAMS=&GameEventID=370&GameID=0021500013&Season=2015-16&flag=1&title=Adams%209%27%20Jump%20Shot%20(4%20PTS)%20(Westbrook%208%20AST))

Result: Successful classification

### FGA ID 47: Q4 8:47

![ID 47](https://williamscale.github.io/attachments/classify-fga-tracking/fga_47.png)

[Adams Alley Oop Dunk](https://www.nba.com/stats/events?CFID=&CFPARAMS=&GameEventID=435&GameID=0021500013&Season=2015-16&flag=1&title=Adams%20%20Alley%20Oop%20Dunk%20(6%20PTS)%20(Augustin%204%20AST))

The alley-oop pass is also classified as a FGA, which makes sense given the model inputs.

Result: Successful classification

### FGA ID 168: Q4 0:15

![ID 168](https://williamscale.github.io/attachments/classify-fga-tracking/fga_168.png)

[MISS Green 24' 3PT Jump Shot Adams BLOCK](https://www.nba.com/stats/events?CFID=&CFPARAMS=&GameEventID=535&GameID=0021500013&Season=2015-16&flag=1&title=MISS%20Green%2024%27%203PT%20Jump%20Shot)

Successful classification even though it was blocked, but I doubt it would be as good if the block changed the trajectory more significantly.

Result: Successful classification

## Conclusion

Future work will include building a confusion matrix manually, by checking all classified FGAs against actual FGAs in a game. More predicting variables could be created as well. Does the ball hit the backboard? This could be a strong correlator of a FGA. Additionally, I could use multiple games worth of data to build the clustering model.