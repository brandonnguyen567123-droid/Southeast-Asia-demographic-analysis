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
library(readxl)
library(rcarbon)

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
# Plotting the summed probability distribution
spd_unbinned <- spd(
  calDates,
  timeRange = c(25000, 5000))
```

    [1] "Extracting and aggregating..."
    [1] "Done."

``` r
plot(spd_unbinned)
```

<div id="fig-unbinned">

![](README_files/figure-commonmark/fig-unbinned-1.png)

Figure 1: Unbinned SPD plot

</div>

In <a href="#fig-unbinned" class="quarto-xref">Figure 1</a> there is a
continuous occupation of Southeast Asia from 25000 BP to present
although there is a more consistent increase in height starting from 14
ka.

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
  timeRange = c(25000, 5000),
  verbose = FALSE)
plot(spd_res)
```

<div id="fig-100-bin">

![](README_files/figure-commonmark/fig-100-bin-1.png)

Figure 2: SPD plot with a bin of 100 years

</div>

In <a href="#fig-100-bin" class="quarto-xref">Figure 2</a> the peaks are
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
  timeRange = c(25000, 5000),
  verbose = FALSE)
plot(spd_res)
```

<div id="fig-200-bin">

![](README_files/figure-commonmark/fig-200-bin-1.png)

Figure 3: SPD plot with a bin of 200 years

</div>

In <a href="#fig-200-bin" class="quarto-xref">Figure 3</a> the changes
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
  timeRange = c(25000, 5000),
  verbose = FALSE)
plot(spd_res)
```

<div id="fig-500-bin">

![](README_files/figure-commonmark/fig-500-bin-1.png)

Figure 4: SPD plot with a bin of 500 years

</div>

In <a href="#fig-500-bin" class="quarto-xref">Figure 4</a> the peaks
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
  timeRange = c(25000, 5000),
  verbose = FALSE)
plot(spd_res)
```

<div id="fig-5000-bin">

![](README_files/figure-commonmark/fig-5000-bin-1.png)

Figure 5: SPD plot with a bin of 5000 years

</div>

In <a href="#fig-5000-bin" class="quarto-xref">Figure 5</a> a lot of the
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
  timeRange = c(25000, 5000)
)
```

    [1] "Extracting and aggregating..."
    [1] "Done."

``` r
# Plotting the SPD after thinning
plot(spd_thin)
```

<div id="fig-thinned">

![](README_files/figure-commonmark/fig-thinned-1.png)

Figure 6: SPD plot with thinning

</div>

In <a href="#fig-thinned" class="quarto-xref">Figure 6</a> the thinning
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
                timeRange = c(25000, 5000),
                bw = 200)
#Plotting the CKDE
plot(Sea.ckde, type = 'multiline')
```

<div id="fig-CKDE">

![](README_files/figure-commonmark/fig-CKDE-1.png)

Figure 7: CKDE plot

</div>

In <a href="#fig-CKDE" class="quarto-xref">Figure 7</a> the bands show a
consistent occupation but support the notion of increased intensity
starting from 14 ka.
