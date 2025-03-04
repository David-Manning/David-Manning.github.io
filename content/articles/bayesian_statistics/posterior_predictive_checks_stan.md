+++
title = 'Posterior Predictive Checks'
draft = true
toc = true
type = 'docs'
+++

This post explains the why and how of posterior predictive checks, using R's brms package.

### Posterior Predictive Checks
Posterior predictive checks are usually graphical checks that the model gives predictions that more or less follow the original data. This isn't a substitute for other model selection methods such as LOO-CV or information criterion and is usually done before those methods because it highlights major issues with a model.

To do them:

1) Fit a model of your choice.
2) Generate predictions from the model, usually using the original data or using a test set (preferably both, if you have a decent sample size).
3) Plot a graph to compare the original data to the predictions. In this case, I have used a density plot. How this is displayed will usually depend on the data, football scores tend to be quite low so the type of graph I have used doesn't work as well.
4) Judge how well the model fits the data based on the graph.

### Dataset
I will use the `quine` data in the MASS package, which shows the number of days missed by school pupils in New South Wales, with ethnicity (aboriginal or not-aboriginal), sex (female or male), age (F0 for primary or forms F1, F2, F3), learner status (AL for average learner, SL for slow learner), and days absent.

```R
> str(quine)
'data.frame':	146 obs. of  5 variables:
 $ Eth : Factor w/ 2 levels "A","N": 1 1 1 1 1 1 1 1 1 1 ...
 $ Sex : Factor w/ 2 levels "F","M": 2 2 2 2 2 2 2 2 2 2 ...
 $ Age : Factor w/ 4 levels "F0","F1","F2",..: 1 1 1 1 1 1 1 1 2 2 ...
 $ Lrn : Factor w/ 2 levels "AL","SL": 2 2 2 1 1 1 1 1 2 2 ...
 $ Days: int  2 11 14 5 5 13 20 22 6 6 ...
```

I won't show any graphs of the data (although I did plot them), but here is a table of the mean, variance, and their ratios for each group. Note that for the Poisson distribution (the obvious model for count data), the variance to mean ratio should be 1.

```R
quine %>%
	group_by(Age, Eth, Lrn) %>%
	summarise(mean = mean(Days),
			  var = var(Days),
			  ratio = var / mean,
			  .groups = "drop")
```


| Age | Eth | Lrn | Mean  | Var   | Ratio |
|-----|-----|-----|-------|-------|-------|
| F0  | A   | AL  | 16.7  | 169.0  | 10.1  |
| F0  | A   | SL  | 7.5   | 35.0    | 4.67  |
| F0  | N   | AL  | 10.6  | 100.0  | 9.48  |
| F0  | N   | SL  | 28.8  | 711.0  | 24.7  |
| F1  | A   | AL  | 11.1  | 32.8  | 2.94  |
| F1  | A   | SL  | 19.5  | 302.0  | 15.5  |
| F1  | N   | AL  | 9.12  | 69.3  | 7.59  |
| F1  | N   | SL  | 6.06  | 23.2  | 3.84  |
| F2  | A   | AL  | 24.2  | 266.0  | 11.0  |
| F2  | A   | SL  | 36.6  | 596.0  | 16.3  |
| F2  | N   | AL  | 8.12  | 85.3  | 10.5  |
| F2  | N   | SL  | 12.0    | 136.0  | 11.3  |
| F3  | A   | AL  | 20.1  | 202.0  | 10.1  |
| F3  | N   | AL  | 19.2  | 320.0  | 16.7  |

The number of days absent ranges from 0 to 81.

### Priors
For all these models, I will use the default priors that brms gives for each model. 

### Gaussian Model
The first model most people use is a Gaussian model, where the errors follow a Gaussian distribution. In brms:

```R
fit_gaussian <- brm(Days ~ Sex + (1 | Age) + Eth + Lrn, 
                    family = gaussian(), data = quine)
```
I haven't displayed the model output to save space, but estimated errors were very, very high - possibly down to the vague priors, but let's see how it compares to the original data.
```R
pp_check(fit_gaussian) +
	theme_bw() +
		labs(title = "Posterior Predictive Check for quine dataset",
		     subtitle = "Gaussian Distribution",
		     x = "Days Absent",
		     y = "Density")
```

![](/articles/bayesian_stats/posterior_predictive_checks/pp_check_gaussian.png)

The solid line is the density plot of the actual data and the faded lines are density plots of some draws. These should follow the original data quite closely and it should only be possible to notice that the observed data are not from the draws because of the bold. There are a few issues with this plot.

Error 1: There are far too many negative values, which we know is not possible. A very small number (fractions of a percent) of predictions going outside the bounds might not be an issue, but clearly many here are (more than 16% of all predictions).

Error 2: The mode is in the wrong place. On the original data, it is at 5, but here is around 15 to 20. It might be acceptable to have a mode at 6, depending on how the rest of the distribution looks, but this is too much.

This is a poor fit for the observed data and there isn't much good to say about the fit, so the Gaussian probably **isn't** a good fit for these data.


### Poisson Model

The most obvious model for count data is Poisson, which gives a model that looks like this.

```r
fit_poisson <- brm(Days ~ Sex + (1 | Age) + Eth + Lrn, 
                   family = poisson(), 
                   data = quine)
```

And a graphical posterior check looks like this:
```R
pp_check(fit_poisson) +
	theme_bw() +
		labs(title = "Posterior Predictive Check for quine dataset",
		     subtitle = "Poisson Distribution",
		     x = "Days Absent",
		     y = "Density")
```
![](/articles/bayesian_stats/posterior_predictive_checks/pp_check_poisson.png)

This is definitely better

Positive 1: All values are non-negative, due to the constraint in the Poisson distribution.

Error 1: The mode of the predictions is around 12 or 13, compared to 5 for the actual data.

Error 2: The predictions only go up to around 45, but the actual data go all the way to 80.

Error 3: There is a large underestimate in low values (days absent < 6 or 7) and a large overestimate from there up to around days absent = 30.

This is better than before, but still pretty rubbish.

### Negative-Binomial Model
Finally, I will model it as a negative binomial distribution. This is a Poisson distribution with an overdispersion parameter, giving a higher variation than expected by the Poisson (brms does not allow the overdispersion parameter to vary with covariates).

```R
fit_neg_binom <- brm(Days ~ Sex + (1 | Age) + Eth + Lrn, 
                     family = negbinomial(), data = quine)
```

A graphical posterior predictive check looks like this:
```R
pp_check(fit_neg_binom) +
	theme_bw() +
		labs(title = "Posterior Predictive Check for quine dataset",
		     subtitle = "Negative Binomial Distribution",
		     x = "Days Absent",
		     y = "Density")
```
![](/articles/bayesian_stats/posterior_predictive_checks/pp_check_neg_binom.png)

Positive 1: The predictions are all non-negative, due to the constraint imposed by the negative-binomial distribution.

Positive 2: The predictions clearly follow the dataset.

Positive 3: The predictions are a similar density at the mode, with some draws over-estimating the density and some under-estimating the density.

Error 1: The days absent predictions go over 200, but the maximum in the original data is 81. Depending on what you are modelling, this may not be critical, and including other predictor variables or more informative priors should help. 


This model is clearly a much better fit to the data, and unless there is an even better fitting distribution, we can use this for whatever we wanted the model for.

