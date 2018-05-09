# Useful R <s>one-liners</s> knowledge
***
> The streamlining of the big dollar stores opens up, for other outlets, their original source of cheap merchandise: distressed goods, closeouts, overstock, salvage merchandize, department-store returns, liquidated goods, discontinued lines, clearance items, ex-catalog stock, freight-damaged goods, irregulars, salvage cosmetics, test-market items and bankruptcy inventories.

Here is my dollar-store inventory of how to do stuff in R

## Install/load many packages at once
install.packages('needs') # first we install "needs" the old way
library(needs)
needs(rmarkdown, ggplot2, readr, tidyr, stringr)

## Useful line for knitting `.Rmd` files
if (!require("needs")) {
   install.packages("needs", dependencies = TRUE)
   library(needs)
}
needs(rmarkdown, ggplot2, readr, tidyr, stringr)

## Add more color's to brewer.pal's 9 color limit with colorRampPalette
color = colorRampPalette(rev(brewer.pal(n = 9, name = "BuPu")))(100)

## If you see that year is being treated as a number and not as a year or factor
df$year <- factor(df$year, ordered = TRUE)

## Rename a column 
cars >%
   filter(speed > 20) %>%
   rename(high_speed = speed)
   
## `add_tally()` is short-hand for `mutate()`
mtcars %>% add_tally()

## `count()` is short-hand for `group_by()` and `tally()`
mtcars %>% add_tally()

## `add_count()` is short-hand for `group_by()` and `add_tally()` and is useful for groupwise filtering (e.g. show only species with a single member)
starwars %>%
  add_count(species) %>%
  filter(n == 1)

## Conditional statement to replace an erroneous date with a missing value
library(lubridate)
library(dplyr)
y <- mdy("1/23/2016", "3/2/2016", "12/1/1901", "11/23/2016")
if_else( year(y) != 2016, mdy(NA), y) # wrapping NA in date function mdy() forcces it into a date object

y <- c("apple", "banana", "banana", "pear", "apple")
if_else( y == "banana", as.character(NA), y)

## Changing values based on multiple conditions 
### if multiple sets of conditions are to be tested, nested if/else statements become cumbersome and are prone to clerical error.
z <- c("deg","F","C", "Temperature")
case_when(z == "deg" ~ "Degree",
          z == "F" ~ "Fahrenheit",
          z == "C" ~ "Celsius",
          TRUE ~ z)
          
## Use `if_else` `case_when` and `recode` inside a `mutate` function
dat1 <- dat %>% 
  mutate(Country = recode(Country, "Canada" = "CAN",
                                   "United States of America" = "USA"),
         Type = case_when(Source == "Calculated data" ~ 1,
                          Source == "Official data" ~ 2,
                          TRUE ~ 3)) 
head(dat1)  

## Outputting a vector instead of a table using `pull()`
oats <- dat %>% 
  filter(Crop == "Oats",
         Information == "Yield (Hg/Ha)") %>% 
  summarise(Oats_sum = sum(Value)) %>% 
  pull()
oats
          
## Replace NA factor levels
library(forcats)
x <- fct_explicit_na(x, na_level = "Other")

## If a column has many measurements for one variable (duplicate row names with different values in different column) group them and take the sum
if (!require("dplyr")) {
   install.packages("dplyr", dependencies = TRUE)
   library(dplyr)
}

df1 <- df1 %>% group_by(ensembl_gene_name) %>% summarise(sum(dBet6_1h))

## Merge multiple columns from different datasets together based on similar row values in a column
m1 <- merge(df1, df2, "ensembl_gene_name")

## Turn +/- Inf values into some arbitrary number
dm1[dm1==Inf] <- 2

dm1[dm1==-Inf] <- -2

## Turn numbers greater than 10 to 10 and numbers smaller than -10 to -10
dm1 <- ifelse(dm1 < -10, -10, dm1)

dm1 <- [dm1>10] <- 10

## Delete duplicated rows based on one column
df1 <- df1[!duplicated(df1$Gene_symbol),]

## Make bold titles and add a little space at the baseline
g + theme(plot.title = element_text(size = 20, face="bold", margin = margin(10, 0 , 10, 0)))

## Subset dates
date = "1997-01-01"
df$year <- substring(df$date, 1, 4)
df$month <- substring(df$date, 6, 7)
