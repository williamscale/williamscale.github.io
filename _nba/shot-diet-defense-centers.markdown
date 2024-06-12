---
layout: nba
title: 2023-24 Spurs Opponent FGA By Center
---

In this project, I will investigate the shot selection of the Spurs' opponent's based on the center on the court for San Antonio. The centers evaluated are
* <span style="color:#EF426F">Victor Wembanyama</span> (x mins),
* <span style="color:#00B2A9">Zach Collins</span> (x mins),
* and both <span style="color:#FF8200">Wembanyama and Collins</span> together (x mins).

Shotcharts for each lineup are below. Note these include both missed and made field goals.

![FGA](https://williamscale.github.io/attachments/shot-diet-defense-centers/fga.png)
![FGA Hex](https://williamscale.github.io/attachments/shot-diet-defense-centers/fga_log.png)
![FGA Density](https://williamscale.github.io/attachments/shot-diet-defense-centers/fga_dens.png)

The hexagonal heatmap is shown on a log scale so that shots at the rim, which are overwhelmingly more frequent, do not saturate the plot. It is still not evident if the defending Spurs center corresponds to a different shot diet for their opponents though. Next, I calculated the attempt rate by zone (\frac{\text{zone FGA}}{\text{FGA}}) for each center lineup. Below is that plot filled with the respective center with the highest attempt rate. 

![FGA Rate Leading C](https://williamscale.github.io/attachments/shot-diet-defense-centers/attempt_rate_leadingC.png)

For example, the highlighted cell below is:

\begin{table}[]
\begin{tabular}{llll}
C                            & Zone FGA & Total FGA & Opponent FGA Rate \\ \cline{2-4} 
\multicolumn{1}{l|}{VW}      & 81       & 3,249     & 0.025             \\
\multicolumn{1}{l|}{ZC}      & 39       & 2,119     & 0.018             \\
\multicolumn{1}{l|}{VW + ZC} & 14       & 839       & 0.017            
\end{tabular}
\end{table}

![FGA Rate Leading C Ex](https://williamscale.github.io/attachments/shot-diet-defense-centers/attempt_rate_leadingC.png)
