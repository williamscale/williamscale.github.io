---
layout: nba
title: 2023-24 Spurs Center Rim Protection
---

In this project, I will investigate the rim protection of <span style="color:#EF426F">Victor Wembanyama</span> and <span style="color:#00B2A9">Zach Collins</span>. Much of my approach is derived from Seth Partnow's thought process in this [interview](https://www.nytimes.com/athletic/1870696/2020/06/15/evaluation-orlando-magic-rim-protection/). Like Seth, I split up rim protection into *rim deterrence* and *rim defense*. Additionally, I explored defensive rebounding as another aspect of rim protection. 

Essentially, I've broked rim protection up into 3 steps:

1. limit rim fga (*rim deterrence*)
2. force missed fga near the rim (*rim defense*)
3. gain possession of missed fga near the rim (*defensive rebounding*)

## Data

All data comes from [NBA Stats](https://www.nba.com/stats/) and is scraped via the [hoopR package](https://hoopr.sportsdataverse.org/).

Player stats were limited to players in the 2023-24 NBA season who
* played in at least 30 games,
* averaged at least 10 minutes per game, and
* are 6'10" or taller.

This resulted in 72 players.

Field goals included are those within 6 feet of the rim. This distance was selected to align with NBA Stats distance bins.

## Rim Deterrence

For rim deterrence, I'm ignoring the outcome of each FGA. That will be covered in the rim defense section.

First, I scraped all field goal attempts by Spurs opponents with their outcome, timestamps, and coordinates. I then scraped the Spurs rotations and filtered the dataset to include only field goal attempts with one of, but not both, Wembanyama or Collins on the court. Finally, I calculated the distance of the shot and removed all attempts further than 6 feet from the rim. This value was chosen in order to coincide with the NBA's stats defense dashboard demarcations. I'll refer to the remaining FGA as "rim FGA" since they are near the basket. These shots are not necessarily defended by Wembanyama or Collins, they just occur with one of them on the court.

| Player            | Opponent FGA | Opponent Rim FGA | Opponent Rim Rate |
|:------------------|:------------:|:----------------:|:-----------------:|
| Victor Wembanyama | 4,088        | 1,203            | 0.370             |
| Zach Collins      | 2,958        | 748              | 0.353             |

Opponents take slightly fewer shots at the rim (about 1.5 a game) with Collins than Wembanyama. I did not expect this. Of course, other personnel matters and it's just a small piece of the puzzle. Incorporating contested rim FGA yields:

| Player            | Opponent Rim FGA | Defended Rim FGA | Rim Contest Rate |
|:------------------|:----------------:|:----------------:|:----------------:|
| Victor Wembanyama | 1,203            | 586              | 0.487            |
| Zach Collins      | 748              | 424              | 0.567            |

## Rim Defense

also do a fg% vs distance from basket average (will require scraping all shots I think) then vw and zc averages. so line graph with 3 lines, xaxis is distance, y axis is fg%.


Field goal efficiency is used as the measure of rim defense in this section. Below is a snippet of the [data queried](https://www.nba.com/stats/players/defense-dash-lt6?SeasonType=Regular+Season&PerMode=Totals), sorted by defended FGA descending.

| Player             | GP      | MP      | DFGM    | DFGA &#9660;  | DFG%    |
|:-------------------|:-------:|:-------:|:-------:|:-------------:|:-------:|
| Brook Lopez        | 79      | 2,411   | 375     | 673           | 0.557   |
| Chet Holmgren      | 82      | 2,413   | 352     | 666           | 0.529   |
| Nikola Jokic       | 79      | 2,737   | 425     | 657           | 0.647   |
| &#8942;            | &#8942; | &#8942; | &#8942; | &#8942;       | &#8942; |
| Victor Wembanyama  | 71      | 2,106   | 314     | 586           | 0.536   |
| &#8942;            | &#8942; | &#8942; | &#8942; | &#8942;       | &#8942; |
| Zach Collins       | 69      | 1,526   | 253     | 424           | 0.597   |
| &#8942;            | &#8942; | &#8942; | &#8942; | &#8942;       | &#8942; |
| Neemias Queta      | 28      | 333     | 39      | 73            | 0.534   |
| DeAndre Jordan     | 36      | 396     | 43      | 67            | 0.642   |
| Ibou Badji         | 22      | 226     | 35      | 56            | 0.625   |


![Rim Defense Scatter](https://williamscale.github.io/attachments/shot-diet-defense-centers/rim_defense_scatter1.png)

Collins is in the middle of the pack with both defended FGA and defended FG%, while Wembanyama has a lower FG% at a higher volume of attempts. Normalized by minutes played, however, Collins and Wembanyama have identical defended FGA rates.

![Rim Defense Scatter Norm](https://williamscale.github.io/attachments/shot-diet-defense-centers/rim_defense_scatter2.png)

Including blocks into the analysis is a bit tougher because I don't know of any publicly available data that shows where a block happened on the court. Wembanyama probably blocks more jump shots than the other players in this dataset, so assuming all blocks are at the rim would be overestimating his rim block rate. However, until more granular data is available, this is what I have to work with. Therefore, I calculate a block rate as BLK% = BLK / DFGA

| Rank    | Player            | BLK     | DFGA    | BLK% &#9660; |
|:-------:|:------------------|:-------:|:-------:|:------------:|
| 1       | Jonathan Isaac    | 70      | 154     | 0.455        |
| 2       | Victor Wembanyama | 254     | 586     | 0.433        |
| 3       | Walker Kessler    | 154     | 402     | 0.383        |
| &#8942; | &#8942;           | &#8942; | &#8942; | &#8942;      |
| 53      | Zach Collins      | 52      | 424     | 0.123        |
| &#8942; | &#8942;           | &#8942; | &#8942; | &#8942;      |
| 70      | Lauri Markkanen   | 26      | 341     | 0.076        |
| 71      | Danilo Gallinari  | 6       | 122     | 0.049        |
| 72      | Dario Saric       | 10      | 289     | 0.035        |

![Block Rate Scatter](https://williamscale.github.io/attachments/shot-diet-defense-centers/rim_defense_scatter3.png)

## Rim Rebounding

Finally, to finish the defensive possession, the team must get a defensive rebound. If a player leaves his feet for a block attempt and misses, he may have altered the shot enough to force a miss, but be more susceptible to giving up an easy offensive rebound and putback. Thus, I think defensive rebounding should be a factor in rim protection evaluation. The NBA has stats on [contested vs. uncontested rebounds](https://www.nba.com/stats/help/glossary#contested_dreb). It defines a contested rebound as one in which an opponent is within 3.5 feet of the rebounder. An assumption I'm making in this section is that each missed FGA near the rim results in an available contested rebound. 

Then I calculate a contested defensive rebound rate as:

$$
C_DREB.Rate = C_DREB / (DFGA - DFGM)
$$

| Rank    | Player            | Contested DREB | Contested DREB% &#9660; | 
|:-------:|:------------------|:--------------:|:-----------------------:|
| 1       | Omer Yurtseven    | 13             | 0.342                   |
| 2       | Nikola Jokic      | 78             | 0.336                   |
| 3       | Andre Drummond    | 32             | 0.330                   |
| &#8942; | &#8942;           | &#8942;        | &#8942;                 |
| 15      | Victor Wembanyama | 63             | 0.232                   |
| &#8942; | &#8942;           | &#8942;        | &#8942;                 |
| 38      | Zach Collins      | 29             | 0.170                   |
| &#8942; | &#8942;           | &#8942;        | &#8942;                 |
| 70      | Chimezie Metu     | 4              | 0.071                   |
| 71      | Ibou Badji        | 1              | 0.048                   |
| 72      | Davis Bertans     | 0              | 0.000                   |

I then combined **rim defense** and **rim rebounding** to create a success rate of rim FGA. In other words, of the shots attempted at the rim, the success rate is the rate at which the attempted shot is both missed and rebounded by the player. This is not a perfect approach. How often does a player both contest a shot and get the rebound?

$$
DFGA Success Rate = (DFGA - DFGM + CDREB) / DFGA
$$

| Rank    | Player             | DFGA    | DFGM    | CDREB   | DFGA Success Rate &#9660; | 
|:-------:|:-------------------|:-------:|:-------:|:-------:|:-------------------------:|
| 1       | Bol Bol            | 76      | 34      | 7       | 0.645                     |
| 2       | Jonathan Isaac     | 154     | 73      | 9       | 0.584                     |
| 3       | Ivica Zubac        | 448     | 232     | 43      | 0.578                     |
| &#8942; | &#8942;            | &#8942; | &#8942; | &#8942; | &#8942;                   |
| 5       | Victor Wembanyama  | 586     | 314     | 63      | 0.572                     |
| &#8942; | &#8942;            | &#8942; | &#8942; | &#8942; | &#8942;                   |
| 45      | Zach Collins       | 424     | 253     | 29      | 0.472                     |
| &#8942; | &#8942;            | &#8942; | &#8942; | &#8942; | &#8942;                   |
| 70      | Davis Bertans      | 95      | 60      | 0       | 0.368                     |
| 71      | Mitchell Robinson  | 148     | 104     | 10      | 0.365                     |
| 72      | Aleksej Pokusevski | 81      | 54      | 2       | 0.358                     |

<!-- First, I scraped all field goal attempts by Spurs opponents with their outcome, timestamps, and coordinates. I then scraped the Spurs rotations and filtered the dataset to include only field goal attempts with one of, but not both, Wembanyama or Collins on the court. Finally, I calculated the distance of the shot and removed all attempts further than 6 feet from the rim. This value was chosen in order to coincide with the NBA's stats defense dashboard demarcations. I'll refer to the remaining FGA as "rim FGA" since they are near the basket.

As expected, the distribution of FGA is bimodal, with most shots being taken at the rim or the 3-point line.

![Density](https://williamscale.github.io/attachments/shot-diet-defense-centers/dens_distance.png)
![Density Rim](https://williamscale.github.io/attachments/shot-diet-defense-centers/dens_distance_rim.png)

## Rim Deterrence

For rim deterrence, I'm ignoring the outcome of each FGA. That will be covered in the rim defense section.

Below is the summary of scraped FGA. I think these numbers do not match the [NBA's tracking data](https://www.nba.com/stats/player/1641705/defense-dash?PerMode=Totals&SeasonType=Regular%20Season&dir=D&sort=DEFENSE_CATEGORY) because these are FGA with the player on the court, not necessarily if they are assigned as the defending player. For example, Wembanyama could be actively guarding Brook Lopez at the wing 3 while Khris Middleton gets a layup over Julian Champagnie. This would be captured in my dataset since Wembanyama is on the court, but would only be captured in the NBA dataset under Champagnie's defensive tracking data.

| Center On Court | Opp Total FGA | Opp Rim FGA | Opp Rim Rate | Contested Opp Rim FGA | Rim Contest Rate |
|-----------------|---------------|-------------|--------------| ----------------------| -----------------|
| Wembanyama      | 3,249         | 1,203       | 0.370        | [586](https://www.nba.com/stats/player/1641705/defense-dash?PerMode=Totals&SeasonType=Regular%20Season&dir=D&sort=DEFENSE_CATEGORY) | 0.487 |
| Collins         | 2,119         | 748         | 0.353        | [424](https://www.nba.com/stats/player/1628380/defense-dash?PerMode=Totals&SeasonType=Regular%20Season&dir=D&sort=DEFENSE_CATEGORY) | 0.567 |


With Collins on the court, opponents shoot slightly less frequently at the rim and of the shots they do take at the rim, he is contesting them more often than Wembanyama does. This is not what I expected. It seems like Wembanyama contests everything, but perhaps teams are more deliberate about luring him away from the basket before taking rim attempts and they don't bother taking the shot if he is in position to contest it.

## Rim Defense



Shotcharts for each lineup are below. Note these include both missed and made field goals.

![FGA](https://williamscale.github.io/attachments/shot-diet-defense-centers/fga.png)
![FGA Hex](https://williamscale.github.io/attachments/shot-diet-defense-centers/fga_log.png)
![FGA Density](https://williamscale.github.io/attachments/shot-diet-defense-centers/fga_dens.png)

The hexagonal heatmap is shown on a log scale so that shots at the rim, which are overwhelmingly more frequent, do not saturate the plot. It is still not evident if the defending Spurs center corresponds to a different shot diet for their opponents though. To account for the fact that the three situations evaluated had unequal playing times/possessions/defended FGA, next, I calculated the attempt rate by zone ($\frac{\text{zone FGA}}{\text{FGA}}$). Below is that plot filled with the respective center with the highest attempt rate. 

![FGA Rate Leading C](https://williamscale.github.io/attachments/shot-diet-defense-centers/attempt_rate_leadingC.png)

For example, the highlighted cell below is calculated using the following values:

| C                    | Zone FGA | Total FGA | Opponent FGA Rate |
|----------------------|----------|-----------|-------------------|
| Wembanyama           | 81       | 3,249     | 0.025             |
| Collins              | 39       | 2,119     | 0.018             |
| Wembanyama + Collins | 14       | 839       | 0.017             |

![FGA Rate Leading C Ex](https://williamscale.github.io/attachments/shot-diet-defense-centers/attempt_rate_leadingC_ex.png)

What I'm more interested in, though, is the rim protection of these lineups as this is often the center's role on defense. The overall shot selection of the opponent's may depend on the Spurs' center, but it's only a small piece of the puzzle. Gameplans, the other Spur defenders, and opponent's offensive players on the court probably contribute more to the shot selection. Seth Partnow ([@SethPartnow](https://x.com/SethPartnow)) has done a lot of writing on rim protection and its intricacies and has split up rim protection into two parts: *shot protection* and *shot deterrence*. I'm going to use these categories as well. Therefore, I filtered out FGA further than 10 feet from the rim. This distance was chosen arbitrarily.

Firstly, shot deterrence. 

![FGA Hex At Rim](https://williamscale.github.io/attachments/shot-diet-defense-centers/fga_at_rim.png) -->
