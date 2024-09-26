---
layout: projects
title: Minimizing Fuel Usage By Adjusting Vehicle Speed
---

In this project, I use NREL's RouteE Powertrain tool to determine the optimal driving speed on a given route to save on fuel. The RouteE tool takes vehicle make & model, route distance, and road grade as inputs and produces a fuel usage estimate. RouteE documentation can be found [here](https://nrel.github.io/routee-powertrain/intro.html).

For this exercise, I am considering a route from Denver International Airport (DIA) to Vail, CO in a 2016 Toyota Camry (4 cylinder, 2WD). The distances and elevations are approximate. The speed limits are fictional. The road grades are averages, calculated as:

$$
\text{grade}_{AB} = 100 \times \frac{\text{elevation}_{B} - \text{elevation}_{A}}{\text{distance}_{AB}}
$$

| Leg | Point A       | Point B       | Distance [miles] | Elevation Gain [feet] | Grade [%] | Speed Limit [mph] | 
|:---:|:--------------|:--------------|:----------------:|:---------------------:|:---------:|:-----------------:|
| 1   | DIA           | Denver        | 20               | -154                  | -0.15     | 65                |
| 2   | Denver        | Arvada        | 5                | 245                   | 0.93      | 60                |
| 3   | Arvada        | Golden        | 8                | 259                   | 0.61      | 60                |
| 4   | Golden        | Idaho Springs | 21               | 1,742                 | 1.57      | 65                |
| 5   | Idaho Springs | Georgetown    | 22               | 995                   | 0.86      | 70                |
| 6   | Georgetown    | Silverthorne  | 22               | 269                   | 0.23      | 70                |
| 7   | Silverthorne  | Copper Mtn    | 11               | 922                   | 1.59      | 65                |
| 8   | Copper Mtn    | Vail          | 19               | -1,473                | -1.47     | 55                |

This is all the information RouteE needs to predict fuel usage. For example, assuming the above car model, leg distances & grades, and going the exact speed limit, RouteE provides the predictions below.

| Leg     | Gasoline Used [gallons] |
|:-------:|:-----------------------:|
| 1       | 0.590                   |
| 2       | 0.171                   |
| &#8942; | &#8942;                 | 
| 7       | 0.456                   |
| 8       | 0.429                   |

My goal is to minimize the gasoline used though. Perhaps the speed limit is not the optimal speed for a given trip. Using the **visualize_features()** function within the RouteE package, I am able to perform a gridsearch along a specified speed range and grade range. For this, I set the grade to be only the actual leg's grade. The speed range was set as the speed limit $\pm 10$ mph. 

Below is the output of the speed search for leg 1 of the trip. For this leg, all speeds between 55 and 66 mph have equal fuel consumption. For these cases, the speed limit was designated as the optimal speed. For this leg, the worst case would be travelling at speeds greater than 66 mph, using the maximum amount of fuel.

![Leg 1 Speed Search](https://williamscale.github.io/attachments/routee-optimal-speed/speedrange1.png)

Below is the same plot for leg 8. All speeds test have identical consumptions. Again, the speed limit is designated as optimal. 

![Leg 8 Speed Search](https://williamscale.github.io/attachments/routee-optimal-speed/speedrange8.png)

Additionally, I included an actual speed input to my function that allows comparison between energy consumption scenarios. Then, I ran the model on each speed scenario: speed limit, actual speed, optimal speed, and worst case speed. The predicted fuel usages, in gallons, are shown below.

| Leg | Point A | Point B | Gasoline (Speed Limit) | Gasoline (Optimal Speed) | Gasoline (Actual Speed) | Gasoline (Worst Case Speed) |
|:---:|:--------------|:--------------|:-----------:|:-----------:|:-----------:|:----------:|
| 1   | DIA           | Denver        | 0.590 (65)  | 0.590 (65)  | 0.654 (67)  | 0.654 (67) |
| 2   | Denver        | Arvada        | 0.171 (60)  | 0.171 (60)  | 0.171 (52)  | 0.180 (65) |
| 3   | Arvada        | Golden        | 0.262 (60)  | 0.262 (60)  | 0.262 (57)  | 0.283 (66) |
| 4   | Golden        | Idaho Springs | 0.857 (65)  | 0.784 (55)  | 0.784 (61)  | 0.928 (75) |
| 5   | Idaho Springs | Georgetown    | 0.779 (70)  | 0.720 (60)  | 0.872 (76)  | 0.896 (77) |
| 6   | Georgetown    | Silverthorne  | 0.720 (70)  | 0.649 (60)  | 0.720 (73)  | 0.798 (76) |
| 7   | Silverthorne  | Copper Mtn    | 0.456 (65)  | 0.426 (55)  | 0.427 (62)  | 0.512 (75) |
| 8   | Copper Mtn    | Vail          | 0.429 (55)  | 0.429 (55)  | 0.429 (51)  | 0.429 (45) |

An efficiency score based off fuel used normalized to the optimal and worst case ratio is calculated as:

$$
\begin{aligned}
\alpha &= 1 - \frac{\sum \text{g}_\text{actual} - \sum \text{g}_\text{optimal}}{\sum \text{g}_\text{worst} - \sum \text{g}_\text{optimal}} \\
&= 1 - \frac{4.319 - 4.031}{4.680 - 4.031} \\
&= 0.557
\end{aligned}
$$

where $g$ is the gasoline consumed. Below is the plot of cumulative fuel used over the entire trip.

## Future Work

As shown below, the same amount of fuel is used over a range of speed 55 to 62 mph. Currently, if the optimal fuel usage is at a speed less than but not equal to the speed limit, the lowest speed is designated as optimal speed. This doesn't change the fuel consumption calculations, but it increases the time of the trip. Ideally, 62 mph would be called optimal, in this example. Optimizing time after optimizing fuel usage is a good next step in the work.

![Leg 4 Speed Search](https://williamscale.github.io/attachments/routee-optimal-speed/speedrange4.png)
