---
layout: nba
title: 2023-24 Spurs Center Rim Protection
---

In this project, I will investigate the rim protection of <span style="color:#EF426F">Victor Wembanyama</span> and <span style="color:#00B2A9">Zach Collins</span>. Much of my approach is derived from Seth Partnow's thought process in this [interview](https://www.nytimes.com/athletic/1870696/2020/06/15/evaluation-orlando-magic-rim-protection/).

Essentially, I've broken rim protection into 3 steps:

1. Limit rim FGA (**rim deterrence**)
2. Force missed rim FGA (**rim defense**)
3. Gain possession of missed rim FGA (**defensive rebounding**)

## Data

All data comes from [NBA Stats](https://www.nba.com/stats/) and is scraped via the [hoopR package](https://hoopr.sportsdataverse.org/).

Player stats were limited to players in the 2023-24 NBA season who
* played in at least 30 games,
* averaged at least 10 minutes per game, and
* are 6'10" or taller.

This resulted in 72 players. Some of these are classic centers like Mitchell Robinson and others are bigger wings like Franz Wagner. Thus, team roles have a big impact on this analysis—Michael Porter Jr. isn't asked to do much rim protection so his metrics here may not seem favorable.

Field goals included are those within 6 feet of the rim. This distance was selected to align with NBA Stats distance bins.

## Rim Deterrence

For rim deterrence, I'm ignoring the outcome of each FGA. That will be covered in the rim defense section.

First, I scraped all field goal attempts by Spurs opponents with their outcome, timestamps, and coordinates. I then scraped the Spurs rotations and filtered the dataset to include only field goal attempts with at least one of Wembanyama and Collins on the court. Finally, I calculated the distance of the shot and removed all attempts further than 6 feet from the rim. I'll refer to these FGA as "rim FGA" since they are near the basket. These shots are not necessarily defended by Wembanyama or Collins, they just occur with one of them on the court.

| Player            | Opponent FGA | Opponent Rim FGA | Opponent Rim Rate |
|:------------------|:------------:|:----------------:|:-----------------:|
| Victor Wembanyama | 4,088        | 1,203            | 0.370             |
| Zach Collins      | 2,958        | 748              | 0.353             |
| Both              | 839          | 297              | 0.354             |

Below is the rate by distance for Wembanyama, Collins, and the league average. Wembanyama has higher rates than Collins and league average until 5 feet out.

![Distance Proportion Rim](https://williamscale.github.io/attachments/shot-diet-defense-centers/dist_prop_rim.png)

Opponents take slightly fewer shots at the rim with Collins than Wembanyama. I did not expect this. Of course, other personnel matters and it's just a small piece of the puzzle. I would be interested in seeing the same data with other centers around the league to see if there are any outliers. Incorporating perimeter defenders into the equation may also reveal interesting results. Perhaps centers with lower opponent rim rates are playing alongside better defenders with lower blow-by rates, screen navigation, etc. Gameplan is also a factor here. The Spurs may be intentionally funneling ballhandlers into Wembanyama near the rim.

## Rim Defense

I don't have contested FGA data by lineup so to incorporate it, I've included possessions in which both Wembanyama and Collins are on the floor together. Rim attempts are contested at about the same rate, but again Collins has a slight edge.

| Player            | Opponent Rim FGA    | Defended Rim FGA | Rim Contest Rate |
|:------------------|:-------------------:|:----------------:|:----------------:|
| Victor Wembanyama | 1,203 + 297 = 1,500 | 586              | 0.391            |
| Zach Collins      | 748 + 297 = 1,045   | 424              | 0.406            |

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

Below is the opponent efficiency by distance. So although more rim attempts occur with Wembanyama on the court, they are made at a lower rate.

![Rim Defense Scatter Norm](https://williamscale.github.io/attachments/shot-diet-defense-centers/dist_eff.png)

Including blocks into the analysis is a bit tougher because I don't know of any publicly available data that shows where a block happened on the court. Wembanyama probably blocks more jump shots than the other players in this dataset, so assuming all blocks are at the rim would be overestimating his rim block rate. However, until more granular data is available, this is what I have to work with. Therefore, I calculate a block rate as BLK% = BLK / DFGA.

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

Wembanyama is an outlier with that block rate at such a high volume.

## Rim Rebounding

Finally, to finish the defensive possession, the team must get a defensive rebound. If a player leaves his feet for a block attempt and misses, he may have altered the shot enough to force a miss, but be more susceptible to giving up an easy offensive rebound and putback. Thus, I think defensive rebounding included when evaluating rim protection.

I've scraped defensive rebounding data on rim FGA and calculated a defensive rim rebound rate as:

$$
DREB.Rate = Rim DREB / Available Rim DREB = Rim DREB / (DFGA - DFGM)
$$

| Rank    | Player             | DFGA    | DFGM    | Rim DREB | Rim DREB% &#9660; | 
|:-------:|:-------------------|:-------:|:-------:|:--------:|:-----------------:|
| 1       | Karl-Anthony Towns | 263     | 166     | 87       | 0.897             |
| 2       | Nikola Jokic       | 657     | 425     | 200      | 0.862             |
| 3       | Domantas Sabonis   | 621     | 364     | 219      | 0.852             |
| &#8942; | &#8942;            | &#8942; | &#8942; | &#8942;  | &#8942;           |
| 18      | Victor Wembanyama  | 586     | 314     | 159      | 0.585             |
| &#8942; | &#8942;            | &#8942; | &#8942; | &#8942;  | &#8942;           |
| 59      | Zach Collins       | 424     | 253     | 62       | 0.363             |
| &#8942; | &#8942;            | &#8942; | &#8942; | &#8942;  | &#8942;           |
| 70      | Ibou Badji         | 56      | 35      | 5        | 0.238             |
| 71      | Jonathan Isaac     | 154     | 73      | 18       | 0.222             |
| 72      | Davis Bertans      | 95      | 60      | 7        | 0.200             |

![DRB Rate Scatter](https://williamscale.github.io/attachments/shot-diet-defense-centers/rebounding_scatter.png)

The top three players on this list are not thought of as rim protectors. In the proposed equation, DFG% and DREB% are directly correlated. I am curious to know how many of Towns's rim DREB come after shots defended by Rudy Gobert. Regardless, obtaining a rebound after a rim attempt should be valued and taken into account.

