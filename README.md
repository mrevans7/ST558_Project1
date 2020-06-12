ST558 Project 1
================
Michael Evans
6/12/2020

  - [JSON Data](#json-data)
      - [What is it?](#what-is-it)
      - [Where does it get used?](#where-does-it-get-used)
      - [Why is it a good way to store
        data?](#why-is-it-a-good-way-to-store-data)
      - [Packages Available](#packages-available)
      - [My Choice of Package](#my-choice-of-package)
      - [References](#references)
  - [API Calls](#api-calls)
      - [Franchise Function](#franchise-function)
      - [Franchise Team Totals
        Function](#franchise-team-totals-function)
      - [Season Records for Franchise
        Function](#season-records-for-franchise-function)
      - [Goalie Records for Franchise
        Function](#goalie-records-for-franchise-function)
      - [Skater Records for Franchise
        Function](#skater-records-for-franchise-function)
  - [Exploratory Data Analysis](#exploratory-data-analysis)
      - [Contingency Tables](#contingency-tables)
      - [Numerical Summaries](#numerical-summaries)
      - [Plots](#plots)

# JSON Data

## What is it?

JSON stands for Javascript Object Notation and is a way of storing data
in an organized fashion that is easy to access. Generally regarded as a
“lightweight data-interchange format”, JSON data is great for both
humans and machines, as it is both readable by humans and able to be
parsed by machines. It was made popular by Douglas Crawford. This data
type is built on collections of name/value pairs and an ordered list of
values.

## Where does it get used?

Since the two data types JSON data is built on are generally universal
data structures, JSON data is considered language-independent. Because
of this, many of the programming languages used today have ways of
reading JSON data. JSON data is essentially just a text file, which
makes it easy to send over servers. This feature has allowed it to be
used frequently for transmitting information over the web.

## Why is it a good way to store data?

Some of the previous features mentioned are the same reasons as to why
JSON data is a good way of storing data. For starters, the “lightweight”
feature of the data makes it extremely accessible. JSON data can be
loaded quickly because of how “lightweight” it is, which has made it a
great way to store data on the internet. It can be called and loaded
quickly.

Another reason that it is a good way of storing data is because it is
easily compatible with so many modern programming languages. This means
that many people working across different programs can still have easy
access to the same datasets.

That being said, there are also some limitations of JSON data. There is
no way to add comments to JSON data. While JSON data is often readable,
it can be difficult to understand the context of certain things without
comments. This means that you often need to consult additional
documentation to understand the data. Additionally, the language has no
schema, which means that you could accidentally create data that you are
not intending to create. On the other hand, the lack of schema is
something that a lot of people like about JSON data, as it offers more
flexibility.

## Packages Available

There are three major packages available in R for reading JSON data.
These include `jsonlite`, `RJSONIO`, and `rjson`. `jsonlite` is the
newest package of the three and was developed based off of the `RJSONIO`
package. While all of these packages are able to read JSON data,
`jsonlite` offers the ability to return a data frame. Typically,
`RJSONIO` and `rjson` return a list.

`RJSONIO` was built as an alternative to the `rjson` package to offer a
faster way to convert JSON data in R. Through updates, the `rjson`
package is not just as fast, if not faster, than the `RJSONIO` package.
The `RJSONIO` package also offers some additional options for
customizing the JSON data that is being read into the program.

## My Choice of Package

For this project, I am choosing to use the `jsonlite` package, as I like
that it returns a data frame. The data frame is one of the most usable
objects in R and will be helpful for doing exploratory data analysis
after the data is read in. We will combine this package with the `httr`
package to get JSON data.

To read JSON data, we will first use the `GET()` function from `httr` to
get the whatever information is present from the URL provided. Then, we
will use the `content()` function from `httr` to retreive the content of
the URL as a text file (use the option `as = "text` to do this).
Finally, we will use the `fromJSON()` function from `jsonlite` to
convert the JSON data to an R object. We will use the `flatten = T`
option to automatically flatten the nested data frame into a non-nested
data frame.

## References

For more information on JSON data and the packages available in R, check
out these resources that I consulted to gather the initial information:
1. <https://www.json.org/json-en.html> 2.
<https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Objects/JSON>
3.
<https://www.infoworld.com/article/3222851/what-is-json-a-better-format-for-data-exchange.html>
4. <https://www.r-bloggers.com/better-handling-of-json-data-in-r/> 5.
<https://cran.r-project.org/web/packages/RJSONIO/index.html>

# API Calls

## Franchise Function

We will use `function()` to create this function. We will save the URL
that has the data as `full_url`. Then, we will use the `GET()` function
with the full URL and save this object as `get`. Next, we will use the
`content()` function to get the data from this URL. We will want to save
this data as a text file, so we will add the option `as = "text"`. We
will save this output as `content`. Next, we will get the data using the
`fromJSON()` function. We will include the `flatten = T` option to get
this output as a data frame. This object will be saved as `nhl_data`.
Finally, we will select `data` from `nhl_data`, as this has the data we
are looking for. This object will be saved as `data` and returned using
the `return()` function.

``` r
#Franchise Function
franchise <- function(){
  full_url <- "https://records.nhl.com/site/api/franchise"
  get <- GET(url = full_url)
  content <- content(get, as = "text")
  nhl_data <- fromJSON(content, flatten = T)
  data <- nhl_data$data
  return(data)
}
```

We will then get the franchise data by calling the function
`franchise()` and saving the output as the object `franchise_data`.

``` r
#Get Data
franchise_data <- franchise()
```

## Franchise Team Totals Function

We will follow the same steps as we did for the `franchise()` function,
except we will change the URL to access data about franchise team
totals.

``` r
#Franchise Team Totals Function
team_totals <- function(){
  full_url <- "https://records.nhl.com/site/api/franchise-team-totals"
  get <- GET(url = full_url)
  content <- content(get, as = "text")
  nhl_data <- fromJSON(content, flatten = T)
  data <- nhl_data$data
  return(data)
}
```

We will then get the total stats for each franchise by calling the
function `team_totals()` and saving the output as the object
`team_totals_data`.

``` r
#Get Data
team_totals_data <- team_totals()
```

## Season Records for Franchise Function

We will follow the same steps as we did for the `franchise()` function,
except this function will take in an ID and return the appropriate data
set for that ID. Within the function, we will use `paste0()` to combine
the base URL with the given ID. The base URL will access data on season
records for different franchises.

``` r
#Season Records for Franchise
season_franchise <- function(ID){
  ID <- as.character(ID)
  full_url <- paste0("https://records.nhl.com/site/api/franchise-season-records?cayenneExp=franchiseId=", ID) 
  get <- GET(url = full_url)
  content <- content(get, as = "text")
  nhl_data <- fromJSON(content, flatten = T)
  data <- nhl_data$data
  return(data)
}
```

We will then get the season records for a specific franchise by calling
the function `season_franchise("ID")` and saving the output as the
object `season_franchise_data`. For this analysis, we will get data for
the Philadelphia Flyers, so we will use `"16"` as the ID.

``` r
#Get Data
season_franchise_data <- season_franchise("16")
```

    ## No encoding supplied: defaulting to UTF-8.

## Goalie Records for Franchise Function

We will follow the same steps as we did for the `franchise()` function,
except this function will take in an ID and return the appropriate data
set for that ID. Within the function, we will use `paste0()` to combine
the base URL with the given ID. The base URL will access data on goalie
records for different franchises.

``` r
#Goalie Records for Franchise
goalie_records <- function(ID){
  ID <- as.character(ID)
  full_url <- paste0("https://records.nhl.com/site/api/franchise-goalie-records?cayenneExp=franchiseId=", ID) 
  get <- GET(url = full_url)
  content <- content(get, as = "text")
  nhl_data <- fromJSON(content, flatten = T)
  data <- nhl_data$data
  return(data)
}
```

We will then get the goalie records for a specific franchise by calling
the function `goalie_records("ID")` and saving the output as the object
`goalie_records_data`. For this analysis, we will get data for the
Philadelphia Flyers, so we will use `"16"` as the ID.

``` r
#Get Data
goalie_records_data <- goalie_records("16")
```

## Skater Records for Franchise Function

We will follow the same steps as we did for the `franchise()` function,
except this function will take in an ID and return the appropriate data
set for that ID. Within the function, we will use `paste0()` to combine
the base URL with the given ID. The base URL will access data on skater
records for different franchises.

``` r
#Skater Records for Franchise
skater_records <- function(ID){
  ID <- as.character(ID)
  full_url <- paste0("https://records.nhl.com/site/api/franchise-skater-records?cayenneExp=franchiseId=", ID) 
  get <- GET(url = full_url)
  content <- content(get, as = "text")
  nhl_data <- fromJSON(content, flatten = T)
  data <- nhl_data$data
  return(data)
}
```

We will then get the skater records for a specific franchise by calling
the function `skater_records("ID")` and saving the output as the object
`skater_records_data`. For this analysis, we will get data for the
Philadelphia Flyers, so we will use `"16"` as the ID.

``` r
#Get Data
skater_records_data <- skater_records("16")
```

# Exploratory Data Analysis

## Contingency Tables

``` r
#Make frachise status a factor
team_totals_data$activeFranchise <- as.factor(team_totals_data$activeFranchise)

#Sum the number of wins and losses per each unique team
team_totals <- aggregate(list(team_totals_data$wins, team_totals_data$losses), by = list(team_totals_data$teamId, team_totals_data$activeFranchise, team_totals_data$teamName), sum)

#Rename the columns
names(team_totals) <- c("teamId", "activeFranchise", "teamName", "wins", "losses")

#Add variable to indictate is wins > losses
team_totals <- team_totals %>% mutate(win_greater_loss = ifelse(wins > losses, "More Wins", "More Losses"))

#Make variable a factor
team_totals$win_greater_loss <- as.factor(team_totals$win_greater_loss)

#Create Contingency Table
table.1 <- table(team_totals$activeFranchise, team_totals$win_greater_loss)

#Rename Columns
colnames(table.1) <- c("More Losses", "More Wins")

#Rename Rows
rownames(table.1) <- c("Inactive", "Active")

#Create Kable
kable(table.1, caption = "More Wins/Losses vs. Franchise Status")
```

|          | More Losses | More Wins |
| -------- | ----------: | --------: |
| Inactive |          11 |         2 |
| Active   |          17 |        27 |

More Wins/Losses vs. Franchise Status

``` r
#Create Contingency Table
table.2 <- table(skater_records_data$positionCode, skater_records_data$seasons)

#Create Kable
kable(table.2, caption = "Seasons Played vs. Position")
```

|   |  1 |  2 |  3 |  4 |  5 | 6 | 7 | 8 | 9 | 10 | 11 | 12 | 13 | 15 |
| - | -: | -: | -: | -: | -: | -: | -: | -: | -: | -: | -: | -: | -: | -: |
| C | 47 | 38 | 20 | 11 |  8 | 6 | 3 | 2 | 4 |  0 |  0 |  1 |  1 |  1 |
| D | 76 | 42 | 28 | 23 | 13 | 8 | 3 | 1 | 3 |  2 |  3 |  0 |  0 |  0 |
| L | 49 | 29 | 19 |  3 |  5 | 1 | 5 | 2 | 0 |  2 |  2 |  1 |  0 |  0 |
| R | 42 | 26 | 19 |  6 |  5 | 2 | 2 | 3 | 3 |  1 |  4 |  0 |  0 |  0 |

Seasons Played vs. Position

``` r
#Create Contingency Table
table.3 <- table(skater_records_data$activePlayer, skater_records_data$mostGoalsOneGame)

#Rename Rows
rownames(table.3) <- c("Inactive", "Active")

#Create Kable
kable(table.3, caption = "Most Goals in a Game vs. Player Status")
```

|          |   0 |   1 |   2 |  3 | 4 |
| -------- | --: | --: | --: | -: | -: |
| Inactive | 121 | 222 | 119 | 46 | 9 |
| Active   |  13 |  22 |  16 |  7 | 0 |

Most Goals in a Game vs. Player Status

## Numerical Summaries

``` r
goalie_records_summary <- goalie_records_data %>% select(gamesPlayed, losses, wins, mostGoalsAgainstOneGame,
                                                         mostSavesOneGame)

#Get min, 25th quantile, mean, median, 75th quantile, and max
table.4 <- sapply(goalie_records_summary, min)
table.5 <- sapply(goalie_records_summary, quantile, probs = .25, na.rm=TRUE)
table.6 <- sapply(goalie_records_summary, mean, na.rm=TRUE)
table.7 <- sapply(goalie_records_summary, median, na.rm=TRUE)
table.8 <- sapply(goalie_records_summary, quantile, probs = .75, na.rm=TRUE)
table.9 <- sapply(goalie_records_summary, max, na.rm=TRUE)

#Combine into one table
table.10 <- rbind(table.4, table.5, table.6, table.7, table.8, table.9)

#Rename rows
rownames(table.10) <- c("Min.", "25th Percentile", "Mean", "Median", "75th Percentile", "Max.")

#Rename Columns
colnames(table.10) <- c("Games Played", "Losses", "Wins", "Most Goals Against (One Game)", 
                         "Most Saves (One Game)") 

kable(table.10, caption = "Summary for Goalie Statistics")
```

|                 | Games Played | Losses |      Wins | Most Goals Against (One Game) | Most Saves (One Game) |
| --------------- | -----------: | -----: | --------: | ----------------------------: | --------------------: |
| Min.            |      1.00000 |      0 |   0.00000 |                      2.000000 |              20.00000 |
| 25th Percentile |     12.25000 |      3 |   4.25000 |                      5.000000 |              34.50000 |
| Mean            |     89.26471 |     29 |  41.20588 |                      6.235294 |              38.70588 |
| Median          |     47.50000 |     15 |  21.50000 |                      6.000000 |              39.50000 |
| 75th Percentile |    110.75000 |     35 |  52.00000 |                      7.000000 |              44.75000 |
| Max.            |    489.00000 |    172 | 240.00000 |                     11.000000 |              54.00000 |

Summary for Goalie Statistics

## Plots

``` r
#First Visual
visuals.1 <- ggplot(data = team_totals, aes(x = wins, y = losses))
visuals.1 + geom_point(aes(color = activeFranchise, shape = win_greater_loss)) +
  geom_smooth() + 
  labs(color = "Franchise Status", shape = "More Wins or Losses?") + 
  scale_color_manual(values = c("red", "blue"), labels = c("Inactive", "Active"))
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

![](README_files/figure-gfm/visuals.1-1.png)<!-- -->

``` r
#Second Visual
visuals.2 <- ggplot(data = skater_records_data, aes(x = activePlayer, y = goals))
visuals.2 + geom_boxplot(aes(color = activePlayer)) +
  geom_jitter(aes(color = activePlayer), alpha = .2) +
  ylim(0, 75) +
  labs(title = "Boxplot for Goals Scored", color = "Player Status", x = "Player Status", y = "Goals") +
  scale_x_discrete(labels = c("Inactive", "Active")) +
  scale_color_discrete(labels = c("Inactive", "Active"))
```

![](README_files/figure-gfm/visuals.2-1.png)<!-- -->

``` r
#Add variable to indictate if wins > 50
goalie_records_data <- goalie_records_data %>% mutate(win_50 = ifelse(wins > 50, "More Than 50", "Less Than 50"))

#Make variable a factor
goalie_records_data$win_50 <- as.factor(goalie_records_data$win_50)

visuals.3 <- ggplot(data = goalie_records_data, aes(x = shutouts))
visuals.3 + geom_histogram(aes(fill = win_50, y = ..density..), bins = 20) +
  geom_density() +
  labs(title = "Bar Plot for Shutouts", x = "Shutouts", y = "Density", fill = "Number of Wins")
```

![](README_files/figure-gfm/visuals.3-1.png)<!-- -->

``` r
visuals.4 <- ggplot(data = skater_records_data, aes(x = seasons))
visuals.4 + geom_histogram(aes(fill = positionCode), bins = 15) +
  labs(title = "Bar Plot for Seasons Played", x = "Number of Seasons Played", y = "Count", fill = "Position")
```

![](README_files/figure-gfm/visuals.4-1.png)<!-- -->

``` r
visuals.5 <- ggplot(data = skater_records_data, aes(x = mostGoalsOneGame))
visuals.5 + geom_histogram(aes(fill = activePlayer), bins = 5, position = "dodge") +
  labs(title = "Bar Plot for Most Goals in One Game", x = "Most Goals in One Game", y = "Count", 
       fill = "Player Status") +
  scale_fill_discrete(labels = c("Inactive", "Active"))
```

![](README_files/figure-gfm/visuals.5-1.png)<!-- -->

``` r
visuals.6 <- ggplot(data = skater_records_data, aes(x = mostGoalsOneGame))
visuals.6 + geom_density(aes(fill = activePlayer), color = NA, size = 3, adjust = 5, alpha = .5) +
  labs(title = "Density Plot for Most Goals in One Game", x = "Most Goals in One Game", y = "Density", 
       fill = "Player Status") +
  scale_fill_discrete(labels = c("Inactive", "Active"))+
  guides(color = FALSE)
```

![](README_files/figure-gfm/visuals.6-1.png)<!-- -->
