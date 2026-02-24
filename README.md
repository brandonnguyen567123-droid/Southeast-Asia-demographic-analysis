# Using radiocarbon ages as a proxy for human population dynamics in
late Pleistocene mainland Southeast Asia


``` r
library(tidyverse)
```

    ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ✔ dplyr     1.2.0     ✔ readr     2.1.6
    ✔ forcats   1.0.1     ✔ stringr   1.6.0
    ✔ ggplot2   4.0.2     ✔ tibble    3.3.1
    ✔ lubridate 1.9.5     ✔ tidyr     1.3.2
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

## Data

I gathered 267 uncalibrated radiocarbon ages from 30 Pliestocene to
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

#| label: fig-unbinned-climate
#| fig-cap: Unbinned SPD plot with climate events overlayed 
```

![](README_files/figure-commonmark/fig-unbinned-1.png)

``` r
climate_events <- read_excel("Demographic analysis sites.xlsx",
                             sheet = 2) |> 
  mutate(midpoint = `End year` + ( `Start year` - `End year`) / 2 )


# with ggplot2
ggplot(spd_unbinned$grid) +
  aes(calBP, PrDens) +
  geom_line() +
  annotate("rect",
           ymin = 0, ymax = Inf,
           xmin = climate_events$`Start year`[1], 
           xmax = climate_events$`End year`[1],  
           colour = "grey50",
           fill = "grey50",
           alpha = 0.1) +
    annotate("text",
           x = climate_events$midpoint[1], # midpoint of start and end
           y = 0.09,
           label = climate_events$Abbreviation[1]) + # df$event_name[1]
    annotate("rect",
           ymin = 0, ymax = 0.06,
           xmin = climate_events$`Start year`[2], 
           xmax = climate_events$`End year`[2],  
           colour = "salmon",
           fill = "salmon",
           alpha = 0.2) +
    annotate("text",
           x = climate_events$midpoint[2], 
           y = 0.055,
           label = climate_events$Abbreviation[2]) + 
    annotate("rect",
           ymin = 0, ymax = 0.03,
           xmin = climate_events$`Start year`[3],
           xmax = climate_events$`End year`[3],  
           colour = "grey40",
           fill = "grey40",
           alpha = 0.2) +
    annotate("text",
           x = climate_events$midpoint[3], 
           y = 0.03,
           label = climate_events$Abbreviation[3]) + 
    annotate("rect",
           ymin = 0, ymax = 0.06,
           xmin = climate_events$`Start year`[4], 
           xmax = climate_events$`End year`[4],  
           colour = "salmon",
           fill = "salmon",
           alpha = 0.2) +
    annotate("text",
           x = climate_events$midpoint[4], 
           y = 0.05,
           label = climate_events$Abbreviation[4]) + 
    annotate("rect",
           ymin = 0, ymax = 0.06,
           xmin = climate_events$`Start year`[5], 
           xmax = climate_events$`End year`[5],  
           colour = "salmon",
           fill = "salmon",
           alpha = 0.2) +
    annotate("text",
           x = climate_events$midpoint[5], 
           y = 0.045,
           label = climate_events$Abbreviation[5]) + 
    annotate("rect",
           ymin = 0, ymax = 0.06,
           xmin = climate_events$`Start year`[6], 
           xmax = climate_events$`End year`[6],  
           colour = "salmon",
           fill = "salmon",
           alpha = 0.2) +
    annotate("text",
           x = climate_events$midpoint[6], 
           y = 0.04,
           label = climate_events$Abbreviation[6]) + 
    annotate("rect",
           ymin = 0, ymax = 0.06,
           xmin = climate_events$`Start year`[7], 
           xmax = climate_events$`End year`[7],  
           colour = "salmon",
           fill = "salmon",
           alpha = 0.2) +
    annotate("text",
           x = climate_events$midpoint[7], 
           y = 0.05,
           label = climate_events$Abbreviation[7]) + 
    annotate("rect",
           ymin = 0, ymax = 0.03,
           xmin = climate_events$`Start year`[8],
           xmax = climate_events$`End year`[8],  
           colour = "grey40",
           fill = "grey40",
           alpha = 0.2) +
    annotate("text",
           x = climate_events$midpoint[8], 
           y = 0.03,
           label = climate_events$Abbreviation[8]) + 
    annotate("rect",
           ymin = 0, ymax = 0.06,
           xmin = climate_events$`Start year`[9], 
           xmax = climate_events$`End year`[9],  
           colour = "salmon",
           fill = "salmon",
           alpha = 0.2) +
    annotate("text",
           x = climate_events$midpoint[9], 
           y = 0.045,
           label = climate_events$Abbreviation[9]) + 
    annotate("rect",
           ymin = 0, ymax = 0.06,
           xmin = climate_events$`Start year`[10], 
           xmax = climate_events$`End year`[10],  
           colour = "salmon",
           fill = "salmon",
           alpha = 0.2) +
    annotate("text",
           x = climate_events$midpoint[10], 
           y = 0.04,
           label = climate_events$Abbreviation[10]) + 
    annotate("rect",
           ymin = 0, ymax = 0.06,
           xmin = climate_events$`Start year`[11], 
           xmax = climate_events$`End year`[11],  
           colour = "salmon",
           fill = "salmon",
           alpha = 0.2) +
    annotate("text",
           x = climate_events$midpoint[11], 
           y = 0.05,
           label = climate_events$Abbreviation[11]) + 
    annotate("rect",
           ymin = 0, ymax = 0.03,
           xmin = climate_events$`Start year`[12],
           xmax = climate_events$`End year`[12],  
           colour = "grey40",
           fill = "grey40",
           alpha = 0.2) +
    annotate("text",
           x = climate_events$midpoint[12], 
           y = 0.03,
           label = climate_events$Abbreviation[12]) + 
  annotate("rect",
           ymin = 0, ymax = Inf,
           xmin = climate_events$`Start year`[13], 
           xmax = climate_events$`End year`[13],  
           colour = "grey50",
           fill = "grey50",
           alpha = 0.1) +
    annotate("text",
           x = climate_events$midpoint[13], 
           y = 0.09,
           label = climate_events$Abbreviation[13]) +
    annotate("rect",
           ymin = 0, ymax = 0.06,
           xmin = climate_events$`Start year`[14], 
           xmax = climate_events$`End year`[14],  
           colour = "salmon",
           fill = "salmon",
           alpha = 0.2) +
    annotate("text",
           x = climate_events$midpoint[14], 
           y = 0.05,
           label = climate_events$Abbreviation[14]) + 
    annotate("rect",
           ymin = 0, ymax = 0.06,
           xmin = climate_events$`Start year`[15], 
           xmax = climate_events$`End year`[15],  
           colour = "salmon",
           fill = "salmon",
           alpha = 0.2) +
    annotate("text",
           x = climate_events$midpoint[15], 
           y = 0.04,
           label = climate_events$Abbreviation[15]) + 
    annotate("rect",
           ymin = 0, ymax = 0.08,
           xmin = climate_events$`Start year`[16], 
           xmax = climate_events$`End year`[16],  
           colour = "skyblue",
           fill = "skyblue",
           alpha = 0.2) +
    annotate("text",
           x = climate_events$midpoint[16], 
           y = 0.07,
           label = climate_events$Abbreviation[16]) + 
  annotate("rect",
           ymin = 0, ymax = 0.03,
           xmin = climate_events$`Start year`[17], 
           xmax = climate_events$`End year`[17],  
           colour = "grey40",
           fill = "grey40",
           alpha = 0.1) +
    annotate("text",
           x = climate_events$midpoint[17], 
           y = 0.03,
           label = climate_events$Abbreviation[17]) +
    annotate("rect",
           ymin = 0, ymax = 0.06,
           xmin = climate_events$`Start year`[18], 
           xmax = climate_events$`End year`[18],  
           colour = "salmon",
           fill = "salmon",
           alpha = 0.2) +
    annotate("text",
           x = climate_events$midpoint[18], 
           y = 0.05,
           label = climate_events$Abbreviation[18]) + 
    annotate("rect",
           ymin = 0, ymax = 0.08,
           xmin = climate_events$`Start year`[19], 
           xmax = climate_events$`End year`[19],  
           colour = "skyblue",
           fill = "skyblue",
           alpha = 0.2) +
    annotate("text",
           x = climate_events$midpoint[19], 
           y = 0.07,
           label = climate_events$Abbreviation[19]) + 
  annotate("rect",
           ymin = 0, ymax = 0.03,
           xmin = climate_events$`Start year`[20], 
           xmax = climate_events$`End year`[20],  
           colour = "grey40",
           fill = "grey40",
           alpha = 0.1) +
    annotate("text",
           x = climate_events$midpoint[20], 
           y = 0.03,
           label = climate_events$Abbreviation[20]) +
    annotate("rect",
           ymin = 0, ymax = 0.08,
           xmin = climate_events$`Start year`[21], 
           xmax = climate_events$`End year`[21],  
           colour = "salmon",
           fill = "salmon",
           alpha = 0.2) +
    annotate("text",
           x = climate_events$midpoint[21], 
           y = 0.07,
           label = climate_events$Abbreviation[21]) + 
    annotate("rect",
           ymin = 0, ymax = 0.08,
           xmin = climate_events$`Start year`[22], 
           xmax = climate_events$`End year`[22],  
           colour = "skyblue",
           fill = "skyblue",
           alpha = 0.2) +
    annotate("text",
           x = climate_events$midpoint[22], 
           y = 0.065,
           label = climate_events$Abbreviation[22]) + 
  annotate("rect",
           ymin = 0, ymax = Inf,
           xmin = climate_events$`Start year`[23], 
           xmax = climate_events$`End year`[23],  
           colour = "grey50",
           fill = "grey50",
           alpha = 0.1) +
    annotate("text",
           x = climate_events$midpoint[23], 
           y = 0.09,
           label = climate_events$Abbreviation[23]) +
    annotate("rect",
           ymin = 0, ymax = 0.08,
           xmin = climate_events$`Start year`[24], 
           xmax = climate_events$`End year`[24],  
           colour = "skyblue",
           fill = "skyblue",
           alpha = 0.2) +
    annotate("text",
           x = climate_events$midpoint[24], 
           y = 0.07,
           label = "8.2") + 
  scale_x_reverse() +
  theme_minimal() 
