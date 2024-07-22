---
layout: nba
title: Classifying FGA in Tracking Data
---

## Problem

I stumbled on some [NBA tracking data](https://github.com/linouk23/NBA-Player-Movements) recently and quickly realized that unlabelled tracking data would be significantly more difficult to work with than expected. As many people have done, I attempted to join the tracking data with play-by-play (PBP) data from the NBA. That works okay, but many of the PBP labels are a few seconds off. For example, in the Spurs' 2015-16 season opener against Oklahoma City, the [PBP log](https://www.nba.com/game/sas-vs-okc-0021500013/play-by-play?period=Q1) shows Manu Ginobili makes a jump shot with 4:05 remaining in the 1st quarter.

![PBP](https://williamscale.github.io/attachments/classify-fga-tracking/ex3.png)

However, in the screenshots below, you can see that the field goal happens earlier than that.

![FGA Start](https://williamscale.github.io/attachments/classify-fga-tracking/ex1.png)
![FGA End](https://williamscale.github.io/attachments/classify-fga-tracking/ex2.png)

This is fine for limited, small sample size analysis. The shot has been made and logged with 4:05 on the game clock. But if you wanted to extract tracking information on this field goal (or on all of the attempts in a game/season/career!), your data would consistently be off by a bit. I found that after joining the PBP data with the tracking data, I was viewing more rebounds and inbounds passes than actual field goal attempts. 

Others have mitigated this issue by using matching algorithms[^1] to align the data more accurately or taking larger time windows[^2] in their analyses. Instead, in this project, I attempt to classify field goals using unsupervised methods. Use cases may be analytics teams trying to cut film at the correct time stamps.

<!-- [^1]: https://www.statsperform.com/wp-content/uploads/2021/04/Predicting-NBA-Talent-from-Enormous-Amounts-of-College-Basketball-Tracking-Data.pdf
[^2]: https://dukespace.lib.duke.edu/server/api/core/bitstreams/ba5938d6-5455-4720-a018-4e7996e3f67d/content -->

## Data Preparation

```python
def unzip_7z(source_path, destination_path):

	with py7zr.SevenZipFile(source_path, mode = "r") as zipped_file:
	    zipped_file.extractall(destination_path)

unzip_7z(
	source_path = "./data/2016.NBA.Raw.SportVU.Game.Logs/10.28.2015.SAS.at.OKC.7z",
	destination_path = "./data/games_json/"
	)
```