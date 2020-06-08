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