```

![](README_files/figure-commonmark/fig-unbinned-climate-1.png)

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

``` r
ggplot(spd_res$grid) +
  aes(calBP, PrDens) +
  geom_line() +
  annotate("rect",
           ymin = 0, ymax = Inf,
           xmin = climate_events$`Start year`[1], 
           xmax = climate_events$`End year`[1],  
           colour = "grey50",
           fill = "grey50",
           alpha = 0.1) +
    annotate("text",
           x = climate_events$midpoint[1], # midpoint of start and end
           y = 0.09,
           label = climate_events$Abbreviation[1]) + # df$event_name[1]
    annotate("rect",
           ymin = 0, ymax = 0.06,
           xmin = climate_events$`Start year`[2], 
           xmax = climate_events$`End year`[2],  
           colour = "salmon",
           fill = "salmon",
           alpha = 0.2) +
    annotate("text",
           x = climate_events$midpoint[2], 
           y = 0.055,
           label = climate_events$Abbreviation[2]) + 
    annotate("rect",
           ymin = 0, ymax = 0.03,
           xmin = climate_events$`Start year`[3],
           xmax = climate_events$`End year`[3],  
           colour = "grey40",
           fill = "grey40",
           alpha = 0.2) +
    annotate("text",
           x = climate_events$midpoint[3], 
           y = 0.03,
           label = climate_events$Abbreviation[3]) + 
    annotate("rect",
           ymin = 0, ymax = 0.06,
           xmin = climate_events$`Start year`[4], 
           xmax = climate_events$`End year`[4],  
           colour = "salmon",
           fill = "salmon",
           alpha = 0.2) +
    annotate("text",
           x = climate_events$midpoint[4], 
           y = 0.05,
           label = climate_events$Abbreviation[4]) + 
    annotate("rect",
           ymin = 0, ymax = 0.06,
           xmin = climate_events$`Start year`[5], 
           xmax = climate_events$`End year`[5],  
           colour = "salmon",
           fill = "salmon",
           alpha = 0.2) +
    annotate("text",
           x = climate_events$midpoint[5], 
           y = 0.045,
           label = climate_events$Abbreviation[5]) + 
    annotate("rect",
           ymin = 0, ymax = 0.06,
           xmin = climate_events$`Start year`[6], 
           xmax = climate_events$`End year`[6],  
           colour = "salmon",
           fill = "salmon",
           alpha = 0.2) +
    annotate("text",
           x = climate_events$midpoint[6], 
           y = 0.04,
           label = climate_events$Abbreviation[6]) + 
    annotate("rect",
           ymin = 0, ymax = 0.06,
           xmin = climate_events$`Start year`[7], 
           xmax = climate_events$`End year`[7],  
           colour = "salmon",
           fill = "salmon",
           alpha = 0.2) +
    annotate("text",
           x = climate_events$midpoint[7], 
           y = 0.05,
           label = climate_events$Abbreviation[7]) + 
    annotate("rect",
           ymin = 0, ymax = 0.03,
           xmin = climate_events$`Start year`[8],
           xmax = climate_events$`End year`[8],  
           colour = "grey40",
           fill = "grey40",
           alpha = 0.2) +
    annotate("text",
           x = climate_events$midpoint[8], 
           y = 0.03,
           label = climate_events$Abbreviation[8]) + 
    annotate("rect",
           ymin = 0, ymax = 0.06,
           xmin = climate_events$`Start year`[9], 
           xmax = climate_events$`End year`[9],  
           colour = "salmon",
           fill = "salmon",
           alpha = 0.2) +
    annotate("text",
           x = climate_events$midpoint[9], 
           y = 0.045,
           label = climate_events$Abbreviation[9]) + 
    annotate("rect",
           ymin = 0, ymax = 0.06,
           xmin = climate_events$`Start year`[10], 
           xmax = climate_events$`End year`[10],  
           colour = "salmon",
           fill = "salmon",
           alpha = 0.2) +
    annotate("text",
           x = climate_events$midpoint[10], 
           y = 0.04,
           label = climate_events$Abbreviation[10]) + 
    annotate("rect",
           ymin = 0, ymax = 0.06,
           xmin = climate_events$`Start year`[11], 
           xmax = climate_events$`End year`[11],  
           colour = "salmon",
           fill = "salmon",
           alpha = 0.2) +
    annotate("text",
           x = climate_events$midpoint[11], 
           y = 0.05,
           label = climate_events$Abbreviation[11]) + 
    annotate("rect",
           ymin = 0, ymax = 0.03,
           xmin = climate_events$`Start year`[12],
           xmax = climate_events$`End year`[12],  
           colour = "grey40",
           fill = "grey40",
           alpha = 0.2) +
    annotate("text",
           x = climate_events$midpoint[12], 
           y = 0.03,
           label = climate_events$Abbreviation[12]) + 
  annotate("rect",
           ymin = 0, ymax = Inf,
           xmin = climate_events$`Start year`[13], 
           xmax = climate_events$`End year`[13],  
           colour = "grey50",
           fill = "grey50",
           alpha = 0.1) +
    annotate("text",
           x = climate_events$midpoint[13], 
           y = 0.09,
           label = climate_events$Abbreviation[13]) +
    annotate("rect",
           ymin = 0, ymax = 0.06,
           xmin = climate_events$`Start year`[14], 
           xmax = climate_events$`End year`[14],  
           colour = "salmon",
           fill = "salmon",
           alpha = 0.2) +
    annotate("text",
           x = climate_events$midpoint[14], 
           y = 0.05,
           label = climate_events$Abbreviation[14]) + 
    annotate("rect",
           ymin = 0, ymax = 0.06,
           xmin = climate_events$`Start year`[15], 
           xmax = climate_events$`End year`[15],  
           colour = "salmon",
           fill = "salmon",
           alpha = 0.2) +
    annotate("text",
           x = climate_events$midpoint[15], 
           y = 0.04,
           label = climate_events$Abbreviation[15]) + 
    annotate("rect",
           ymin = 0, ymax = 0.08,
           xmin = climate_events$`Start year`[16], 
           xmax = climate_events$`End year`[16],  
           colour = "skyblue",
           fill = "skyblue",
           alpha = 0.2) +
    annotate("text",
           x = climate_events$midpoint[16], 
           y = 0.07,
           label = climate_events$Abbreviation[16]) + 
  annotate("rect",
           ymin = 0, ymax = 0.03,
           xmin = climate_events$`Start year`[17], 
           xmax = climate_events$`End year`[17],  
           colour = "grey40",
           fill = "grey40",
           alpha = 0.1) +
    annotate("text",
           x = climate_events$midpoint[17], 
           y = 0.03,
           label = climate_events$Abbreviation[17]) +
    annotate("rect",
           ymin = 0, ymax = 0.06,
           xmin = climate_events$`Start year`[18], 
           xmax = climate_events$`End year`[18],  
           colour = "salmon",
           fill = "salmon",
           alpha = 0.2) +
    annotate("text",
           x = climate_events$midpoint[18], 
           y = 0.05,
           label = climate_events$Abbreviation[18]) + 
    annotate("rect",
           ymin = 0, ymax = 0.08,
           xmin = climate_events$`Start year`[19], 
           xmax = climate_events$`End year`[19],  
           colour = "skyblue",
           fill = "skyblue",
           alpha = 0.2) +
    annotate("text",
           x = climate_events$midpoint[19], 
           y = 0.07,
           label = climate_events$Abbreviation[19]) + 
  annotate("rect",
           ymin = 0, ymax = 0.03,
           xmin = climate_events$`Start year`[20], 
           xmax = climate_events$`End year`[20],  
           colour = "grey40",
           fill = "grey40",
           alpha = 0.1) +
    annotate("text",
           x = climate_events$midpoint[20], 
           y = 0.03,
           label = climate_events$Abbreviation[20]) +
    annotate("rect",
           ymin = 0, ymax = 0.08,
           xmin = climate_events$`Start year`[21], 
           xmax = climate_events$`End year`[21],  
           colour = "salmon",
           fill = "salmon",
           alpha = 0.2) +
    annotate("text",
           x = climate_events$midpoint[21], 
           y = 0.07,
           label = climate_events$Abbreviation[21]) + 
    annotate("rect",
           ymin = 0, ymax = 0.08,
           xmin = climate_events$`Start year`[22], 
           xmax = climate_events$`End year`[22],  
           colour = "skyblue",
           fill = "skyblue",
           alpha = 0.2) +
    annotate("text",
           x = climate_events$midpoint[22], 
           y = 0.065,
           label = climate_events$Abbreviation[22]) + 
  annotate("rect",
           ymin = 0, ymax = Inf,
           xmin = climate_events$`Start year`[23], 
           xmax = climate_events$`End year`[23],  
           colour = "grey50",
           fill = "grey50",
           alpha = 0.1) +
    annotate("text",
           x = climate_events$midpoint[23], 
           y = 0.09,
           label = climate_events$Abbreviation[23]) +
    annotate("rect",
           ymin = 0, ymax = 0.08,
           xmin = climate_events$`Start year`[24], 
           xmax = climate_events$`End year`[24],  
           colour = "skyblue",
           fill = "skyblue",
           alpha = 0.2) +
    annotate("text",
           x = climate_events$midpoint[24], 
           y = 0.07,
           label = "8.2") + 
  scale_x_reverse() +
  theme_minimal() 
