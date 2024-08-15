---
layout: projects
title: NBA Passing Graphs
---

IN WORK

Code for this project can be found in my []().

## Problem

## Data

Passing data was scraped via the [nba_api package](https://pypi.org/project/nba_api/) using the [PlayerDashPtPass](https://github.com/swar/nba_api/blob/master/docs/nba_api/stats/endpoints/playerdashptpass.md) endpoint for the Spurs 2023-24 season. A snippet of the dataset is shown below.

| PLAYER_ID | Player       | Team    | Pass To           | Pass    | Assist  |
|:---------:|:-------------|:-------:|:------------------|:-------:|:-------:|
| 1631104   | Blake Wesley | SAS     | Victor Wembanyama | 114     | 13      |
| 1631104   | Blake Wesley | SAS     | Sidy Cissoko      | 17      | 3       |
| 1631104   | Blake Wesley | SAS     | Dominick Barlow   | 61      | 4       |
| &#8942;   | &#8942;      | &#8942; | &#8942;           | &#8942; | &#8942; |
| 1628380   | Zach Collins | SAS     | Devonte' Graham   | 44      | 0       |
| 1628380   | Zach Collins | SAS     | Cedi Osman        | 88      | 13      |
| 1628380   | Zach Collins | SAS     | Doug McDermott    | 59      | 9       |

## Graph Creation

To build the graph, I defined the nodes as players and the edges as passes between players. In general, each pair of nodes (players) has two edges connecting them. One edge represents passes from Player A to Player B and the other represents passes from Player B to Player A. For example, Devin Vassell threw 413 passes to Jeremy Sochan and Sochan threw Vassell a pass 432 times. Each player is a single node, and there are parallel directed edges with weights of 413 and 432. In rare cases, due to little playing time, a player may have made a pass to a teammate but never received one from the same teammate, or vice versa. A histogram of the edge weights is shown below.

![Edge Weight Hist](https://williamscale.github.io/attachments/nba-passing-graph/edge_weight_hist.png)

The graph is shown below. The line thickness represents the edge weight.

![Network All](https://williamscale.github.io/attachments/nba-passing-graph/network_all.png)

It is difficult to glean any information from this network right now. More analysis may be helpful.

## Degrees

The degree of a node is the number of connections it has to other nodes. In a directed graph, the total degree for a node is the sum of the incoming degree and the outgoing degree. For example, Keldon Johnson made 2,273 passes to 14 unique players and received 2,072 passes from 15 players. His total degree is 29. Note that degree is unrelated to edge weight. Besides players who get little playing time, total degrees should be about twice the team's rostered players.

$$
\begin{aligned}
k_{\text{Keldon Johnson}} &= k_{\text{Keldon Johnson}}^{in} + k_{\text{Keldon Johnson}}^{out} \\
&= 15 + 14 \\
&= 29
\end{aligned}
$$

For the entire network, $k^{in}=k^{out}$. The average degree of the network is given by:

$$
\begin{aligned}
\langle k^{in} \rangle &= \frac{1}{N} \sum_{i=1}^{N} k_{i}^{in} \\
&= \frac{1}{20} \times 283 \\
&= 14.2
\end{aligned}
$$

The degree distributions and adjacency matrix are shown below.

![Degree Dist](https://williamscale.github.io/attachments/nba-passing-graph/deg_dist.png)
![A](https://williamscale.github.io/attachments/nba-passing-graph/adjacency.png)


