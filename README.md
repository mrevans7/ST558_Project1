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
team_totals <- team_totals %>% mutate(win_greater_loss = ifelse(wins > losses, "Yes", "No"))

#Make variable a factor
team_totals$win_greater_loss <- as.factor(team_totals$win_greater_loss)

#First Graph
visuals.1 <- ggplot(data = team_totals, aes(x = wins, y = losses))
visuals.1 + geom_point(aes(color = activeFranchise, shape = win_greater_loss)) +
  geom_smooth() + 
  labs(color = "Franchise Status", shape = "Wins Greater than Losses?") + 
  scale_color_manual(values = c("red", "blue"), labels = c("Inactive", "Active"))
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

![](README_files/figure-gfm/visuals.1-1.png)<!-- -->
