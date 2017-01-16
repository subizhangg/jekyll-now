User Experience Survey about Two visualization Form (Histogram vs.Scatterplot )   
================
Qingxue(Subi) Zhang
January 15, 2017

Motivation of this survey:
-----------------------

Different forms of data visualization convey different insights to certain dataset. We designed a survey about obtaining information between a histogram and a scatterplot, then collected 23 MDS classmates' survey feedback. We mainly focus on presenting two variables(intentional walk & home runs) sourced from the batting table of the Lahman R package (<http://cran.us.r-project.org/web/packages/Lahman/Lahman.pdf>) for the Toronto Blue Jays team for all years in the dataset (1977 - 2015). The Two core questions need to be answered through this analysis are:

#### - Which type of plot better present distribution ?

#### - Which type of plot better present outliers ?

### 0. Prepairation: Import and clean the survey data

``` r
# convert character variables into factor variables
survey_data <- survey_data %>%
  mutate(plot = as.factor(plot),
         distribution_iw = as.factor(distribution_iw),
         distribution_hr = as.factor(distribution_hr))

# present the top 5 rows of the tidy dataset
survey_data %>%
  head(5)
```

    ## # A tibble: 5 × 13
    ##        plot distribution_general outlier_general trend_general mean_iw
    ##      <fctr>                <int>           <int>         <int>   <dbl>
    ## 1 histogram                    4               4             2     6.0
    ## 2 histogram                    3               4             2     2.5
    ## 3 histogram                    5               5             5     1.0
    ## 4 histogram                    5               3             3     6.0
    ## 5 histogram                    5               5             5     4.0
    ## # ... with 8 more variables: distribution_iw <fctr>,
    ## #   distribution_hr <fctr>, outlier_iw <dbl>, outlier_hr <int>,
    ## #   mode_iw <dbl>, mode_hr <int>, smallest_iw <int>, smallest_hr <dbl>

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

``` r
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

##### Part B: Perform statistical tests

Test method : Wilcoxon signed-rank test

Assumptions :

-   Data are paired and come from the same population.(The experiment are designed to collect paired data, but made a small mistakes to assign each user a unique number)

-   Each pair is chosen randomly and independently.

-   The data are measured at least on an ordinal scale.

``` r
# H0: The average easy extent to identify general distribution should be equal between users who read histgram as well as scatterplot    
# H1: The average easy extent to identify general distribution should not be equal between users who read histgram as well as scatterplot      


wilcox.test(survey_data$distribution_general~survey_data$plot,althernative = "two.sides")
```

    ## Warning in wilcox.test.default(x = c(4L, 3L, 5L, 5L, 5L, 4L, 5L, 4L, 3L, :
    ## cannot compute exact p-value with ties

    ##
    ##  Wilcoxon rank sum test with continuity correction
    ##
    ## data:  survey_data$distribution_general by survey_data$plot
    ## W = 430.5, p-value = 0.000174
    ## alternative hypothesis: true location shift is not equal to 0

The p-value is 0.000174, which is less than 5%. So we are able to reject H0 and to say the average easy extent to identify general distribution is not equal between users who read histgram as well as scatterplot.

``` r
# H0: The average easy extent to identify general trend should be equal between users who read histgram as well as scatterplot    
# H1: The average easy extent to identify general trend should not be equal between users who read histgram as well as scatterplot    