```

![](README_files/figure-commonmark/fig-200-bin-climate-1.png)

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

In <a href="#fig-500-bin" class="quarto-xref">Figure 6</a> the peaks
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

In <a href="#fig-5000-bin" class="quarto-xref">Figure 7</a> a lot of the
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

In <a href="#fig-thinned" class="quarto-xref">Figure 8</a> the thinning
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

In <a href="#fig-CKDE" class="quarto-xref">Figure 9</a> the bands show a
consistent occupation but support the notion of increased intensity
starting from 14 ka.

``` r
library(tidyverse)
library(readxl)
library(rnaturalearth)
library(rnaturalearthdata)
```


    Attaching package: 'rnaturalearthdata'

    The following object is masked from 'package:rnaturalearth':

        countries110

``` r
library(sf)
```

    Linking to GEOS 3.13.0, GDAL 3.5.3, PROJ 9.5.1; sf_use_s2() is TRUE

``` r
library(ggrepel)

#| label: fig-map
#| fig-cap: Map of early sites in Southeast Asia

# Load site data from Excel file
dates_data <- read_excel("Demographic analysis sites.xlsx")

# Prepare the spatial data by filtering unique sites
sites_sf <- dates_data %>%
  distinct(Site, lat, long) %>%
  st_as_sf(coords = c("long", "lat"), 
           remove = FALSE, 
           crs = 4326)

