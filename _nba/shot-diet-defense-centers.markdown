---
layout: nba
title: 2023-24 Spurs Opponent FGA By Center
---

In this project, I will investigate the shot selection of the Spurs' opponent's based on the center on the court for San Antonio. The centers evaluated are
* <span style="color:#EF426F">Victor Wembanyama</span>,
* <span style="color:#00B2A9">Zach Collins</span>,
* and both <span style="color:#FF8200">Wembanyama and Collins</span> together.

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

What I'm more interested in, though, is the rim protection of these lineups as this is often the center's role on defense. The overall shot selection of the opponent's may depend on the Spurs' center, but it's only a small piece of the puzzle. Gameplans, the other Spur defenders, and opponent's offensive players on the court probably contribute more to the shot selection. Therefore, I filtered out FGA further than 10 feet from the rim. This distance was chosen arbitrarily.

![FGA Hex At Rim](https://williamscale.github.io/attachments/shot-diet-defense-centers/fga_at_rim.png)
