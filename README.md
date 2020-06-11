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

kable(table(team_totals$activeFranchise, team_totals$win_greater_loss), caption = "Test")
```

|   | More Losses | More Wins |
| - | ----------: | --------: |
| 0 |          11 |         2 |
| 1 |          17 |        27 |

Test

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