# Get the map background
world <- ne_countries(scale = "medium", returnclass = "sf")

# Generate the Map, focus on Southeast Asia, and label sites clearly 
sea_map <- ggplot(world) +
  geom_sf() +
  geom_sf(data = sites_sf, size = 2) +
  coord_sf(xlim = c(90, 115), 
           ylim = c(8, 30), 
           expand = FALSE) +
  geom_text_repel(data = sites_sf,
                  aes(x = long, y = lat, label = Site),
                  max.overlaps = Inf,
                  size = 3, 
                  nudge_x = 0.5,
                  force = 10,
                  point.padding = 0.5,
                  box.padding = 0.5,
                  min.segment.length = 0,
                  bg.color = "white",
                  bg.r = 0.15) +
  theme_void() +
  labs(title = "Early sites in Southeast Asia") 

sea_map
```

![](README_files/figure-commonmark/unnamed-chunk-2-1.png)

``` r
# Filter for sites older than 30,000 BP
earliest_sites <- dates_data %>%
  filter(C14Age > 30000) %>%
  distinct(Site, lat, long) %>%
  st_as_sf(coords = c("long", "lat"), 
           remove = FALSE, 
           crs = 4326)

# Base map of Southeast Asia
world <- ne_countries(scale = "medium", returnclass = "sf")

