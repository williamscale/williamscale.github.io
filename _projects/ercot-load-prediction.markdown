---
layout: projects
title: Predicting ERCOT Daily System Load
---

Code for this project can be found in my [repo](https://github.com/williamscale/projects/tree/main/ERCOT).

# Background

The goal of this project is to predict system energy load.

# Data

Hourly system load data was downloaded as a csv from the [ERCOT Data Access Portal](https://data.ercot.com/) within the Actual System Load by Forecast Zone report. A snippet of the raw data is shown below. The system load units are not explicit, but based off other ERCOT documentation, I believe it is megawatts (MW).

| operatingDay   | hourEnding  | north    | south    | west    | houston  | total    | DSTFlag |
|:--------------:|:-----------:|:---------|:---------|:--------|:---------|:---------|:-------:|
| 1/9/2025       | 1:00        | 21376.41 | 14718.76 | 9639.81 | 12661.75 | 58396.72 | FALSE   |
| 1/9/2025       | 2:00        | 20791.35 | 14412.47 | 9610.9  | 12521.24 | 57335.97 | FALSE   |
| &#8942;        | &#8942;     | &#8942;  | &#8942;  | &#8942; | &#8942;  | &#8942;  | &#8942; |
| 12/11/2023     | 23:00       | 20791.35 | 14412.47 | 9610.9  | 12521.24 | 57335.97 | FALSE   |
| 12/11/2023     | 24:00       | 20791.35 | 14412.47 | 9610.9  | 12521.24 | 57335.97 | FALSE   |

The data includes system loads for different zones in Texas as shown below.

![ERCOT Zones](https://williamscale.github.io/attachments/ercot-load-prediction/ERCOT-Maps_Load-Zone.jpg)

The **total** column is simply the sum of loads across all zones. The **DSTFlag** column refers to Daylight Savings Time (DST) and thus clarifies instances in which there are duplicate timestamps at 2AM. For this work, only South zone data was used. Additionally, data is summarized by summing load across all hours in a day. To avoid DST days having extra load due to their 25th hour, rows where **DSTFlag** is *True* were removed. A couple of new features were created based only on the date: **dayOfWeek** and **weekend.flag**. Finally, to simplify cyclic period definitions, only data in 2024 is used. The ERCOT dataset then looks as such:

| operatingDay | dayOfWeek | weekend.flag | daily.load |
|:------------:|:----------|:------------:|:----------:|
| 1/1/2024     | Monday    | Weekday      | 257883.6   |
| 1/2/2024     | Tuesday   | Weekday      | 299634.2   |
| &#8942;      | &#8942;   | &#8942;      | &#8942;    | 
| 12/30/2024   | Monday    | Weekday      | 273434.7   |
| 12/31/2024   | Tuesday   | Weekday      | 261770.4   |

Next, [NOAA's Online Weather Data](https://www.weather.gov/media/climateservices/NOWData.pdf) (NOWData) was [queried](https://www.weather.gov/wrh/Climate?wfo=ewx) for daily high and low temperatures [$\deg$F]. Although the South zone does not include San Antonio, the location selected was San Antonio Area as it is geographically central to the South zone and should be representative. A snippet of the weather data is shown below.

| operatingDay | Min     | Max     |
|:------------:|:-------:|:-------:|
| 1/1/2024     | 46      | 62      |
| 1/2/2024     | 44      | 49      |
| &#8942;      | &#8942; | &#8942; |
| 12/30/2024   | 46      | 89      |
| 12/31/2024   | 50      | 72      |

It's important to recognize that these are actual temperatures, not forecasts.

The two datasets were then joined on **operatingDay**. The data was then split into training, validation, and testing sets. The training set includes all data between January 1, 2024 and September 30, 2024 ($n=274$). The validation set is October & November 2024 ($n=61$) and the test set is December 2024 ($n=31$). A snippet of the training set is shown below.

| operatingDay | dayOfWeek | weekend.flag | Min Temp | Max Temp | daily.load |
|:------------:|:----------|:------------:|:--------:|:--------:|:----------:|
| 1/1/2024     | Monday    | Weekday      | 46       | 62       | 257883.6   |
| 1/2/2024     | Tuesday   | Weekday      | 44       | 49       | 299634.2   |
| &#8942;      | &#8942;   | &#8942;      | &#8942;  | &#8942;  | &#8942;    | 
| 9/29/2024    | Sunday    | Weekend      | 64       | 97       | 333407.2   |
| 9/29/2024    | Monday    | Weekday      | 64       | 95       | 349663.2   |

# Features

In this section, all analysis is done using the training set.

## Day of Week

Below are the daily loads by the day of the week. It is not immediately evident if there are any significant differences.

![Load by Day of Week](https://williamscale.github.io/attachments/ercot-load-prediction/load_day_jitter.png)

An Analysis of Variance (ANOVA) can be done to determine if there are differences. However, as shown below, the data is not normal and thus, ANOVA assumptions are violated and a nonparametric method must be used.

![Day of Week Histogram](https://williamscale.github.io/attachments/ercot-load-prediction/hist_day.png)
![Day of Week QQ](https://williamscale.github.io/attachments/ercot-load-prediction/qq_day.png)

Therefore, a Kruskal-Wallis test was performed and results imply there is not a statistically significant difference in system load by day of the week ($\text{p-value} = 0.90$) and it should not be included in the model.

## Weekday vs. Weekend

Similarly, below are the daily loads for weekdays and weekends. It is not obvious whether there is a significant difference between the two groups. Further investigation may be useful.

![Load by Weekend](https://williamscale.github.io/attachments/ercot-load-prediction/load_weekend_jitter.png)

As shown below the distributions of daily loads by weekday/weekend are not normal either. Thus, a two-sample t-test cannot be used and we opt for a two-sample Wilcoxon test.

![Weekend Histogram](https://williamscale.github.io/attachments/ercot-load-prediction/hist_weekend.png)
![Weekend QQ](https://williamscale.github.io/attachments/ercot-load-prediction/qq_weekend.png)

$$\text{p-value} = 0.18$$, therefore we accept the null hypothesis that there is not a significant difference between weekdays and weekends.

## Temperature

Below are the minimum and maximum daily temperatures plotted with the daily loads. It is evident that there is a relationship between the variables, albeit a non-linear one.

![Min vs. Load](https://williamscale.github.io/attachments/ercot-load-prediction/load_minTemp.png)
![Max vs. Load](https://williamscale.github.io/attachments/ercot-load-prediction/load_maxTemp.png)

Because the day of the week features were deemed not statistically significant, they are excluded from model building. Temperature with a quadratic term is thus the only feature so far. Because minimum and maximum temperatures are collinear, they cannot be used simultaneously in a model, but will be evaluated separately. 

# Model Building

## Model 1: Minimum Temperature

This model was built on the training data as

$$
\text{Daily Load} = \beta_{0} + \beta_{1} \text{Min Temp} + \beta_{2} \text{Min Temp}^{2}
$$

where $\beta_{0} = 728,831$, $\beta_{1} = -18,506$, and $\beta_{2} = 183$. All coefficients are significant with $\text{p-values} \approx 0$ and $R^{2}=0.88$. The resulting regression line is shown below.

![Model 1](https://williamscale.github.io/attachments/ercot-load-prediction/m1.png)

### Assumptions

Firstly, I checked for outliers using the Cook's distance metric. A data point exceeding a Cook's distance of 1 or $\frac{4}{n}$ where $n$ is the number of data points, should be investigated. These are two common rules of thumb, not hard rules. As shown below, there are many points above the $\frac{4}{n}$ red line and a few significantly larger than the rest of the dataset.

![CD Model 1](https://williamscale.github.io/attachments/ercot-load-prediction/cd_m1.png)

From the fitted values vs. residuals plot below, we can see the constant variance and linearity assumptions are satisfied. The residuals are centered around 0 and there does not appear to be any heteroscedasticity. There may be some independence assumption deviation with potential clusters at the lower end of the fitted values. However, it doesn't seem too severe.

![Fitted vs. Residuals M1](https://williamscale.github.io/attachments/ercot-load-prediction/fitted_resid_m1.png)

The normality assumption holds as shown by the histogram and QQ plots below.

![Hist Residuals M1](https://williamscale.github.io/attachments/ercot-load-prediction/hist_resid_m1.png)
![QQ M1](https://williamscale.github.io/attachments/ercot-load-prediction/qq_m1.png)

## Model 2: Maximum Temperature

This model was built on the training data as

$$
\text{Daily Load} = \beta_{0} + \beta_{1} \text{Max Temp} + \beta_{2} \text{Max Temp}^{2}
$$

where $\beta_{0} = 1,017,614$, $\beta_{1} = -21,511$, and $\beta_{2} = 155$. All coefficients are significant with $\text{p-values} \approx 0$ and $R^{2}=0.87$. The resulting regression line is shown below.

![Model 2](https://williamscale.github.io/attachments/ercot-load-prediction/m2.png)

### Assumptions

![CD Model 2](https://williamscale.github.io/attachments/ercot-load-prediction/cd_m2.png)

The three highest points correspond to the three days of the training set with much colder daily maximum temps than the rest of the data. January 15-17 were 3 of the 4 coldest daily highs in the entire year. Removing some outliers should be considered. Ideally, more winter data would be available to determine how rare these cold days are.

From the fitted values vs. residuals plot below, we can see the constant variance and linearity assumptions are satisfied. The residuals are centered around 0 and there does not appear to be any heteroscedasticity. There may be some independence assumption deviation with potential clusters at the lower end of the fitted values. However, it doesn't seem too severe.

![Fitted vs. Residuals M2](https://williamscale.github.io/attachments/ercot-load-prediction/fitted_resid_m2.png)

The normality assumption holds as shown by the histogram and QQ plots below.

![Hist Residuals M2](https://williamscale.github.io/attachments/ercot-load-prediction/hist_resid_m2.png)
![QQ M2](https://williamscale.github.io/attachments/ercot-load-prediction/qq_m2.png)

## Model Comparison

Predictions were then made on the validation set using both Model 1 and Model 2. Model 2 performs slightly better using root mean squared error (RMSE) as the comparison metric. This method penalizes larger errors more than mean absolute error (MAE), for example.

| Model | MAE   | RMSE  |
|:-----:|:-----:|:-----:|
| 1     | 18,168 | 22,820 |
| 2     | 16,594 | 20,031 |

Then, the training and validation sets are combined and a model is re-trained on this larger dataset using the features from Model 2. The resulting model is

$$
\text{Daily Load} = \beta_{0} + \beta_{1} \text{Max Temp} + \beta_{2} \text{Max Temp}^{2}
$$

where $\beta_{0} = 1,019,220$, $\beta_{1} = -21,555$, and $\beta_{2} = 155$. All coefficients are significant with $\text{p-values} \approx 0$ and $R^{2}=0.86$. The addition of the validation set data did not affect the estimated coefficients much. The resulting regression line is shown below.

![Model](https://williamscale.github.io/attachments/ercot-load-prediction/m.png)

# Prediction

Finally, the latest model was used to make predictions on the test set with $\text{MAE}=12,617$ and $\text{RMSE}=17,807$.