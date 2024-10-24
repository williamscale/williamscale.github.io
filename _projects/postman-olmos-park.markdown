---
layout: projects
title: Generating a Postman's Optimal Route
---

Code for this project can be found in my [repo](https://github.com/williamscale/maps/blob/master/Running/postman/postman.py).

# Background

There is a neighborhood adjacent to mine that is good for running—little traffic, shady, and a mix of flat and hilly sections. I thought it would be fun to try and run every street in the neighborhood in a single run, but was unsure how big of a run that would be. I could brute force it if my legs were up to it, but knew there was a more elegant solution. The goal of this project is to generate a route that minimizes mileage while traversing every street.

# Data

A map of the neighborhood is shown below. I limited the scope to all streets bounded by McCullough Ave on the west, E Olmos Dr on the south, and E Contour Dr on the east/north. 

![OP Google Map](https://williamscale.github.io/attachments/postman-olmospark/googlemap.PNG)

I downloaded the [street data](https://data.sanantonio.gov/dataset/streets) from [Open Data SA](https://data.sanantonio.gov/) and was able to identify and plot nodes (intersections) and edges (streets) as shown below. 

![OP plt](https://williamscale.github.io/attachments/postman-olmospark/op.png)

I was unable to use this data to create a cohesive, usable grid though and resorted to doing it by hand. I gave each node and edge a unique identifier. There is an extraneous node on the eastern boundary that I ignored.

![Node IDs](https://williamscale.github.io/attachments/postman-olmospark/node_ids.jpeg)

![Edge IDs](https://williamscale.github.io/attachments/postman-olmospark/edge_ids.jpeg)

Using [On The Go Map route planner](https://onthegomap.com/#/create), I extracted all edge lengths (in miles) and created a Python dictionary capturing all connections of nodes via edges. Each node is given a key with the value being a list of tuples containing the nodes the key is connected to and the associated distance and edge. For example, node 0 is connected to node 1 via edge 0 with an edge length of 0.29 miles. A snippet of the dictionary is shown below. There is potential error in my distance data collection since it was done by hand, but it shouldn't be too substantial.

| key     | value                                            |
|:-------:|:-------------------------------------------------|
| 0       | [(1, 0.29, 0), (25, 0.18, 24)]                   |
| 1       | [(0, 0.29, 0), (2, 0.03, 1), (28, 0.06, 28)]     |
| &#8942; | &#8942;                                          |
| 61      | [(34, 0.08, 68), (60, 0.02, 97), (62, 0.03, 98)] |
| 62      | [(35, 0.11, 69), (58, 0.02, 99), (61, 0.03, 98)] |

# Model Creation

For all models run, I start at node 0 (closest to my house) until all edges have been visited. The route does not have to necessarily end at node 0, and it probably won't.

## Model Evaluation

Because this is a minimization problem, the models created are judged on overall distance travelled. It's impossible to traverse every edge once and only once. Some roads will need to be repeated. Therefore, minimum distance is the evaluation metric. Comparing the route distance to the sum of all of the edge distances is another way to measure the route efficiency. The total road length is equal to 9.96 miles, which I call $d_{optimal}$. All routes generated will be greater than $d_{optimal}$. The route efficiency is then calculated as:

$$
\text{efficiency} = \frac{d_{optimal}}{d_{route}}
$$

## Model 1: Random

The first model is purely random. At each node, a random edge is selected to travel down, until the convergence condition is met. As expected this results in many extra miles. A single iteration example result is shown below.

$$
\text{distance} = 92.83 \text{ miles} \qquad \text{efficiency} = \frac{9.96}{92.83} = 0.11
$$

For reference, a snippet of the node path is shown below.

$$
\text{node route} = [0, 1, 2, 1, 2, 3, 33, 34, ...]
$$

There are obvious inefficiencies right off the bat. Going backwards (node A $\rightarrow$ node B $\rightarrow$ node A) may make sense in some cases. However, a route of node A $\rightarrow$ node B $\rightarrow$ node A $\rightarrow$ node B has no justification. Therefore, I built a pruning function that removes these consecutive, identical edge traversals.

In this iteration, pruning shortened the route to 80.85 miles, a decrease of about 13%.

Additionally, because there is a randomness factor in the process, there are different results on each model run. Therefore, I simulated each model 100 times, pruned each resulting route, and selected the shortest route as the model's best. For all remaining results, the models have been simulated and pruned. The best simulated route distances along with their efficiencies are shown as well as simulation distributions and cumulative distance vs. convergence progress plots.

### Results

$$
\text{distance} = 44.55 \text{ miles} \qquad \text{efficiency} = \frac{9.96}{44.55} = 0.22
$$

![Model 1 Histogram](https://williamscale.github.io/attachments/postman-olmospark/m1_dist_hist.png)
![Model 1 Progress](https://williamscale.github.io/attachments/postman-olmospark/m1_progress.png)

## Model 2: Distance-Weighted

The next model I created randomly selects the next edge to traverse, but the selection probability distribution is not uniform. Instead, it is weighted by distance, with shorter edges given a higher probability of selection. This was done in an attempt to avoid repeating long edges. For example, at node 61, the available edges to travel down have lengths of 0.08, 0.02, and 0.03. The respective probabilities assigned to these edges are 0.130, 0.522, and 0.348.

### Results

$$
\text{distance} = 49.10 \text{ miles} \qquad \text{efficiency} = \frac{9.96}{49.10} = 0.20
$$

![Model 2 Histogram](https://williamscale.github.io/attachments/postman-olmospark/m2_dist_hist.png)
![Model 2 Progress](https://williamscale.github.io/attachments/postman-olmospark/m2_progress.png)

## Model 3: Exploration Status-Weighted

Model 3 is similar to Model 2 but is weighted by exploration status, with higher weights given to edges not yet explored. 

### Results

$$
\text{distance} = 37.04 \text{ miles} \qquad \text{efficiency} = \frac{9.96}{37.04} = 0.27
$$

![Model 3 Histogram](https://williamscale.github.io/attachments/postman-olmospark/m3_dist_hist.png)
![Model 3 Progress](https://williamscale.github.io/attachments/postman-olmospark/m3_progress.png)

## Model 4: Random without Backtracking

Next I created a model that did not allow immediate backtracking.

### Results

$$
\text{distance} = 36.40 \text{ miles} \qquad \text{efficiency} = \frac{9.96}{36.40} = 0.27
$$

![Model 4 Histogram](https://williamscale.github.io/attachments/postman-olmospark/m4_dist_hist.png)
![Model 4 Progress](https://williamscale.github.io/attachments/postman-olmospark/m4_progress.png)

## Model 5: Tree Search

By now, it was evident that allowing this level of randomness would not converge quickly enough for me. Therefore, I moved forward with building a solution that used some reasoning in its path generation. Model 5 looks 3 edges ahead in all directions. Of the edges in the search that have not yet been traversed, weights are assigned based on their distance from the current location. Immediate path selection is based on the sum of weights down each searched path.

For example, assume node 15 is the current location and edge weights are $\{ 1, 0.5, 0.25 \}$ for one, two, and three layers away. Assume the only visited edges are $\{ 12, 40, 78, 80, 45, 102 \}$.

From node 15, the next node to travel to is either node 14 or node 16. The scoring process for each option is shown below.

**Node 14 Path Scoring**

| Layer | Weight | Potential Edges    | Unexplored Edges | Score                  |
|:-----:|:------:|:-------------------|:-----------------|:----------------------:|
| 1     | 1      | 14                 | 14               | $1 \times 1 = 1$       |
| 2     | 0.5    | 13, 41             | 13, 41           | $2 \times 0.5 = 1$     |
| 3     | 0.25   | 12, 40, 42, 43, 79 | 42, 43, 79       | $3 \times 0.25 = 0.75$ |

$$
\text{Node 14 Score} = 1 + 1 + 0.75 = 2.75
$$

**Node 16 Path Scoring**

| Layer | Weight | Potential Edges    | Unexplored Edges   | Score                  |
|:-----:|:------:|:-------------------|:-------------------|:----------------------:|
| 1     | 1      | 15                 | 15                 | $1 \times 1 = 1$       |
| 2     | 0.5    | 16, 42             | 16, 42             | $2 \times 0.5 = 1$     |
| 3     | 0.25   | 17, 41, 43, 44, 79 | 17, 41, 43, 44, 79 | $5 \times 0.25 = 1.25$ |

$$
\text{Node 16 Score} = 1 + 1 + 1.25 = 3.25
$$

$\text{Node 16 Score} > \text{Node 14 Score}$, thus, node 16 (edge 15) is selected as the next step. In cases in which all edges within three layers have been explored, the shortest paths to all nodes connecting to an unexplored edge are calculated using Dijkstra's algorithm via the [NetworkX multi_source_dijkstra()](https://networkx.org/documentation/stable/reference/algorithms/generated/networkx.algorithms.shortest_paths.weighted.multi_source_dijkstra.html#networkx.algorithms.shortest_paths.weighted.multi_source_dijkstra) function and the closest one is selected. 

### Results

$$
\text{distance} = 12.87 \text{ miles} \qquad \text{efficiency} = \frac{9.96}{12.87} = 0.77
$$

![Model 5 Histogram](https://williamscale.github.io/attachments/postman-olmospark/m5_dist_hist_shortestpath.png)
![Model 5 Progress](https://williamscale.github.io/attachments/postman-olmospark/m5_progress_shortestpath.png)

# Conclusion

| Model                       | Average Distance | Best Distance |
|:----------------------------|:----------------:|:-------------:|
| Random                      | 91.5             | 44.6          |
| Distance-Weighted           | 102.9            | 49.1          |
| Exploration Status-Weighted | 81.4             | 37.0          |
| Random without Backtracking | 73.6             | 36.4          |
| Tree Search                 | 14.6             | 12.9          |

Based off of average distances travelled in the simulations, the tree-based model is far superior. It is computationally more expensive, but the juice is worth the squeeze, with no other model producing a path $< 30$ miles.

I ran the route on October 12, 2024. Garmin logged 12.57 miles with 508 feet of elevation gain. The difference in distance is due to some combination of distance measurements & entry on my part, Garmin GPS error, and route-running (running in the center of the street vs. on the shoulder).

![Garmin Route](https://williamscale.github.io/attachments/postman-olmospark/garmin_route.PNG)

Implementing the pruning step into the route planning would be computationally less expensive. Incorporating elevation gain into the edge weight would also be useful, with higher probabilities of selection given to hillier/flatter edges, depending on what I want to run. Adding the shortest path algorithm into the random functions if all adjacent choices have been visited may also improve results for those models. This comes at a computational cost however.