# Plot earliest sites
early_sea_map <-ggplot(world) +
  geom_sf() +
  geom_sf(data = earliest_sites, size = 2) +
  coord_sf(xlim = c(90, 115), 
           ylim = c(8, 30), 
           expand = FALSE) +
  geom_text_repel(data = earliest_sites,
                  aes(x = long, y = lat, label = Site),
                  max.overlaps = Inf,
                  size = 3, 
                  nudge_x = 0.5,
                  force = 10,
                  point.padding = 0.5,
                  box.padding = 0.5,
                  min.segment.length = 0,
                  bg.color = "white",
                  bg.r = 0.15) +
  theme_void() +
  labs(title = "Sites older than 30 ka") 

early_sea_map
```

![Sites older than 30,000
BP](README_files/figure-commonmark/early-fig-map-1.png)

``` r
# Filter for sites between 30,000 and 15,0000 BP
semi_earliest_sites <- dates_data %>%
  filter(C14Age >= 15000 & C14Age <= 30000) %>%
  distinct(Site, lat, long) %>%
  st_as_sf(coords = c("long", "lat"), 
           remove = FALSE, 
           crs = 4326)

# Base map of Southeast Asia
world <- ne_countries(scale = "medium", returnclass = "sf")

# Plot sites
semi_early_sea_map <-ggplot(world) +
  geom_sf() +
  geom_sf(data = semi_earliest_sites, size = 2) +
  coord_sf(xlim = c(90, 115), 
           ylim = c(8, 30), 
           expand = FALSE) +
  geom_text_repel(data = semi_earliest_sites,
                  aes(x = long, y = lat, label = Site),
                  max.overlaps = Inf,
                  size = 3, 
                  nudge_x = 0.5,
                  force = 10,
                  point.padding = 0.5,
                  box.padding = 0.5,
                  min.segment.length = 0,
                  bg.color = "white",
                  bg.r = 0.15) +
  theme_void() +
  labs(title = "Occupation between 30-15 ka") 