wilcox.test(survey_data$trend_general~survey_data$plot,althernative = "two.sides")
```

    ## Warning in wilcox.test.default(x = c(2L, 2L, 5L, 3L, 5L, 3L, 4L, 5L, 3L, :
    ## cannot compute exact p-value with ties

    ##
    ##  Wilcoxon rank sum test with continuity correction
    ##
    ## data:  survey_data$trend_general by survey_data$plot
    ## W = 231.5, p-value = 0.4603
    ## alternative hypothesis: true location shift is not equal to 0

The p-value is 0.4603, which is greater than 5%. So we are unable to reject H0. The average easy extent to identify general distribution is equal between users who read histgram as well as scatterplot.

##### Part C: Make conclusions

Users' are much easier to identify the general distribution in histogram than in scatterplot, and test indicates the difference is siginificant in statistics. Maybe it derives from that histogram looks very similar to density plot where the possibility of getting each value is obvious to notice.

Users' are much easier to identify the general trend in scatterplot than in histogram. But the test indicates the difference is not siginificant in statistics. Maybe it derives from that scatterplot are better at expressing the relationship between two variables: positive correlated or negative correlated.

### (3) Analysis of questions whose answers are factor: Q5,Q6

##### Part A: Visualization users' responses

``` r
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

##### Part B: Perform statistical tests

Test method : Fisher's Exact Test

Assumptions :

-   Sample size in each group is small.

-   The contingency table is not well-balanced, which means at least one expected values in the cells of are below 5

``` r
# create contingency tables
iw_table <- table(survey_data$plot, survey_data$distribution_iw)
colnames(iw_table) <- c("Normal", "Poisson","Binomial","Exponential")

# H0: The plot type doesn't influence users' guess about variable intentional walk's distribution
# H1: The type does influence users' guess about variable intentional walk's distribution

fisher.test(iw_table)
```

    ##
    ##  Fisher's Exact Test for Count Data
    ##
    ## data:  iw_table
    ## p-value = 0.1686
    ## alternative hypothesis: two.sided

The P-value is 0.1686 (greater than 5%), so we are unable to reject null hypothesis above.

``` r
# create contingency tables
hr_table <- table(survey_data$plot, survey_data$distribution_hr)
colnames(hr_table) <- c("Normal", "Poisson","Binomial","Exponential")

# H0: The plot type doesn't influence users' guess about variable home run's distribution

# H1: The type does influence users' guess about variablehome run's distribution

fisher.test(hr_table)
```

    ##
    ##  Fisher's Exact Test for Count Data
    ##
    ## data:  hr_table
    ## p-value = 0.4548
    ## alternative hypothesis: two.sided

The P-value is 0.4548 (greater than 5%), so we are unable to reject null hypothesis above.

##### Part C: Make conclusions

We are unable to reject the two null hypothesis above, since both p-value is greater than 5%. As a result, we are unable to say the two type plots may influence users' guess about the distribution of this two specific variables.

### (4) Analysis of questions whose answers are numerical: Q4,Q9,Q10,Q11,Q12

##### Part A: Visualization users' responses

``` r
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

    ## Warning: Removed 1 rows containing non-finite values (stat_density).

![](../images/Q4,Q9,Q10,Q11,Q12%20plot-1.png)

``` r
grid.arrange(p9,p10,p11,p12,top = "Estimate of mode and minmum value for each variable")
```

    ## Warning: Removed 3 rows containing non-finite values (stat_density).

    ## Warning: Removed 2 rows containing non-finite values (stat_density).

    ## Warning: Removed 3 rows containing non-finite values (stat_density).

    ## Warning: Removed 1 rows containing non-finite values (stat_density).

![](../images/Q4,Q9,Q10,Q11,Q12%20plot-2.png)

In general, users' estimates of mode have less variation and less extreme value in histogram compared with in scatterplot, which might indicates the historgam is better at expressing mode.

In addition, users' estimates of minimum value have less variation and less extreme value in scatterplot compared with in histogram, which might indicates the scatterplot is better at expressing minimum value.

Users' estimates of intentional walk's mean, however, is very diverse in both plots where I feel it hard to discover pattern.

##### Part B: Perform statistical tests

Test method : Welch's t-test

Assumptions :

-   Two populations are both normally distributed.

-   The variance is unequal between the two populations.

``` r
# H0: The 2 plot types don't influence users' estimate of intentional walk's mean.  
# H1: The 2 plot types do influence users' estimate of intentional walk's mean.

t.test(survey_data$mean_iw~survey_data$plot,
       var.equal = FALSE,
       alternative = "two.sided")
