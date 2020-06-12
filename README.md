ST558 Project 1
================
Michael Evans
6/5/2020

# API Calls

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

#Get Data
franchise_data <- franchise()
```

    ## No encoding supplied: defaulting to UTF-8.

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

#Get Data
team_totals_data <- team_totals()
```

    ## No encoding supplied: defaulting to UTF-8.

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

#Get Data
season_franchise_data <- season_franchise("16")
```

    ## No encoding supplied: defaulting to UTF-8.

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

#Get Data
goalie_records_data <- goalie_records("16")
```

    ## No encoding supplied: defaulting to UTF-8.

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

#Get Data
skater_records_data <- skater_records("16")
```

    ## No encoding supplied: defaulting to UTF-8.

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

Most Goals in a Game vs. Player
Status

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
