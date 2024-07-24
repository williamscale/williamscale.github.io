---
layout: nba
title: Classifying FGA in Tracking Data
---

## Problem

I stumbled on some [NBA tracking data](https://github.com/linouk23/NBA-Player-Movements) recently and quickly realized that unlabelled tracking data would be significantly more difficult to work with than expected. As many people have done, I attempted to join the tracking data with play-by-play (PBP) data from the NBA. That works okay, but many of the PBP labels are a few seconds off. For example, in the Spurs' 2015-16 season opener against Oklahoma City, the [PBP log](https://www.nba.com/game/sas-vs-okc-0021500013/play-by-play?period=Q1) shows Manu Ginobili makes a jump shot with 4:05 remaining in the 1st quarter.

![PBP](https://williamscale.github.io/attachments/classify-fga-tracking/ex3.PNG)

However, in the screenshots below, you can see that the field goal attempt (FGA) happens earlier than that.

![FGA Start](https://williamscale.github.io/attachments/classify-fga-tracking/ex1.PNG)
![FGA End](https://williamscale.github.io/attachments/classify-fga-tracking/ex2.PNG)

This is fine for limited, small sample size analysis. The shot has been made and logged with 4:05 on the game clock. But if you wanted to extract tracking information on this field goal (or on all of the attempts in a game/season/career!), your data would consistently be off by a bit. I found that after joining the PBP data with the tracking data, I was viewing more rebounds and inbounds passes than actual field goal attempts. 

Others have mitigated this issue by using matching algorithms to align the data more accurately or taking larger time windows in their analyses. Instead, in this project, I attempt to classify field goals using unsupervised methods.

## Data Preparation

Sorting through the data took some time, but essentially, it contains ball and player locations measured at 25 Hz and separated into "events". These events have IDs that align with the NBA's PBP data (hence the common joining approach). PBP data can be scraped via the [nba_api package](https://pypi.org/project/nba_api/). Tracking data corresponding to a single event ID sometimes includes field goal attempts by both teams. Additionally, tracking data includes information when the game clock isn't moving, like free throws or inbounds passes. If the referees add time back on the clock, timestamps are repeated with different spatial data.

I combined all the tracking data that was separated by event into a single dataframe and then removed duplicated timestamp rows. Below is a snippet of the dataset.

| Quarter | Game Clock | Ball Position [X, Y, Z] | Player 1 Position [X, Y] | &#8230; | Player 10 Position [X, Y] |
|:-:|:-:|:-:|:-:|:-:|:-:|
| 1 | 720.00 | [46.454, 27.131, 6.186] | [48.731, 32.787] | &#8230; | [45.830, 32.779] |
| 1 | 719.98 | [46.669, 25.544, 5.205] | [48.802, 32.764] | &#8230; | [45.896, 32.732] |
| 1 | 719.94 | [46.549, 24.963, 6.732] | [48.731, 32.787] | &#8230; | [45.955, 32.666] |
| &#8942; | &#8942; | &#8942; | &#8942; | &#8942; | &#8942; |
| 4 | 0.10 | [45.589, 25.933, 4.887] | [37.329, 7.833] | &#8230; | [39.873, 5.071] |
| 4 | 0.06 | [45.462, 26.362, 5.141] | [37.191, 7.803] | &#8230; | [39.635, 5.071] |
| 4 | 0.02 | [44.893, 25.914, 4.796] | [37.061, 7.769] | &#8230; | [39.394, 5.078] |


Here is how this data would look on the court.

![Court Scatter](https://williamscale.github.io/attachments/classify-fga-tracking/ex4.PNG)

## Feature Creation

