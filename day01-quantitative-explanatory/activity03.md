Activity 3 - MLR
================

In the last activity, you might have noticed that I made this Rmd output
a document with a type of `github_document` (in the YAML header
underneath the title - on line 3) instead of a HTML, Word, or PDF
document. This produces a GitHub friendly Markdown file that GitHub then
renders to HTML. You can read more about this output type in RMarkdown’s
[documentation
page](https://rmarkdown.rstudio.com/github_document_format.html) if you
want to learn more.

# Day 1

## Load the necessary packages

I encourage you to continue using the two packages from Posit (formerly
[RStudio](https://posit.co/)): `{tidyverse}` and `{tidymodels}`.
Remember that [Emil Hvitfeldt](https://www.emilhvitfeldt.com/) (of
Posit) has put together a [complementary online
text](https://emilhvitfeldt.github.io/ISLR-tidymodels-labs/index.html)
for the labs in the *ISLR* text that utilize `{tidyverse}` and
`{tidymodels}` instead of base R.

- In the **Packages** pane of RStudio, check if `{tidyverse}` and
  `{tidymodels}` are installed. Be sure to check both your **User
  Library** and **System Library**.

- If either of these are not currently listed (they should be because
  you verified this in Activity 1), type the following in your
  **Console** pane, replacing `package_name` with the appropriate name,
  and press Enter/Return afterwards.

  ``` r
  install.packages("package_name")
  ```

- Once you have verified that both `{tidyverse}` and `{tidymodels}` are
  installed (in either your user or system library), load these packages
  in the R chunk below titled `load-packages`.

- Run the `load-packages` code chunk or **knit**
  <img src="../README-img/knit-icon.png" alt="knit" width = "20"/> icon
  your Rmd document to verify that no errors occur.

``` r
library(tidyverse)
library(tidymodels)
library(GGally)
library("scatterplot3d")
```

Since we will be looking at many relationships graphically, it will be
nice to not have to code each of these individually. `{GGally}` is an
extension to `{ggplot2}` that reduces some of the complexities when
combining multiple plots. For example,
[`GGally::ggpairs`](http://ggobi.github.io/ggally/articles/ggpairs.html)
is very handy for pairwise comparisons of multiple variables.

- In the **Packages** pane of RStudio, check if `{GGally}` is already
  installed. Be sure to check both your **User Library** and **System
  Library**.

- If this is not currently listed, type the following in your
  **Console** pane and press Enter/Return afterwards.

  ``` r
  install.packages("GGally")
  ```

- Once you have verified that `{GGally}` is installed, load it in the R
  chunk titled `load-packages`.

- Run the `setup` code chunk or **knit**
  <img src="../README-img/knit-icon.png" alt="knit" width = "20"/> icon
  your Rmd document to verify that no errors occur.

## Load the data and

I found a way to upload data from OpenIntro without needing to download
it first! Recall that data we are working with is from the OpenIntro
site (its “about” page:
<https://www.openintro.org/data/index.php?data=hfi>). We can access the
raw data from their tab-delimited text file link:
<https://www.openintro.org/data/tab-delimited/hfi.txt>.

Create a new R code chunk below that is titled `load-data` and reads in
the above linked TSV (tab-separated values) file by doing the following:

- Rather than downloading this file, uploading to RStudio, then reading
  it in, explore how to load this file directly from the provided URL
  with `readr::read_tsv` (`{readr}` is part of `{tidyverse}`).
- Assign this data set into a data frame named `hfi` (short for “Human
  Freedom Index”).
- Filter the data `hfi` data frame for year 2016 and assigns the result
  to an R data object named `hfi_2016`. You will use `hfi_2016` for the
  remainder of this activity.

``` r
hfi <- readr::read_tsv("hfi.txt")
```

    ## Rows: 1458 Columns: 123
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: "\t"
    ## chr   (3): ISO_code, countries, region
    ## dbl (120): year, pf_rol_procedural, pf_rol_civil, pf_rol_criminal, pf_rol, p...
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
hfi_2016 <- hfi %>% filter(year == "2016")
```

We will continue using personal freedom scores, `pf_score`, as the
response variable and build on our model that had
`pf_expression_control` as the explanatory variable.

Create a new R code chunk below, with an appropriate title, that does
the following:

- Review the about page of the data set and select at least one
  additional numeric variables (hint: look for `<dbl>` or `<int>`
  designations) to describe its distribution. Remember to write your
  description.
- You may also wish to do this for `pf_score` and
  `pf_expression_control` again to help you remember what you noticed
  last week.

## Pairwise relationships

In Activity 2 you explored simple linear regression models.
Specifically, you fit and assessed this relationship:

$$
y = \beta_0 + \beta_1 \times x + \varepsilon
$$

``` r
ggplot(hfi_2016, aes(y = pf_rank)) +
  geom_boxplot(fill = "grey") +
  ggtitle("Box Plot of Personal Freedom (rank)") +
  ylab("Personal Freedom (rank)")
```

![](activity03_files/figure-gfm/distrubution-plots-1.png)<!-- -->

``` r
ggplot(hfi_2016, aes(y = pf_score)) +
  geom_boxplot(fill = "grey") +
  ggtitle("Box Plot of Personal Freedom Scores") +
  ylab("Personal Freedom Score")
```

![](activity03_files/figure-gfm/distrubution-plots-2.png)<!-- -->

``` r
ggplot(hfi_2016, aes(y = pf_expression_control)) +
  geom_boxplot(fill = "grey") +
  ggtitle("Box Plot of Political Pressures and Controls on Media Content Index") +
  ylab("Political Pressures and Controls on Media Content Index")
```

![](activity03_files/figure-gfm/distrubution-plots-3.png)<!-- -->

![check-in](../README-img/noun-magnifying-glass.png) **Check in**

Review how you described this model in Activity 2. - What were your
parameter estimates (i.e., the $\beta$s)? How did you interpret these
and what did they imply for this scenario? - How good of a fit was this
model? What did you use to assess this?

Answer: Using the `tidy` output, I got the parameter estimates. and this
was the parameter estimates
$\hat{\texttt{pf\score}} = 4.28 + 0.54 \times \texttt{pf\_expression\_control}$
and the model was quite good, as it showed the strong positive linear
relationship, To assess I used $R^2$ (the coefficient of determination),
Linearity & Constant variability.

For this activity, we will begin using the two other quantitative
variables to describe the patterns in the response variable. Take a
moment to think about what this previous sentence means:

- What does this mean from a statistical point of view? Answer: Using
  two other quantitative variables to describe the patterns in the
  response variable means incorporating additional predictors into your
  analysis to better understand and model the relationship with the
  response variable.

- What does this mean from a “real world” point of view (i.e., for your
  data’s situation)? Answer: This means a more deeper understanding of
  how various factors contribute to personal freedom scores

Now, we will obtain graphical and numerical summaries to describe the
pairwise relationships.

- In the code chunk below titled `pairs-plot`, replace “verbatim” with
  “r” just before the code chunk title.
- Replace `explanatory` in the `select` line with the variable you
  identified above
- Run your code chunk or knit your document.

``` r
hfi_2016 %>% 
  select(pf_score, pf_expression_control, pf_rank) %>% 
  ggpairs()
```

![](activity03_files/figure-gfm/pairs-plot-1.png)<!-- -->

Note that a warning message (really a list of warning messages) might
display in your **Console** and likely under your R code chunk when you
knit this report. In R, warning messages are not necessarily a bad thing
and you should read these to make sure you understand what it is
informing you of. To suppress warning messages from displaying after
this specific R code chunk when you knit your report, add the follow
inside the curly brackets (`{r }`) at the top of your R code chunk
(notice the preceding comma): `, warning=FALSE`.

Somewhat related… If you do not want all the messages `{tidyverse}` and
`{tidymodels}` produce when you load them, you can add `, message=FALSE`
to your `load-packages` R code chunk.

After running the `pairs-plot` code, answer the following questions:

1.  For each pair of variables, how would you describe the relationship
    graphically? Do any of the relationships look linear? Are there any
    interesting/odd features (outliers, non-linear patterns, etc.)?

Answer: pf_score vs. pf_expression_control: The relationship appears
linear, with a positive correlation. As pf_expression_control increases,
pf_score also increases. No outliers are visible, and the pattern seems
consistent. pf_score vs. pf_rank: The relationship is strongly negative,
which makes sense since a higher rank (a larger number) indicates a
lower level of freedom. This relationship is also linear.
pf_expression_control vs. pf_rank: Its a negative relationship. As
pf_expression_control increases, indicating more control and thus less
freedom, pf_rank increases as well.

2.  For each pair of variables, how would you describe the relationship
    numerically?

Answer: pf_score and pf_expression_control has corr = 0.845 which a
strong positive linear relationship. pf_score and pf_rank has corr =
-0.977 which is a strong negative linear relationship.
pf_expression_control and pf_rank has corr = -0.855 which is a strong
negative linear relationship.

3.  Are your two explanatory variables collinear (correlated)?
    Essentially, this means that adding more than one of these variables
    to the model would not add much value to the model. We will talk
    more on this issue in Activity 4 (other considerations in regression
    models).

Answer: They appear to be collinear with the response variable
(pf_score). This indicates that they might not provide independent
information in a predictive model for pf_score.

## The multiple linear regression model

You will now fit the following model:

$$
y = \beta_0 + \beta_1 \times x_1 + \beta_2 \times x_2 + \varepsilon
$$

- In the code chunk below titled `mlr-model`, replace “verbatim” with
  “r” just before the code chunk title.
- Replace `explanatory`, similarly to what you did in your `pairs-plot`
  R code chunk.
- Run your code chunk or knit your document.

``` r
#fit the mlr model
lm_spec <- linear_reg() %>%
  set_mode("regression") %>%
  set_engine("lm")

lm_spec
```

    ## Linear Regression Model Specification (regression)
    ## 
    ## Computational engine: lm

``` r
mlr_mod <- lm_spec %>% 
fit(pf_score ~ pf_expression_control + pf_rank, data = hfi_2016)

# model output
tidy(mlr_mod)
```

    ## # A tibble: 3 × 5
    ##   term                  estimate std.error statistic   p.value
    ##   <chr>                    <dbl>     <dbl>     <dbl>     <dbl>
    ## 1 (Intercept)             9.31     0.184       50.7  4.01e-100
    ## 2 pf_expression_control   0.0240   0.0209       1.15 2.53e-  1
    ## 3 pf_rank                -0.0300   0.00104    -29.0  2.57e- 65

After doing this, answer the following questions:

4.  Using your output, write the complete estimated equation for this
    model. Remember in Activity 2 that this looked like:

$$
\hat{y} = b_0 + b_1 \times x_1 
$$

where $b_0$ and $b_1$ were your model parameter estimates. Note that
your model here will be different (and have more terms).

$$
\hat{y} = 9.31111200 + 0.02401278 \times pf_expression_control -0.03001381\times pf_rank
$$

5.  For each of the estimated parameters (the *y*-intercept and the
    slopes associated with each explanatory variable - three total),
    interpret these values in the context of this problem. That is, what
    do they mean for a “non-data” person?

Answer: - **Intercept (9.31111200)**: the personal freedom score would
be around 9.31. This number sets the baseline for our scores.

- **Slope for pf_expression_control (0.02401278)**: For each additional
  point in the control of media content (indicating more restrictions),
  the personal freedom score tends to increase only slightly, by about
  0.024 points.

- **Slope for pf_rank (-0.03001381)**: For each step down in rank (where
  a higher rank number means worse personal freedom), the personal
  freedom score decreases by 0.03. This means that countries with higher
  ranks (worse standing in personal freedom) have notably lower personal
  freedom scores.

## Challenge: 3-D plots

In *ISL*, the authors provided a 3-D scatterplot with a plane that
represents the estimated model. Do some internet sleuthing to minimally
produce a 3-D scatterplot (you do not need to include the plane).
Ideally, this would be something that plays nicely with (looks similar
to) `{ggplot2}`.

- Create a new R code chunk, with a descriptive name, and add your code
  to create this plot.

``` r
scatterplot3d(hfi_2016$pf_expression_control, hfi_2016$pf_rank, hfi_2016$pf_score, main="3D Scatterplot", xlab="Expression Control", ylab="Rank", zlab="Score")
```

![](activity03_files/figure-gfm/3-D%20plot-1.png)<!-- -->

After doing this, respond to the following prompt:

6.  Compare your 3-D scatterplot and the `GGally::ggpairs` output.
    Comment on the strengths and weaknesses of these two visualizations.
    Do both display on GitHub when you push your work there?

Answer: One challenge is that it is hard to interpret in 3d whereas in
2d it was easy to interpret. The disadvantage for ggpairs is that it
does not capture the three-variable interactions in the same way a 3-D
plot does.

# Day 2

During Day 1, you fit a model with one quantitative response variable
and two quantitative explanatory variables. Now we look at a model with
one quantitative explanatory variable and one qualitative explanatory
variable. We will use the full 2016 dataset for this entire activity.
For the Mini-Competition next week, you will be instructed to use the
train/test split process.

## Fitting the overall model

This is similar to what we have already been doing - fitting our desired
model. For today’s activity, we will fit something like:

$$
y = \beta_0 + \beta_1 \times \text{qualitative\\_variable} + \beta_2 \times \text{quantitative\\_variable} + \varepsilon
$$

where $y$, $\text{qualitative\\_variable}$, and
$\text{quantitative\\_variable}$ are from `hfi_2016`. Note that the two
explanatory variables can be entered in whatever order.

To help with interpretability, we will focus on qualitative predictor
variables with only two levels. Unfortunately, none of the current `chr`
variables have only two levels. Fortunately, we can create our own.

- In the code chunk below titled `binary-pred`, replace “verbatim” with
  “r” just before the code chunk title.
- Run your code chunk or knit your document.

``` r
hfi_2016 <- hfi_2016 %>%
  mutate(west_atlantic = if_else(
    region %in% c("North America", "Latin America & the Caribbean"),
    "No",
    "Yes"
  ))
```

7.  What is happening in the above code? What new variable did we
    create? How do you know it is new? What values does it take when?

Answer: A new variable called west_atlantic is created within the
hfi_2016 dataframe. This variable is new to the dataset and is defined
based on the region variable. It assigns the value “No” if a country is
in either “North America” or “Latin America & the Caribbean”, and “Yes”
otherwise

- In the code chunk below titled `qual-mlr`, replace “verbatim” with “r”
  just before the code chunk title.
- Run your code chunk or knit your document.

``` r
# review any visual patterns
hfi_2016 %>% 
  select(pf_score, west_atlantic, pf_expression_control) %>% 
  ggpairs()
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.
    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

![](activity03_files/figure-gfm/qual-mlr-1.png)<!-- -->

``` r
#fit the mlr model
lm_spec <- linear_reg() %>%
  set_mode("regression") %>%
  set_engine("lm")

qual_mod <- lm_spec %>% 
  fit(pf_score ~ west_atlantic + pf_expression_control, data = hfi_2016)

# model output
tidy(qual_mod)
```

    ## # A tibble: 3 × 5
    ##   term                  estimate std.error statistic  p.value
    ##   <chr>                    <dbl>     <dbl>     <dbl>    <dbl>
    ## 1 (Intercept)              4.38     0.213     20.5   1.57e-46
    ## 2 west_atlanticYes        -0.102    0.167     -0.612 5.41e- 1
    ## 3 pf_expression_control    0.540    0.0273    19.8   1.01e-44

When looking at your `ggpairs` output, remember to ask yourself, “does
it make sense to include all of these variables?” Specifically, if you
notice that the response variables are highly correlated (collinear),
including both does not necessarily add much value as they are
essentially saying the same thing. Note: There are more advanced methods
to include the variability within a rater for our model - this is beyond
STA 631. If this sounds of interest to you, explore *generalized
estimating equations* (GEE) or *generalized linear mixed models* (GLMM).
However, there are often times when we choose to include variables in
our model because it is important to us - for various reasons.
Regardless, I encourage you to keep your readings of *DF* in mind - who
will benefit by including this information; who will be hurt by
including this information?

Also, when looking at your model (`tidy`) output, the `term` label for
your qualitative explanatory variable look odd. Answer the following
questions:

8.  What is the label that R assigned to this explanatory variable
    `term`?

Answer: west_atlanticYes, This is because R automatically creates dummy
variables for qualitative data and uses one of the levels as the
reference (in this case, “No”) and the other level is labelled with the
variable name followed by the level name (here, “Yes”).

9.  What information is represented here?

Answer: The west_atlanticYes coefficient represents the difference in
the average pf_score between the reference group (countries not in
“North America” or “Latin America & the Caribbean”, labelled as “No”)
and the “Yes” group (countries in “North America” or “Latin America &
the Caribbean”).

10. What information is missing here?

Answer: By default, R takes the first level alphabetically as the
reference level. Therefore, the coefficient for west_atlanticYes shows
the effect relative to the reference level, which is west_atlanticNo,
but this is not explicitly shown in the output.

Your are essentially fitting two models (or $k$ models, where $k$ is the
number of levels in your qualitative variable). From your reading, you
learned that R is creating an indicator variable (see p. 83). If you
have 3 levels in your qualitative variable, you would have 2 (3 - 1)
indicator variables. If you have $k$ levels in your qualitative
variable, you would have $k - 1$ indicator variables.

The decision for R to call the indicator variable by one of your levels
instead of the other has no deeper meaning. R simply codes the level
that comes first alphabetically with a $0$ for your indicator variable.
You can change this reference level of a categorical variable, which is
the level that is coded as a 0, using the `relevel` function. Use
`?relevel` to learn more.

11. Write the estimated equation for your MLR model with a qualitative
    explanatory variable.

Answer: pf_score = 4.3771413 + ( −0.1024089 × west_atlanticYes ) +
0.5401164 × pf_expression_control

12. Now, for each level of your qualitative variable, write the
    simplified equation of the estimated line for that level. Note that
    if your qualitative variable has two levels, you should have two
    simplified equations.

Answer: pf_score(No) = 4.3771413+ 0.5401164 × pf_expression_control

pf_score (Yes) = (4.3771413−0.1024089)+ 0.5401164 ×
pf_expression_control pf_score (Yes) = 4.2747324 + 0.5401164 ×
pf_expression_control

The interpretation of the coefficients (parameter estimates) in multiple
regression is slightly different from that of simple regression. The
estimate for the indicator variable reflects how much more a group is
expected to be if something has that quality, *while holding all other
variables constant*. The estimate for the quantitative variable reflects
how much change in the response variable occurs due to a 1-unit increase
in the quantitative variable, *while holding all other variables
constant*.

13. Interpret the parameter estimate for the reference level of your
    categorical variable in the context of your problem. Page 83 of the
    text can help here (or have me come chat with you).

Answer:

14. Interpret the parameter estimate for your quantitative variable in
    the context of your problem.

Answer: The parameter estimate for the quantitative variable
pf_expression_control in the context of the model is 0.5401164. This
estimate indicates that, holding the qualitative variable west_atlantic
constant, a one-unit increase in pf_expression_control is associated
with an increase of 0.5401164 in the pf_score.

## Challenge: Multiple levels

Below, create a new R code chunk (with a descriptive name) that fits a
new model with the same response (`pf_score`) and quantitative
explanatory variable (`pf_expression_control`), but now use a
qualitative variable with more than two levels (say, `region`) and
obtain the `tidy` model output. How does R appear to handle categorical
variables with more than two levels?

``` r
model <- lm(pf_score ~ region + pf_expression_control, data = hfi_2016)

model_tidy <- tidy(model)
print(model_tidy)
```

    ## # A tibble: 11 × 5
    ##    term                                estimate std.error statistic  p.value
    ##    <chr>                                  <dbl>     <dbl>     <dbl>    <dbl>
    ##  1 (Intercept)                            5.39     0.272     19.8   7.47e-44
    ##  2 regionEast Asia                        0.496    0.380      1.31  1.93e- 1
    ##  3 regionEastern Europe                   0.326    0.309      1.06  2.93e- 1
    ##  4 regionLatin America & the Caribbean   -0.229    0.300     -0.762 4.47e- 1
    ##  5 regionMiddle East & North Africa      -1.39     0.299     -4.64  7.40e- 6
    ##  6 regionNorth America                    0.610    0.542      1.13  2.62e- 1
    ##  7 regionOceania                          0.233    0.433      0.537 5.92e- 1
    ##  8 regionSouth Asia                      -0.716    0.305     -2.35  2.02e- 2
    ##  9 regionSub-Saharan Africa              -0.746    0.283     -2.64  9.24e- 3
    ## 10 regionWestern Europe                   0.522    0.345      1.52  1.32e- 1
    ## 11 pf_expression_control                  0.387    0.0299    12.9   2.99e-26

Answer: R handles categorical variables with more than two levels by
creating a series of dummy variables.R named each term as region
followed by the specific region name (except for the reference level
which is included in the intercept). For each of these region terms,
there is an associated estimate which quantifies the difference in the
pf_score from the reference level for that particular region.

# Day 3

We will explore a MLR model with an interaction between quantitative and
qualitative explanatory variables as well as see some other methods to
assess the fit of our model. From the modeling process we came up with
as a class, we will now address the “series of important questions that
we should consider when performing multiple linear regression” (*ISL*
[Section 3.2.2](https://hastie.su.domains/ISLR2/ISLRv2_website.pdf),
p. 75):

1.  Is at least one of the $p$ predictors $X_1$, $X_2$, $\ldots$, $X_p$
    useful in predicting the response $Y$?
2.  Do all the predictors help to explain $Y$, or is only a subset of
    the predictors useful?
3.  How well does the model fit the data?
4.  Given a set of predictor values, what response value should we
    predict and how accurate is our prediction?

Note that the text (*ISLR*) covers interactions between two quantitative
explanatory variables as well. By including an interaction term in our
model, it may seem like we are relaxing the “additive assumption” a
little. However, the additive assumption is about the coefficients (the
$\beta$s) and not the variables.

## Fitting the overall model with $qualitative \times quantitative$ interaction

Recall from Day 2 that you explored the model:

$$
y = \beta_0 + \beta_1 \times \text{qualitative\\_variable} + \beta_2 \times \text{quantitative\\_variable} + \varepsilon
$$

Today we will explore a similar model, except that also includes the
interaction between your qualitative and quantitative explanatory
variables. That is,

$$
y = \beta_0 + \beta_1 \times \text{qualitative\\_variable} + \beta_2 \times \text{quantitative\\_variable} + \beta_3 \times ( \text{qualitative\\_variable} \times \text{quantitative\\_variable}) + \varepsilon
$$

- Run all previous code up to this point - you will need your prior
  dataset of just 2016 observations with the `west_atlantic` variable.
- In the code chunk below titled `int-mlr`, replace “verbatim” with “r”
  just before the code chunk title.
- Run your code chunk or knit your document.

``` default
# review any visual patterns
hfi_2016 %>% 
  select(pf_score, west_atlantic, pf_expression_control) %>% 
  ggpairs()

#fit the mlr model
lm_spec <- linear_reg() %>%
  set_mode("regression") %>%
  set_engine("lm")

int_mod <- lm_spec %>% 
  fit(pf_score ~ west_atlantic * pf_expression_control, data = hfi_2016)

# model output
tidy(int_mod)
```

Note that I shortened the model statement using
`qualitative * quantitative`, but this can sometimes be confusing to
read. Another way to write the right-hand side of the equation is:
`qualitative + quantitative + qualitative * quantitative`.

After doing this, answer the following question:

15. When viewing the `tidy` output, notice that the interaction term is
    listed as `qualitativelevel:quantitative`. Referring back to Day 2
    with how R displays qualitative variables, interpret what this
    syntax means.

16. Using page 100 of *ISLR* as a reference, if needed, and your work
    from Day 2, write the simplified equation of the line corresponding
    to each level of your qualitative explanatory variable.

17. For two observations with similar values of the quantitative , which
    level tends to have higher values of the response variable?

18. Like you did in Day 1, assess the fit of this model (no need to do
    any formal hypothesis testing - we will explore this next). How does
    `int_mod`’s fit compare to `mlr_mod`? What did you use to compare
    these? Why?

Recall our brief discussion on how many disciplines are moving away from
$p$-values in favor of other methods. We will explore $p$-values these
other methods later this semester, but we will practice our classical
methods here. This is known as an “overall $F$ test” and the hypotheses
are:

That (the null) no predictors are useful for the model (i.e., all slopes
are equal to zero) versus the alternative that at least one predictor is
useful for the model (i.e., at least one slope is not zero). One way to
check this is to build our null model (no predictors) and then compare
this to our candidate model (`int_mod`).

- In the code chunk below titled `mod-comp`, replace “verbatim” with “r”
  just before the code chunk title.

``` default
# null model
null_mod <- lm_spec %>% 
fit(response ~ 1, data = data)

anova(
  extract_fit_engine(int_mod),
  extract_fit_engine(null_mod)
)
```

19. Using your background knowledge of $F$ tests, what is the $F$ test
    statistic and $p$-value for this test? Based on an $\alpha = 0.05$
    significant level, what should you conclude?

## Partial slope test - do all predictors help explain $y$?

Assuming that your overall model is significant (at least one predictor
is useful), we will continue on. Continue through these next tasks even
if your overall model was not significant.

We could do a similar process to fit a new model while removing one
explanatory variable at at time, and using `anova` to compare these
models. However, the `tidy` output also helps here (the `statistic` and
`p.value` columns).

For each slope, you are testing if that slope is zero (when including
the other variables, the null) or if it is not zero (when including the
other variables, the alternative). Because the interaction term is a
combination of the other two variables, we should assess the first.

20. What is the $t$ test statistic and $p$-value associated with this
    test? Based on an $\alpha = 0.05$ significant level, what should you
    conclude?

If your interaction term was not significant, you could consider
removing it. Now look at your two non-interaction terms…

21. What are the $t$ test statistic and $p$-value associated with these
    tests? Based on an $\alpha = 0.05$ significant level, what should
    you conclude about these two predictors?

You would not need to do (21) if the interaction was significant. You
also should not remove a main variable (non-interaction variable) if the
interaction variable remains in your model.

## Residual assessment - how well does the model fit the data?

You have already done this step in past activities by exploring your
residuals (Activity 2). Using your final model from Task 3, assess how
well your model fits the data.
