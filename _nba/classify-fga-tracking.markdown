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

drop duplicates already happened in view.py so why needed again?

## Feature Creation

