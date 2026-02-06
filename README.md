# SPD test


## Data

I gathered 269 uncalibrated radiocarbon ages from 32 Pliestocene to
early Holocene sites across Southeast Asia and southern China. For one
of the Chinese sites Xiaodong I had to uncalibrate the ages using the
built in feature in rcarbon.

## Methods

First we calibrated all the dates using IntCal20 in rcarbon, then we
made summed probability distributions and visualized it in multiple
ways, first without binning. Next to account for well dated sites
overshadowing sites with less amount of dates we binned the data by 100
years, then 200 years, then 500, and finally 5000. Another method used
to account for inter-site variation is thinning where only 1 date from
each bin is chosen to construct the SPD with. A new method used to plot
was composite kernel density estimates where random calendar dates were
sampled from each calibrated date to generate a kernel density estimate,
its done multiple times which all are visualized as an envelope.

``` r
library(tidyverse)
```

    Warning: package 'ggplot2' was built under R version 4.5.2

    Warning: package 'tibble' was built under R version 4.5.2

    Warning: package 'tidyr' was built under R version 4.5.2

    Warning: package 'readr' was built under R version 4.5.2

    Warning: package 'purrr' was built under R version 4.5.2

    ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ✔ dplyr     1.1.4     ✔ readr     2.1.6
    ✔ forcats   1.0.1     ✔ stringr   1.6.0
    ✔ ggplot2   4.0.1     ✔ tibble    3.3.1
    ✔ lubridate 1.9.4     ✔ tidyr     1.3.2
    ✔ purrr     1.2.1     
    ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ✖ dplyr::filter() masks stats::filter()
    ✖ dplyr::lag()    masks stats::lag()
    ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

``` r
library(readxl)
library(rcarbon)
```


    Attaching package: 'rcarbon'

    The following object is masked from 'package:dplyr':

        combine

``` r
# Read excel sheet with dates across Southeast Asia and southern China
dates <- read_excel("Demographic analysis sites.xlsx")

# Calibrate dates using IntCal20
calDates <- calibrate(
  x = dates$C14Age,
  errors = dates$C14SD,
  calCurves = "intcal20",
  verbose = FALSE)
```

## Results

The resulting plots are relatively consistent in peaks and show that
practically every period is accounted for. The consistent increase in
dates after 14,000 BP do call for some investigation which is our
current focus.

``` r
age_range <- c(50000,2000)

# Plotting the summed probability distribution
spd_unbinned <- spd(
  calDates,
  timeRange = age_range)
```

    [1] "Extracting and aggregating..."
    [1] "Done."

``` r
plot(spd_unbinned)

# with ggplot2
ggplot(spd_unbinned$grid) +
  aes(calBP, PrDens) +
  geom_line() +
  annotate("rect",
           ymin = 0, ymax = Inf,
           xmin = 26000, xmax = 19000,  # df$start[1], df$end[1]
           colour = "red",
           fill = "red",
           alpha = 0.2) +
    annotate("text",
           x = 22000, # midpoint of start and end
           y = 0.09,
           label = "LGM") + # df$event_name[1]
  scale_x_reverse() +
  theme_minimal() 
```

![](README_files/figure-commonmark/fig-unbinned-1.png)

![](README_files/figure-commonmark/fig-unbinned-2.png)

In **?@fig-unbinned** there is a continuous occupation of Southeast Asia
from 25000 BP to present although there is a more consistent increase in
height starting from 14 ka.

``` r
# Grouping dates within 100 years of each other into bins
bins_100 <- binPrep(
  sites = dates$Site,
  ages = dates$C14Age,
  h = 100
)

# Plotting the summed probability distribution with new bins
spd_res <- spd(
  calDates, 
  bins = bins_100,
  timeRange = age_range,
  verbose = FALSE)

plot(spd_res)
```

![](README_files/figure-commonmark/fig-100-bin-1.png)

In <a href="#fig-100-bin" class="quarto-xref">Figure 3</a> the peaks are
consistent but grow more intense the only exceptions being the hills
just before 15 ka and just after 10 ka are a little depressed from the
binning compared to the rest of the peaks.

``` r
# Grouping dates within 200 years of each other into bins
bins_200 <- binPrep(
  sites = dates$Site,
  ages = dates$C14Age,
  h = 200
)

# Plotting the summed probability distribution with new bins
spd_res <- spd(
  calDates, 
  bins = bins_200,
  timeRange = age_range,
  verbose = FALSE)

plot(spd_res)
```

![](README_files/figure-commonmark/fig-200-bin-1.png)

In <a href="#fig-200-bin" class="quarto-xref">Figure 4</a> the changes
are more noticeable as the peak around 13 ka and just before 10 ka is
much higher than the original plot.

``` r
# Grouping dates within 500 years of each other into bins
bins_500 <- binPrep(
  sites = dates$Site,
  ages = dates$C14Age,
  h = 500
)

# Plotting the summed probability distribution with new bins
spd_res <- spd(
  calDates, 
  bins = bins_500,
  timeRange = age_range,
  verbose = FALSE)
plot(spd_res)
```

![](README_files/figure-commonmark/fig-500-bin-1.png)

In <a href="#fig-500-bin" class="quarto-xref">Figure 5</a> the peaks
around 13 ka are even more intense but the hill around 14 ka depresses a
little, the peak just before 10 ka also lowers considerably compared to
the original plot.

``` r
# Grouping dates within 5000 years of each other into bins
bins_5000 <- binPrep(
  sites = dates$Site,
  ages = dates$C14Age,
  h = 5000
)

# Plotting the summed probability distribution with new bins
spd_res <- spd(
  calDates, 
  bins = bins_5000,
  timeRange = age_range,
  verbose = FALSE)
plot(spd_res)
```

![](README_files/figure-commonmark/fig-5000-bin-1.png)

In <a href="#fig-5000-bin" class="quarto-xref">Figure 6</a> a lot of the
hills depress however a couple of them spike up into their own peaks
especially around 25-23 ka, 17 ka, and 12 ka.

``` r
# Grouping dates within 200 years of each other into bins
bins_200 <- binPrep(
  sites = dates$Site,
  ages = dates$C14Age,
  h = 200
)

# Thinning selects 1 random date from each bin
calDates2 = calDates[thinDates(ages = dates$C14Age,
                               errors = dates$C14SD,
                               bins = bins_200,
                               size = 1,
                               method = 'random')]

spd_thin <- spd(
  calDates2,
  timeRange = age_range
)
```

    [1] "Extracting and aggregating..."
    [1] "Done."

``` r
# Plotting the SPD after thinning
plot(spd_thin)
```

![](README_files/figure-commonmark/fig-thinned-1.png)

In <a href="#fig-thinned" class="quarto-xref">Figure 7</a> the thinning
caused more mild variation like the 100 and 200 year bins but the peaks
around 13 and 12 ka intensify and the one just after 10 ka depresses.

``` r
# Grouping dates within 200 years of each other into bins
bins_200 <- binPrep(
  sites = dates$Site,
  ages = dates$C14Age,
  h = 200
)

# Randomly sampling from each calibrated date to generate a kernel density estimate
ckde_res = sampleDates(calDates,bins=bins_200,nsim=100,verbose=FALSE)

Sea.ckde = ckde(ckde_res,
                timeRange = age_range,
                bw = 200)

#Plotting the CKDE
plot(Sea.ckde, type = 'multiline')
```

![](README_files/figure-commonmark/fig-CKDE-1.png)

In <a href="#fig-CKDE" class="quarto-xref">Figure 8</a> the bands show a
consistent occupation but support the notion of increased intensity
starting from 14 ka.
