---
layout: post
title: User Experience Survey about Two visualization Form (Histogram vs.Scatterplot )
---

### Motivation of this survey:

Different forms of data visualization convey different insights to certain dataset. We designed a survey about obtaining information between a histogram and a scatterplot, then collected 23 MDS classmates' survey feedback. We mainly focus on presenting two variables(intentional walk & home runs) sourced from the batting table of the Lahman R package (<http://cran.us.r-project.org/web/packages/Lahman/Lahman.pdf>) for the Toronto Blue Jays team for all years in the dataset (1977 - 2015). The Two core questions need to be answered through this analysis are:

#### - Which type of plot better present distribution ?

#### - Which type of plot better present outliers ?

### 0. Prepairation: Import and clean the survey data

```
# convert character variables into factor variables
survey_data <- survey_data %>%
  mutate(plot = as.factor(plot),
         distribution_iw = as.factor(distribution_iw),
         distribution_hr = as.factor(distribution_hr))

# present the top 5 rows of the tidy dataset
survey_data %>%
  head(5)
```
### 1. Analysis between plot type (histogram/scatterplot) and variables'distributions

#### (1) Group Questions related to distribution:

| Answer type | Question No. | Variable Name         |
|-------------|--------------|-----------------------|
| ordinal     | 1            | distribution\_general |
| ordinal     | 3            | trend\_general        |
| numerical   | 4            | mean\_iw              |
| factor      | 5            | distribution\_iw      |
| factor      | 6            | distribution\_hr      |
| numerical   | 9            | mode\_iw              |
| numerical   | 10           | mode\_hr              |
| numerical   | 11           | smallest\_iw          |
| numerical   | 12           | smallest\_hr          |

#### (2) Analysis of questions whose answers are ordinal: Q1,Q3

##### Part A: Visualization users' responses

```
# import two new palettes
newpalette1<- colorRampPalette(brewer.pal(8,"Accent"))(length(unique(survey_data$plot)))
newpalette2<- colorRampPalette(brewer.pal(12,"Set3"))(length(unique(survey_data$plot)))

# create boxplots for Q1,Q3
p1 <- survey_data %>%
  ggplot(aes(x = plot, y= distribution_general,fill = plot )) +
  geom_boxplot() +
  scale_fill_manual(values=newpalette2)


p3 <- survey_data %>%
  ggplot(aes(x = plot, y= trend_general,fill = plot )) +
  geom_boxplot() +
  scale_fill_manual(values=newpalette2)

# glue the two plots together
grid.arrange(p1,p3,top = "How easily to estimate the general distribution/trend ?")
```

![](../images/Q1%20Q3%20plot-1.png)

Most users think it's much easier to estimate the general distribution in histogram plot than in scatterplot, but a little bit harder to estimate the general trend compared to scatterplot.

am as well as scatterplot.

##### Part B: Make conclusions

Users' are much easier to identify the general distribution in histogram than in scatterplot, and test indicates the difference is significant in statistics. Maybe it derives from that histogram looks very similar to density plot where the possibility of getting each value is obvious to notice.

Users' are much easier to identify the general trend in scatterplot than in histogram. But the test indicates the difference is not significant in statistics. Maybe it derives from that scatterplot are better at expressing the relationship between two variables: positive correlated or negative correlated.

### (3) Analysis of questions whose answers are factor: Q5,Q6

##### Part A: Visualization users' responses

```
p5 <- survey_data %>%
  ggplot(aes(x = distribution_iw)) +
  geom_bar(aes(fill = plot),color = "black") +
  facet_grid(plot ~ .) +
  scale_fill_manual(values=newpalette1)

p6 <- survey_data %>%
  ggplot(aes(x = distribution_hr)) +
  geom_bar(aes(fill = plot),color = "black") +
  facet_grid(plot ~ .) +
  scale_fill_manual(values=newpalette1)

grid.arrange(p5,p6,top = "Users' guess about the distribution type of each variable")
```

![](../images/Q5%20Q6%20plot-1.png)

On variable intentional walk, nobody guessed it follows normal distribution when reading a histogram. I think it should be one of the most impossible guesses in this case(the normal distribution and binomial distribution is symmetric, which doesn't look like the two type plots in our survey neither)

On variable home runs, less users guessed it follows a normal distribution when reading a histogram compared with a scatterplot. But a weired thing is more users guessed it follows a binomial distribution when reading a histogram.

##### Part B: Make conclusions

We are unable to reject the two null hypothesis above, since both p-value is greater than 5%. As a result, we are unable to say the two type plots may influence users' guess about the distribution of this two specific variables.

### (4) Analysis of questions whose answers are numerical: Q4,Q9,Q10,Q11,Q12

##### Part A: Visualization users' responses

```
p4 <-  survey_data %>%
  count(mean_iw, plot) %>%
  ggplot(aes(x = mean_iw,fill = plot)) +
  geom_density(alpha = 0.3) +
  ggtitle("Estimate of the mean of intentional walks for a player")


p9 <-  survey_data %>%
  ggplot(aes(x = mode_iw,fill = plot)) +
  geom_density(alpha = 0.5)  

p10 <-  survey_data %>%
  ggplot(aes(x = mode_hr,fill = plot)) +
  geom_density(alpha = 0.5)  

p11 <-  survey_data %>%
  ggplot(aes(x = smallest_iw,fill = plot)) +
  geom_density(alpha = 0.5)

p12 <-  survey_data %>%
  ggplot(aes(x = smallest_hr,fill = plot)) +
  geom_density(alpha = 0.5)

p4
```

![](../images/Q4,Q9,Q10,Q11,Q12%20plot-1.png)

```
grid.arrange(p9,p10,p11,p12,top = "Estimate of mode and minmum value for each variable")
```

![](../images/Q4,Q9,Q10,Q11,Q12%20plot-2.png)

In general, users' estimates of mode have less variation and less extreme value in histogram compared with in scatterplot, which might indicates the histogram is better at expressing mode.

In addition, users' estimates of minimum value have less variation and less extreme value in scatterplot compared with in histogram, which might indicates the scatterplot is better at expressing minimum value.

Users' estimates of intentional walk's mean, however, is very diverse in both plots where I feel it hard to discover pattern.

##### Part B: Make conclusions

There are 3 questions' results pass the tests: home run's mode, minimum value of intentional walk and minimum value of home run. In other words, the two type of plot may influence users' guess about home run's mode, minimum value of intentional walk and minimum value of home run.

### 2. Analysis between plot type (histogram/scatterplot) and variables' outlier

#### (1) Group Questions related to distribution:

| Answer Type | Question No. | Variable Name    |
|-------------|--------------|------------------|
| ordinal     | 2            | outlier\_general |
| numerical   | 7            | outlier\_iw      |
| numerical   | 8            | outlier\_hr      |

### (1) Analysis of questions whose answers are ordinal: Q2

##### Part A: Visualization users' responses

``` r
(p2 <- survey_data %>%
  ggplot(aes(x = plot, y= outlier_general,fill = plot )) +
  geom_boxplot() +
  scale_fill_manual(values=newpalette2) +
  ggtitle("How easily to identify outliers in each plot? "))
```

![](../images/Q2%20plot-1.png)


##### Part B: Make conclusions

Two types plots don't influence users' specific estimate about outliers to each variable.
