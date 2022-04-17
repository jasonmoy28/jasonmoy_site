+++
categories = ["tutorial"]
date = "2022-04-17"
description = ""
featured = ""
featuredalt = ""
featuredpath = "date"
linktitle = ""
title = "An introduction to psycModel"
slug = ""
type = "post"
+++

At its core, this package allows people to analyze their data with one simple function call. For example, when you are running a linear regression, you need to fit the model, check the goodness of fit (e.g., R2), check the model assumption, and plot the interaction (if the interaction is included). Without this package, you need several packages to do the above steps. Additionally, if you are an R beginner, you probably don’t know where to find all these R packages. This package has done all that work for you, so you can just do everything with one simple function call.

Another good example is CFA. The most common (and probably the only) option to fit a CFA in R is using lavaan. Lavaan has its own unique set of syntax. It is very versatile and powerful, but you do need to spend some time learning it. It may not worth the time for people who just want to run a quick and simple CFA model. In my package, it’s very intuitive with `cfa_summary(data, x1:x3)`, and you get the model summary, the fit measures, and a nice-looking path diagram. The same logic also applies to HLM since lme4 / nlme also has its own set of syntax that you need to learn.

Moreover, I also made fitting the model even simpler by using the dplyr::select syntax. In short, traditionally, if you want to fit a linear regression model, the syntax looks like this `lm(y ~ x1 + x2 + x3 + x4 + ... + xn, data)`. Now, the syntax is much shorter and more intuitive: `lm_model(y, x1:xn, data)`. You can even replace `x1:xn` with `everything()`.

## Regression Models

### Integrated Summary for Linear Regression

integrated_model_summary is the integrated function for linear regression and generalized linear regression. It will first fit the model using lm_model or glm_model, then it will pass the fitted model object to model_summary which produces model estimates and assumption checks. If interaction terms are included, they will be passed to the relevant interaction_plot function for plotting (the package currently does not support generalized linear regression interaction plotting).

Additionally, you can request assumption_plot and simple_slope (default is FALSE). By requesting assumption_plot, it produces a panel of graphs that allow you to visually inspect the model assumption (in addition to testing it statistically). simple_slope is another powerful way to probe further into the interaction. It shows you the slope estimate at the mean and +1/-1 SD of the mean of the moderator. For example, you hypothesized that social-economic status (SES) moderates the effect of teacher experience on education quality. Then, simple_slope shows you the slope estimate of teacher experience on education quality at +1/-1 SD and the mean level of SES. Additionally, it produces a Johnson-Newman plot that shows you at what level of the moderator that the slope_estimate is predicted to be insignificant.

```r
integrated_model_summary(
   data = iris,
   response_variable = Sepal.Length,
   predictor_variable = tidyselect::everything(),
   two_way_interaction_factor = c(Sepal.Width, Petal.Width),
   model_summary = TRUE,
   interaction_plot = TRUE,
   assumption_plot = TRUE,
   simple_slope = TRUE,
   plot_color = TRUE
 )
```

The output will be

```r
Model Summary
Model Type = Linear regression
Outcome = Sepal.Length
Predictors = Sepal.Width, Petal.Length, Petal.Width, Species

Model Estimates
───────────────────────────────────────────────────────────────────────────────────────
                Parameter  Coefficient       t   df     SE          p            95% CI
───────────────────────────────────────────────────────────────────────────────────────
              (Intercept)        1.609   3.858  144  0.417  0.000 ***  [ 0.785,  2.433]
              Sepal.Width        0.772   6.475  144  0.119  0.000 ***  [ 0.536,  1.007]
             Petal.Length        0.749  12.754  144  0.059  0.000 ***  [ 0.633,  0.865]
              Petal.Width        0.112   0.297  144  0.378  0.767      [-0.635,  0.860]
                  Species       -0.264  -2.222  144  0.119  0.028 *    [-0.499, -0.029]
  Sepal.Width:Petal.Width       -0.154  -1.485  144  0.103  0.140      [-0.358,  0.051]
───────────────────────────────────────────────────────────────────────────────────────

Goodness of Fit
───────────────────────────────────────────────────
     AIC      BIC     R²  R²_adjusted   RMSE      σ
───────────────────────────────────────────────────
  82.514  103.588  0.864        0.860  0.304  0.310
───────────────────────────────────────────────────

Model Assumption Check
OK: Residuals appear to be independent and not autocorrelated (p = 0.980).
OK: residuals appear as normally distributed (p = 0.892).
Unable to check autocorrelation. Try changing na.action to na.omit.
Warning: Heteroscedasticity (non-constant error variance) detected (p = 0.047).
Warning: Severe multicolinearity detected (VIF > 10). Please inspect the following table to identify high correlation factors.
Multicollinearity Table
─────────────────────────────────────────────
                     Term      VIF  SE_factor
─────────────────────────────────────────────
              Sepal.Width    4.174      2.043
             Petal.Length   16.625      4.077
              Petal.Width  128.506     11.336
                  Species   14.673      3.831
  Sepal.Width:Petal.Width   90.119      9.493
─────────────────────────────────────────────
```
