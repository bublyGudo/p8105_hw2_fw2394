# p8105_hw2_fw2394

This is the homework 2 for p8105. 

# Problem 3

## Load "bakers" dataset:
```{r bakers}
bakers = read_csv ("./data/gbb_datasets/bakers.csv") |> 
  janitor::clean_names() |> 
  separate(baker_name, into = c("first_name", "last_name"), sep = " ", extra = "merge")
```

## Load "bakes" dataset: 
```{r bakes}
bakes = read_csv ("./data/gbb_datasets/bakes.csv") |> 
  janitor::clean_names() |> 
  rename (
    first_name = "baker"
  )
```

## Combine the two datasets above:
```{r combined_bakers}
combined_bakers =
  left_join(bakes, bakers, by = c("first_name", "series")) |> 
  arrange (series, episode) |> 
  select (series, episode, first_name, last_name, baker_age, everything())
anti_join (combined_bakers, bakes)
```

## Load the dataset "results":
```{r results}
results = 
  read_csv ("./data/gbb_datasets/results.csv", skip = 2) |> 
  mutate(
    result = case_match(
      result,
      "IN" ~"stayed_in",
      "OUT"~ "eliminated",
      "STAR BAKER" ~ "star_baker",
      "WINNER" ~ "series_winner",
      "Runner-up" ~ "series_runner-up",
      "WD" ~ "withdrew"
    )
  ) |> 
  mutate (
    star_baker = if_else (result == "star_baker", TRUE, NA),
    series_winner = if_else (result == "series_winner", TRUE, NA)
  ) |> 
  rename (
    first_name = "baker"
  )
```

## Combine to make the final dataset:
```{r final}
baker_final = 
  left_join (results, combined_bakers, by = c("first_name", "series", "episode")) |> 
  select (series, episode, first_name, last_name, baker_age, result, star_baker, series_winner, everything())
```

## Load viewer dataset:
```{r viewer}
viewer = read_csv ("./data/gbb_datasets/viewers.csv") |> 
  janitor::clean_names() |> 
  pivot_longer(
    cols = series_1:series_10,
    names_to = "series_number",
    values_to = "viewership"
  )
head(viewer, 10)
```

## Calculate the average viewership in season 1:
```{r mean_season1}
viewer |> 
  filter (episode =="1") |> 
  summarise (viewership_mean_1 = mean (viewership))
```

## Calculate the average viewership in season 5:
```{r mean_season5}
viewer |> 
  filter (episode =="5") |> 
  summarise (viewership_mean_5 = mean (viewership))
```