semi_early_sea_map
```

![Sites occupied between 30,000-15,000
BP](README_files/figure-commonmark/semi-early-fig-map-1.png)

``` r
# Filter for sites around the 14 ka event
sites_14ka <- dates_data %>%
  filter(C14Age >= 13000 & C14Age <= 15000) %>%
  distinct(Site, lat, long) %>%
  st_as_sf(coords = c("long", "lat"), 
           remove = FALSE, 
           crs = 4326)

# Plot 14 ka sites
sea_map_14ka <-ggplot(world) +
  geom_sf() +
  geom_sf(data = sites_14ka, size = 2) +
  coord_sf(xlim = c(90, 115), 
           ylim = c(8, 30), 
           expand = FALSE) +
  geom_text_repel(data = sites_14ka,
                  aes(x = long, y = lat, label = Site),
                  max.overlaps = Inf,
                  size = 3, 
                  nudge_x = 0.5,
                  force = 10,
                  point.padding = 0.5,
                  box.padding = 0.5,
                  min.segment.length = 0,
                  bg.color = "white",
                  bg.r = 0.15) +
  theme_void() +
  labs(title = "Occupation between 15-13 ka") 

sea_map_14ka
```

![Sites occupied during the 14 ka population
rise](README_files/figure-commonmark/14ka-fig-map-1.png)

``` r
# Filter for sites after the 14 ka event
sites_after_14ka <- dates_data %>%
  filter(C14Age <= 13000) %>%
  distinct(Site, lat, long) %>%
  st_as_sf(coords = c("long", "lat"), 
           remove = FALSE, 
           crs = 4326)

# Plot the after 14 ka sites
sea_map_after_14ka <-ggplot(world) +
  geom_sf() +
  geom_sf(data = sites_after_14ka, size = 2) +
  coord_sf(xlim = c(90, 115), 
           ylim = c(8, 30), 
           expand = FALSE) +
  geom_text_repel(data = sites_after_14ka,
                  aes(x = long, y = lat, label = Site),
                  max.overlaps = Inf,
                  size = 3, 
                  nudge_x = 0.5,
                  force = 10,
                  point.padding = 0.5,
                  box.padding = 0.5,
                  min.segment.length = 0,
                  bg.color = "white",
                  bg.r = 0.15) +
  theme_void() +
  labs(title = "Occupation after 13 ka") 

sea_map_after_14ka
```

![Sites occupied after the 14 ka population
rise](README_files/figure-commonmark/after-fig-map-1.png)
