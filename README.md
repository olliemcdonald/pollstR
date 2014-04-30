


[![Build Status](https://travis-ci.org/rOpenGov/pollstR.svg?branch=master)](https://travis-ci.org/rOpenGov/pollstR)


# R client for the Huffpost Pollster API

This R package is an interface to the Huffington Post [Pollster API](http://elections.huffingtonpost.com/pollster/api), which provides access to opinion polls collected by the Huffington Post.

The package is released under GPL-2 and the API data it accesses is released under the [Creative Commons Attribution-NonCommercial-ShareAlike 3.0 Unported License](http://creativecommons.org/licenses/by-nc-sa/3.0/deed.en_US).



# Install

This package is not *yet* on CRAN.

You can install this with the function ``install_github`` in the **devtools** package.

```r
install.packages("devools")
library("devtools")
install_github("rOpenGov/pollstR")
```


```r
library("pollstR")
```




# API Overview

The Pollster API has two primary data structures: charts and polls.

*Polls* are individual, dated topline results for a single set of candidates in a single race.
The poll data structure consists of (generally) named candidates and percentage support for each, along with additional information (e.g., polling house, sampling frame, sample size, margin of error, state, etc.).

*Charts* aggregate polls for a particular race or topic (e.g., "2012-president" or "obama-job-approval".
A chart reports aggregated survey estimates of support for the candidates in a given race and, possibly, daily estimates of support.
Each chart is named, reports the number of aggregated polls it presents, a last-updated date, and a "slug" field. The "slug" identifies the chart both in the API and on the Pollster website.

In ``pollstR``, there are three functions in that provide access to the opinion polls and model estimates from Huffpost Pollster.

- ``pollstr_charts``: Get a list of all charts and the current model estimates.
- ``pollstr_chart``: Get a single chart along with historical model estimates.
- ``pollstr_polls``: Get opinion poll data.

## Charts

To get a list of all the charts in the API use the function ``pollstr_charts``,

```r
charts <- pollstr_charts()
str(charts)
```

```
## List of 2
##  $ charts   :'data.frame':	444 obs. of  9 variables:
##   ..$ title        : chr [1:444] "2012 Iowa GOP Primary" "2012 New Hampshire GOP Primary" "2012 South Carolina GOP Primary" "2012 Florida GOP Primary" ...
##   ..$ slug         : chr [1:444] "2012-iowa-gop-primary" "2012-new-hampshire-gop-primary" "2012-south-carolina-gop-primary" "2012-florida-gop-primary" ...
##   ..$ topic        : chr [1:444] "2012-president-gop-primary" "2012-president-gop-primary" "2012-president-gop-primary" "2012-president-gop-primary" ...
##   ..$ state        : Factor w/ 51 levels "IA","NH","SC",..: 1 2 3 4 5 6 7 8 8 8 ...
##   ..$ short_title  : chr [1:444] "1/3 Iowa Caucus" "1/10 New Hampshire Primary" "1/21 South Carolina Primary" "1/31 Florida Primary" ...
##   ..$ election_date: Date[1:444], format: NA ...
##   ..$ poll_count   : int [1:444] 65 55 44 59 10 34 19 258 589 300 ...
##   ..$ last_updated : POSIXct[1:444], format: "2012-01-02 13:08:44" ...
##   ..$ url          : chr [1:444] "http://elections.huffingtonpost.com/pollster/2012-iowa-gop-primary" "http://elections.huffingtonpost.com/pollster/2012-new-hampshire-gop-primary" "http://elections.huffingtonpost.com/pollster/2012-south-carolina-gop-primary" "http://elections.huffingtonpost.com/pollster/2012-florida-gop-primary" ...
##  $ estimates:'data.frame':	1046 obs. of  8 variables:
##   ..$ choice         : chr [1:1046] "Romney" "Paul" "Santorum" "Gingrich" ...
##   ..$ value          : num [1:1046] 22.5 21.3 15.9 12.6 11.1 8.3 3.7 5.9 0.9 39.6 ...
##   ..$ lead_confidence: num [1:1046] NA NA NA NA NA NA NA NA NA NA ...
##   ..$ first_name     : chr [1:1046] "Mitt" "Ron" "Rick" "Newt" ...
##   ..$ last_name      : chr [1:1046] "Romney" "Paul" "Santorum" "Gingrich" ...
##   ..$ party          : Factor w/ 6 levels "Dem","Gre","ind",..: 6 6 6 6 6 6 6 NA NA 6 ...
##   ..$ incumbent      : logi [1:1046] FALSE FALSE FALSE FALSE FALSE FALSE ...
##   ..$ slug           : chr [1:1046] "2012-iowa-gop-primary" "2012-iowa-gop-primary" "2012-iowa-gop-primary" "2012-iowa-gop-primary" ...
##  - attr(*, "class")= chr "pollstr_charts"
```

This returns a ``list`` with two data frames.
The data frame ``charts`` has data on each chart,
while the data frame ``estimates`` has the current poll-tracking estimates from each chart.

The query can be filtered by state or topic.
For example, to get only charts related to national topics,

```r
us_charts <- pollstr_charts(state = "US")
```


## Chart

To get a particular chart use the function ``pollstr_chart``.
For example, to get the chart for [Barack Obama's Favorable Rating](http://elections.huffingtonpost.com/pollster/obama-favorable-rating), specify its *slug*, ``obama-favorable-rating``.

```r
obama_favorable <- pollstr_chart("obama-favorable-rating")
print(obama_favorable)
```

```
## Title:       Barack Obama Favorable Rating 
## Chart Slug:  obama-favorable-rating 
## Topic:       favorable-ratings 
## State:       1 
## Polls:       804 
## Updated:     1.398e+09 
## URL:         http://elections.huffingtonpost.com/pollster/obama-favorable-rating 
## Estimates:
##             choice value lead_confidence first_name last_name party
## 1        Favorable  45.7              NA       <NA>      <NA>  <NA>
## 2      Unfavorable  49.0              NA       <NA>      <NA>  <NA>
## 3        Undecided   4.5              NA       <NA>      <NA>  <NA>
## 4 Not Heard Enough   0.2              NA       <NA>      <NA>  <NA>
##   incumbent
## 1        NA
## 2        NA
## 3        NA
## 4        NA
## 
## First 6 (of 2123) daily estimates:
##        choice value       date
## 1   Favorable  45.7 2014-04-21
## 2   Undecided   4.5 2014-04-21
## 3 Unfavorable  49.0 2014-04-21
## 4   Favorable  45.7 2014-04-15
## 5   Undecided   4.4 2014-04-15
## 6 Unfavorable  49.0 2014-04-15
```

The slug can be found from the results of a ``pollstr_charts`` query.
Alternatively the slug is the path of the url of a chart, http://elections.huffingtonpost.com/pollster/obama-favorable-rating.

The historical estimates of the Huffpost Pollster poll-tracking model are contained in the element ``"estimates_by_date"``,

```r
(ggplot(obama_favorable[["estimates_by_date"]], aes(x = date, y = value, color = choice)) + 
    geom_line())
```

![plot of chunk obama-favorable-chart](README-figures/obama-favorable-chart.png) 


## Polls

To get the opinion poll results use the function ``pollstr_polls`.
The polls returned can be filtered by topic, chart, state, or date.

By default, ``pollstr_polls`` only returns 1 page of results (about 10 polls).
To have it return more polls, increase the value of ``max_pages``.
To have it return all polls, set the value of ``max_pages`` to a very high number.
For example, to return all the polls on the favorability of Bararck Obama after March 1, 2014,

```r
obama_favorable_polls <- pollstr_polls(max_pages = 10000, chart = "obama-favorable-rating", 
    after = "2014-3-1")
str(obama_favorable_polls)
```

```
## List of 2
##  $ polls    :'data.frame':	15 obs. of  9 variables:
##   ..$ id           : int [1:15] 19316 19256 19261 19252 19239 19169 19132 19137 19172 19123 ...
##   ..$ pollster     : Factor w/ 8 levels "YouGov/Economist",..: 1 2 1 3 1 1 1 4 5 6 ...
##   ..$ start_date   : Date[1:15], format: "2014-04-19" ...
##   ..$ end_date     : Date[1:15], format: "2014-04-21" ...
##   ..$ method       : Factor w/ 2 levels "Internet","Phone": 1 2 1 2 1 1 1 1 2 2 ...
##   ..$ source       : chr [1:15] "http://d25d2506sfb94s.cloudfront.net/cumulus_uploads/document/lx2kkwdvcu/econToplines.pdf" "http://www.foxnews.com/politics/interactive/2014/04/21/fox-news-poll-independents-more-likely-to-back-anti-obamacare-candidates"| __truncated__ "http://d25d2506sfb94s.cloudfront.net/cumulus_uploads/document/8cnxcwv20i/econToplines.pdf" "http://maristpoll.marist.edu/wp-content/misc/usapolls/us140407/Obama/Complete%20April%202014%20USA%20McClatchy_Marist%20Poll%20"| __truncated__ ...
##   ..$ last_updated : POSIXct[1:15], format: "2014-04-25 14:45:08" ...
##   ..$ survey_houses: chr [1:15] "" "" "" "" ...
##   ..$ sponsors     : chr [1:15] "" "" "" "" ...
##  $ questions:'data.frame':	971 obs. of  14 variables:
##   ..$ question       : Factor w/ 33 levels "US Right Direction Wrong Track",..: 1 1 1 2 2 2 2 2 3 3 ...
##   ..$ chart          : Factor w/ 31 levels "2014-national-house-race",..: 31 31 31 17 17 17 17 17 20 20 ...
##   ..$ topic          : Factor w/ 7 levels "","2014-house",..: 1 1 1 6 6 6 6 6 6 6 ...
##   ..$ state          : Factor w/ 1 level "US": 1 1 1 1 1 1 1 1 1 1 ...
##   ..$ subpopulation  : Factor w/ 12 levels "Adults","Adults - Democrat",..: 1 1 1 1 1 1 1 1 1 1 ...
##   ..$ observations   : int [1:971] 1000 1000 1000 1000 1000 1000 1000 1000 1000 1000 ...
##   ..$ margin_of_error: num [1:971] 4.1 4.1 4.1 4.1 4.1 4.1 4.1 4.1 4.1 4.1 ...
##   ..$ choice         : chr [1:971] "Right Direction" "Wrong Track" "Undecided" "Very Favorable" ...
##   ..$ value          : num [1:971] 32 55 13 13 21 13 18 34 24 19 ...
##   ..$ first_name     : chr [1:971] NA NA NA NA ...
##   ..$ last_name      : chr [1:971] NA NA NA NA ...
##   ..$ party          : Factor w/ 3 levels "Dem","ind","Rep": NA NA NA NA NA NA NA NA NA NA ...
##   ..$ incumbent      : logi [1:971] NA NA NA NA NA NA ...
##   ..$ id             : int [1:971] 19316 19316 19316 19316 19316 19316 19316 19316 19316 19316 ...
##  - attr(*, "class")= chr "pollstr_polls"
```




# Example: Obama's Job Approval Rating

This section shows how to use ``pollstr`` to create a chart similar to those displayed on the Huffpost Pollster website.
I'll use Obama's job approval rating in this example.

The slug or name of the chart is ``obama-job-approval``, which is derived from the chart's URL , http://elections.huffingtonpost.com/pollster/obama-job-approval.
I'll focus on approval in 2013 in order to reduce the time necessary to run this code.

```r
slug <- "obama-job-approval"
start_date <- as.Date("2013-1-1")
end_date <- as.Date("2014-1-1")
```

For the plot, I'll need both Pollster's model estimates and opinion poll estimates.
I get the Pollster model estimates using ``polster_chart``,

```r
chart <- pollstr_chart(slug)
estimates <- chart[["estimates_by_date"]]

estimates <- estimates[estimates$date >= start_date & estimates$date < end_date, 
    ]
```

and the opinion poll results,

```r
polls <- pollstr_polls(chart = slug, after = start_date, before = end_date, 
    max_pages = 1e+06)
```

Note that in ``polster_poll`` I set the ``max_pages`` argument to a very large number in order to download all the polls available.
This may take several minutes.

Before continuing, we will need to clean up the opinion poll data.
First, only keep results from national subpopulations ("Adults", "Likely Voters", "Registered Voters").
This will drop subpopulations like Republicans, Democrats, and Independents.

```r
questions <- subset(polls[["questions"]], chart == slug & subpopulation %in% 
    c("Adults", "Likely Voters", "Registered Voters"))
```

Second, I will need to recode the choices into three categories, "Approve", "Disapprove", and "Undecided".

```r
approvalcat <- c(Approve = "Approve", Disapprove = "Disapprove", Undecided = "Undecided", 
    Neither = "Undecided", Refused = NA, Neutral = "Undecided", `Strongly Approve` = "Approve", 
    `Somewhat Approve` = "Approve", `Somewhat Disapprove` = "Disapprove", `Strongly Disapprove` = "Disapprove")

questions2 <- (questions %.% mutate(choice = plyr::revalue(choice, approvalcat)) %.% 
    group_by(id, subpopulation, choice) %.% summarise(value = sum(value)))
```

Now merge the question data with the poll metadata,

```r
polldata <- merge(polls$polls, questions2, by = "id")
```


Now, I can plot the opinion poll results along with the Huffpost Pollster trend estimates,

```r
(ggplot() + geom_point(data = polldata, mapping = aes(y = value, x = end_date, 
    color = choice), alpha = 0.3) + geom_line(data = estimates, mapping = aes(y = value, 
    x = date, color = choice)) + scale_x_date("date") + scale_color_manual(values = c(Approve = "black", 
    Disapprove = "red", Undecided = "blue")))
```

![plot of chunk obama-favorable-chart-2](README-figures/obama-favorable-chart-2.png) 

```r

```


<!--  LocalWords:  Huffpost API Huffington CRAN github devtools str
 -->
<!--  LocalWords:  devools jrnold ggplot obama url aes favorability
 -->
<!--  LocalWords:  Bararck
 -->


# Misc

An earlier R interface was written by [Drew Linzer](https://github.com/dlinzer/pollstR/).

<!--  LocalWords:  Huffpost API Huffington CRAN github devtools str
 -->
<!--  LocalWords:  devools jrnold ggplot obama url aes favorability
 -->
<!--  LocalWords:  Bararck suppressPackageStartupMessages eval
 -->
<!-- -->
<!--  LocalWords:  rOpenGov pollstR pollstr Linzer
 -->
