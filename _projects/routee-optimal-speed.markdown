---
layout: projects
title: Minimizing Fuel Usage By Adjusting Vehicle Speed
---

IN WORK

In this project, I use NREL's RouteE tool to determine the optimal driving speed on a given route to save on fuel. The RouteE tool takes vehicle make & model, route distance, and road grade as inputs and produces a fuel usage estimate. RouteE documentation can be found [here](https://nrel.github.io/routee-powertrain/intro.html).

For this exercise, I am considering a route from Denver International Airport (DIA) to Vail, CO. The distances and elevations are approximate. The speed limits are fictional. The road grades are averages, calculated as:

$$
\text{grade}_{AB} = 100 \times \frac{\text{elevation}_{B} - \text{elevation}_{A}}{\text{distance}_{AB}}
$$

| Leg | Point A       | Point B       | Distance [miles] | Elevation Gain [feet] | Grade [%] | Speed Limit [mph] | 
|:---:|:--------------|:--------------|:----------------:|:---------------------:|:---------:|:-----------------:|
| 1   | DIA           | Denver        | 20               | -154                  | -0.15     | 50                |
| 2   | Denver        | Arvada        | 5                | 245                   | 0.93      | 40                |
| 3   | Arvada        | Golden        | 8                | 259                   | 0.61      | 50                |
| 4   | Golden        | Idaho Springs | 21               | 1,742                 | 1.57      | 55                |
| 5   | Idaho Springs | Georgetown    | 22               | 995                   | 0.86      | 70                |
| 6   | Georgetown    | Silverthorne  | 22               | 269                   | 0.23      | 65                |
| 7   | Silverthorne  | Copper Mtn    | 11               | 922                   | 1.59      | 55                |
| 8   | Copper Mtn    | Vail          | 19               | -1,473                | -1.47     | 45                |

