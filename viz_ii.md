Visualization
================

## Load weather data

## Recall this plot …

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) + 
  geom_point(alpha = .5)
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](viz_ii_files/figure-gfm/unnamed-chunk-2-1.png)<!-- -->

## Labels

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) + 
  geom_point(alpha = .5) + 
  labs(
    title = "Temperature plot", 
    x = "Minimum daily temperature (C)",
    Y = "Minimum daily temperature (C)",
    caption = "Data from rnoaa package; temperatures in 2017"
  )
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](viz_ii_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->

## scales

Start with the same plot; x and y scales

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) + 
  geom_point(alpha = .5) + 
  labs(
    title = "Temperature plot", 
    x = "Minimum daily temperature (C)",
    Y = "Minimum daily temperature (C)",
    caption = "Data from rnoaa package; temperatures in 2017"
  ) + 
  scale_x_continuous(
    breaks = c(-15, 0, 15), 
    labels = c("-15 ˚C", "O ˚C","15 ˚C")
  ) + 
  scale_y_continuous(
    #trans = "sqrt"
   # trans = "log"
    position = "right"
  )
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](viz_ii_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->

Look at other scales

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) + 
  geom_point(alpha = .5) + 
  labs(
    title = "Temperature plot", 
    x = "Minimum daily temperature (C)",
    Y = "Minimum daily temperature (C)",
    caption = "Data from rnoaa package; temperatures in 2017"
  ) +
  scale_color_hue(
    name = "Location",
    h = c(100, 800)
    )
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](viz_ii_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->

And if you don’t want to deal with choosing your own colors Using the
`viridis` package

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) + 
  geom_point(alpha = .5) + 
  labs(
    title = "Temperature plot", 
    x = "Minimum daily temperature (C)",
    Y = "Minimum daily temperature (C)",
    caption = "Data from rnoaa package; temperatures in 2017"
  ) + 
  viridis::scale_color_viridis(
  name = "Location", 
  discrete = TRUE)
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](viz_ii_files/figure-gfm/unnamed-chunk-6-1.png)<!-- -->

## Themes

Shift the legend

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) + 
  geom_point(alpha = .5) + 
  labs(
    title = "Temperature plot", 
    x = "Minimum daily temperature (C)",
    Y = "Minimum daily temperature (C)",
    caption = "Data from rnoaa package; temperatures in 2017"
  ) + 
  viridis::scale_color_viridis(
  name = "Location", 
  discrete = TRUE) + 
  theme(legend.position = "bottom")
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](viz_ii_files/figure-gfm/unnamed-chunk-7-1.png)<!-- -->

Change the overall theme.

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) + 
  geom_point(alpha = .5) + 
  labs(
    title = "Temperature plot", 
    x = "Minimum daily temperature (C)",
    Y = "Minimum daily temperature (C)",
    caption = "Data from rnoaa package; temperatures in 2017"
  ) + 
  viridis::scale_color_viridis(
  name = "Location", 
  discrete = TRUE) + 
  #theme_bw() ##options for overall themes
  #theme_classic()
  #ggthemes::theme_economist()
  #ggthemes::theme_excel()
   theme_minimal() + 
  theme(legend.position = "bottom")
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](viz_ii_files/figure-gfm/unnamed-chunk-8-1.png)<!-- -->

Setting options

``` r
library(tidyverse)

knitr::opt_chunk$set(
  fig.width = 6,
  fig.asp = .6, 
  out.width = "90%"
)

theme_set(theme_minimal() + theme(legend.position = "bottom"))

options(
  ggplot2.continous.colour = "viridis",
  ggplot2.continous.fill = "viridis"
)

scale_colour_discrete = scale_color_viridis_d
scale_fill_discrete = scale_fill_viridis_d
```

## Data args in `geom`

Which geom applies to which dataset? How to use 2 geoms specifying to
different data frames in one ggplot statement? 1st create the two
datasets: 2nd: specify the datasets in the 2 dft geoms of interest

``` r
central_park = 
  weather_df %>% 
  filter(name == "CentralPark_NY")

waikiki = 
  weather_df %>% 
  filter(name == "Waikiki_HA")


ggplot(data = waikiki, aes(x = date, y = tmax, color = name)) + 
  geom_point() + 
  geom_line(data = central_park)
```

    ## Warning: Removed 3 rows containing missing values (geom_point).

![](viz_ii_files/figure-gfm/unnamed-chunk-10-1.png)<!-- -->

## `patchwork`

Recall faceting …

``` r
weather_df %>% 
  ggplot(aes(x = tmin, fill = name)) + 
  geom_density(alpha = .5) + 
  facet_grid(. ~ name)
```

    ## Warning: Removed 15 rows containing non-finite values (stat_density).

![](viz_ii_files/figure-gfm/unnamed-chunk-11-1.png)<!-- -->

But what if we wanted to plot two different types of plots side by side
and can’t facet? BOOM - `faceting`

## patchwork in practice

``` r
tmax_tmin_plot = 
  weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) + 
  geom_point(alpha = .5) + 
  theme(legend.position = "none")

prcp_dens_plot = 
  weather_df %>% 
  filter(prcp > 0) %>% 
  ggplot(aes(x = prcp, fill = name)) +
  geom_density(alpha = .5) + 
  theme(legend.position = "none")

tmax_date_plot = 
  weather_df %>% 
  ggplot(aes(x = date, y = tmax, color = name)) + 
  geom_point() + 
  geom_smooth(se = FALSE) + 
  theme(legend.position = "none")


# tmax_tmin_plot + prcp_dens_plot + tmax_date_plot
# tmax_tmin_plot + (prcp_dens_plot + tmax_date_plot)
# tmax_tmin_plot / (prcp_dens_plot + tmax_date_plot)
(tmax_tmin_plot + prcp_dens_plot) / (tmax_date_plot)
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 3 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 3 rows containing missing values (geom_point).

![](viz_ii_files/figure-gfm/unnamed-chunk-12-1.png)<!-- -->