```

    ##
    ##  Welch Two Sample t-test
    ##
    ## data:  survey_data$mean_iw by survey_data$plot
    ## t = 0.47601, df = 37.5, p-value = 0.6368
    ## alternative hypothesis: true difference in means is not equal to 0
    ## 95 percent confidence interval:
    ##  -2.317207  3.741120
    ## sample estimates:
    ##   mean in group histogram mean in group scatterplot
    ##                  5.586957                  4.875000

The P-value is 0.6368 (greater than 5%), so we are unable to reject null hypothesis above.

``` r
# H0: The 2 plot types don't influence users' estimate of intentional walk's mode.  
# H1: The 2 plot types do influence users' estimate of intentional walk's mode.

t.test(survey_data$mode_iw~survey_data$plot,
       var.equal = FALSE,
       alternative = "two.sided")
```

    ##
    ##  Welch Two Sample t-test
    ##
    ## data:  survey_data$mode_iw by survey_data$plot
    ## t = -0.95461, df = 35.717, p-value = 0.3462
    ## alternative hypothesis: true difference in means is not equal to 0
    ## 95 percent confidence interval:
    ##  -4.975156  1.791173
    ## sample estimates:
    ##   mean in group histogram mean in group scatterplot
    ##                  2.476190                  4.068182

The P-value is 0.3462 (greater than 5%), so we are unable to reject null hypothesis above.

``` r
# H0: The 2 plot types don't influence users' estimate of home run's mode.  
# H1: The 2 plot types do influence users' estimate of home run's mode.

t.test(survey_data$mode_hr~survey_data$plot,
       var.equal = FALSE,
       alternative = "two.sided")
```

    ##
    ##  Welch Two Sample t-test
    ##
    ## data:  survey_data$mode_hr by survey_data$plot
    ## t = -3.3714, df = 28.41, p-value = 0.002171
    ## alternative hypothesis: true difference in means is not equal to 0
    ## 95 percent confidence interval:
    ##  -14.172453  -3.463911
    ## sample estimates:
    ##   mean in group histogram mean in group scatterplot
    ##                  4.590909                 13.409091

The P-value is 0.002171 (less than 5%), and we are excited since we get a finding that is signicicant in statistics.

``` r
# H0: The 2 plot types don't influence users' estimate of intentional walk's minimun value.  
# H1: The 2 plot types do influence users' estimate of intentional walk's minimun value.

t.test(survey_data$smallest_iw~survey_data$plot,
       var.equal = FALSE,
       alternative = "two.sided")
```

    ##
    ##  Welch Two Sample t-test
    ##
    ## data:  survey_data$smallest_iw by survey_data$plot
    ## t = 2.1495, df = 21.064, p-value = 0.04336
    ## alternative hypothesis: true difference in means is not equal to 0
    ## 95 percent confidence interval:
    ##  0.08616348 5.18223479
    ## sample estimates:
    ##   mean in group histogram mean in group scatterplot
    ##                2.68181818                0.04761905

The P-value is 0.04336 (less than 5%), and we are excited since we get another finding that is signicicant in statistics.

``` r
# H0: The 2 plot types don't influence users' estimate of home run's minimun value.  
# H1: The 2 plot types do influence users' estimate of home run's minimun value.

t.test(survey_data$smallest_hr~survey_data$plot,
       var.equal = FALSE,
       alternative = "two.sided")
```

    ##
    ##  Welch Two Sample t-test
    ##
    ## data:  survey_data$smallest_hr by survey_data$plot
    ## t = 3.1283, df = 21.69, p-value = 0.004947
    ## alternative hypothesis: true difference in means is not equal to 0
    ## 95 percent confidence interval:
    ##  1.268472 6.270659
    ## sample estimates:
    ##   mean in group histogram mean in group scatterplot
    ##                 4.0000000                 0.2304348

The P-value is 0.004947 (less than 5%), and we are excited again since we get finding that is signicicant in statistics.

##### Part C: Make conclusions

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


\#\#\#\#\# Part B: Perform statistical tests

Test method : Wilcoxon signed-rank test

Assumptions :

-   Data are paired and come from the same population.(The experiment are designed to collect paired data, but made a small mistakes to assign each user a unique number)

-   Each pair is chosen randomly and independently.

-   The data are measured at least on an ordinal scale.

``` r
# H0: The average easy extent to identify general outliers should be equal between users who read histgram as well as scatterplot    
# H1: The average easy extent to identify general outliers should not be equal between users who read histgram as well as scatterplot      


wilcox.test(survey_data$outlier_general~survey_data$plot,althernative = "two.sides")
```

    ## Warning in wilcox.test.default(x = c(4L, 4L, 5L, 3L, 5L, 2L, 4L, 1L, 1L, :
    ## cannot compute exact p-value with ties

    ##
    ##  Wilcoxon rank sum test with continuity correction
    ##
    ## data:  survey_data$outlier_general by survey_data$plot
    ## W = 148, p-value = 0.00852
    ## alternative hypothesis: true location shift is not equal to 0

##### Part C: Make conclusions

I'm able to reject null hypothesis above since the p-value is less than 5%. That's to say, two types plots may influence users' general estimate about outliers.

### (2) Analysis of questions whose answers are numerical: Q7,Q8

##### Part A: Visualization users' responses

``` r
p7 <-  survey_data %>%
  ggplot(aes(x = outlier_iw,fill = plot)) +
  geom_density(alpha = 0.5)


p8 <-  survey_data %>%
  ggplot(aes(x = outlier_hr,fill = plot)) +
  geom_density(alpha = 0.5)

grid.arrange(p7,p8,top = "Estimate of numbers of outliers in each plot")
```

    ## Warning: Removed 2 rows containing non-finite values (stat_density).

    ## Warning: Removed 1 rows containing non-finite values (stat_density).

![](../images/Q7%20Q8%20plot-1.png)

##### Part B: Perform statistical tests

Test method : Welch's t-test

Assumptions :

-   Two populations are both normally distributed.

-   The variance is unequal between the two populations.

``` r
# H0: The 2 plot types don't influence users' estimate of the number of intentional walk's outliers
# H0: The 2 plot types do influence users' estimate of the number of intentional walk's outliers

t.test(survey_data$outlier_iw~survey_data$plot,
       var.equal = FALSE,
       alternative = "two.sided")
```

    ##
    ##  Welch Two Sample t-test
    ##
    ## data:  survey_data$outlier_iw by survey_data$plot
    ## t = -1.0176, df = 24.15, p-value = 0.3189
    ## alternative hypothesis: true difference in means is not equal to 0
    ## 95 percent confidence interval:
    ##  -11.580261   3.930158
    ## sample estimates:
    ##   mean in group histogram mean in group scatterplot
    ##                  5.761905                  9.586957

The P-value is 0.3189 (greater than 5%), so we are unable to reject null hypothesis above.

``` r
# H0: The 2 plot types don't influence users' estimate of the number of home run's outliers
# H0: The 2 plot types do influence users' estimate of the number of home run's outliers

t.test(survey_data$outlier_hr~survey_data$plot,
       var.equal = FALSE,
       alternative = "two.sided")
```

    ##
    ##  Welch Two Sample t-test
    ##
    ## data:  survey_data$outlier_hr by survey_data$plot
    ## t = 0.45844, df = 33.378, p-value = 0.6496
    ## alternative hypothesis: true difference in means is not equal to 0
    ## 95 percent confidence interval:
    ##  -16.04609  25.38601
    ## sample estimates:
    ##   mean in group histogram mean in group scatterplot
    ##                  16.40909                  11.73913

The P-value is 0.6496 (greater than 5%), so we are unable to reject null hypothesis above.

##### Part C: Make conclusions

Two types plots don't influence users' specific estimate about outliers to each variable.
