# Useful R <s>one-liners</s> knowledge
***
> The streamlining of the big dollar stores opens up, for other outlets, their original source of cheap merchandise: distressed goods, closeouts, overstock, salvage merchandize, department-store returns, liquidated goods, discontinued lines, clearance items, ex-catalog stock, freight-damaged goods, irregulars, salvage cosmetics, test-market items and bankruptcy inventories.

Here is my dollar-store inventory of how to do stuff in R. Plans are eventually to split this into sections (e.g. visualization with ggplot2, manipulation with dplyr, etc.) and make this `.Rmd` pretty but I may never.

## Install/load many packages at once
```{r}
install.packages('needs')
library(needs)
needs(rmarkdown, ggplot2, readr, tidyr, stringr)
```

## Add more color's to brewer.pal's 9 color limit with colorRampPalette
```{r}
color = colorRampPalette(rev(brewer.pal(n = 9, name = "BuPu")))(100)
```

## Pass the dots function
```{r}
examine_df <- function(df, ...){ # "..." works for multiple items
   dplyr::group_by(...) %>%
   dplyr::summarise()
}
examine_df(mtcars, mpy, cyl)
```

## Rename a column 
```{r}
cars >%
   filter(speed > 20) %>%
   rename(high_speed = speed)
```

## `add_tally()` is short-hand for `mutate()`
```{r}
mtcars %>% 
   add_tally()
```

## `count()` is short-hand for `group_by()` and `tally()`
```{r}
mtcars %>% 
   add_tally()
```

## `add_count()` is short-hand for `group_by()` and `add_tally()` and is useful for groupwise filtering (e.g. show only species with a single member)
```{r}
starwars %>%
  add_count(species) %>%
  filter(n == 1)
```

## Examine your numeric columns
```{r}
diamonds %>%
   dplyr::select_if(is.numeric) %>%
   skimr::skim()
```
![](https://ibb.co/YQwFw13)

## Conditional statement to replace an erroneous date with a missing value
```{r}
library(lubridate)
library(dplyr)
y <- mdy("1/23/2016", "3/2/2016", "12/1/1901", "11/23/2016")
if_else( year(y) != 2016, mdy(NA), y) # wrapping NA in date function mdy() forcces it into a date object

y <- c("apple", "banana", "banana", "pear", "apple")
if_else( y == "banana", as.character(NA), y)
```

## Changing values based on multiple conditions 
### if multiple sets of conditions are to be tested, nested if/else statements become cumbersome and are prone to clerical error.
```{r}
z <- c("deg","F","C", "Temperature")
dplyr::case_when(z == "deg" ~ "Degree",
          z == "F" ~ "Fahrenheit",
          z == "C" ~ "Celsius",
          TRUE ~ z)
```

## summarise all combinations of grouping variables at once

```{r}
mtcars %>%
  dplyr::group_by(cyl, gear, am) %>%
  armgin::margins(mpg = mean(mpg),
          hp = min(hp)) %>%
  print(n = Inf)
#> # A tibble: 21 x 5
#> # Groups:   cyl [3]
#>      cyl  gear    am   mpg    hp
#>    <dbl> <dbl> <dbl> <dbl> <dbl>
#>  1     4    NA    NA  26.7    52
#>  2     6    NA    NA  19.7   105
#>  3     8    NA    NA  15.1   150
#>  4     4     3    NA  21.5    97
#>  5     4     4    NA  26.9    52
#>  6     4     5    NA  28.2    91
#>  7     6     3    NA  19.8   105
#>  8     6     4    NA  19.8   110
#>  9     6     5    NA  19.7   175
#> 10     8     3    NA  15.0   150
#> 11     8     5    NA  15.4   264
#> 12     4     3     0  21.5    97
#> 13     4     4     0  23.6    62
#> 14     4     4     1  28.0    52
#> 15     4     5     1  28.2    91
#> 16     6     3     0  19.8   105
#> 17     6     4     0  18.5   123
#> 18     6     4     1  21     110
#> 19     6     5     1  19.7   175
#> 20     8     3     0  15.0   150
#> 21     8     5     1  15.4   264
```
Output individual data frames with `bind = FALSE`.
```{r}
mtcars %>%
  dplyr::group_by(cyl, gear, am) %>%
  armgin::margins(mpg = mean(mpg),
          hp = min(hp),
          bind = FALSE)
#> [[1]]
#> # A tibble: 3 x 3
#> # Groups:   cyl [3]
#>     cyl   mpg    hp
#>   <dbl> <dbl> <dbl>
#> 1     4  26.7    52
#> 2     6  19.7   105
#> 3     8  15.1   150
#> 
#> [[2]]
#> # A tibble: 8 x 4
#> # Groups:   cyl, gear [8]
#>     cyl  gear   mpg    hp
#>   <dbl> <dbl> <dbl> <dbl>
#> 1     4     3  21.5    97
#> 2     4     4  26.9    52
#> 3     4     5  28.2    91
#> 4     6     3  19.8   105
#> 5     6     4  19.8   110
#> 6     6     5  19.7   175
#> 7     8     3  15.0   150
#> 8     8     5  15.4   264
#> 
#> [[3]]
#> # A tibble: 10 x 5
#> # Groups:   cyl, gear, am [10]
#>      cyl  gear    am   mpg    hp
#>    <dbl> <dbl> <dbl> <dbl> <dbl>
#>  1     4     3     0  21.5    97
#>  2     4     4     0  23.6    62
#>  3     4     4     1  28.0    52
#>  4     4     5     1  28.2    91
#>  5     6     3     0  19.8   105
#>  6     6     4     0  18.5   123
#>  7     6     4     1  21     110
#>  8     6     5     1  19.7   175
#>  9     8     3     0  15.0   150
#> 10     8     5     1  15.4   264
```

## Use `if_else` `case_when` and `recode` inside a `mutate` function
```{r}
dat1 <- dat %>% 
  mutate(Country = recode(Country, "Canada" = "CAN",
                                   "United States of America" = "USA"),
         Type = case_when(Source == "Calculated data" ~ 1,
                          Source == "Official data" ~ 2,
                          TRUE ~ 3)) 
head(dat1)  
```

## Outputting a vector instead of a table using `pull()`
```{r}
dat %>% 
  filter(Crop == "Oats",
         Information == "Yield (Hg/Ha)") %>% 
  summarise(Oats_sum = sum(Value)) %>% 
  pull()
```

          
## Replace NA factor levels
```{r}
library(forcats)
x <- fct_explicit_na(x, na_level = "Other")
```

## If a column has many measurements for one variable (duplicate row names with different values in different column) group them and take the sum
```{r}
if (!require("dplyr")) {
   install.packages("dplyr", dependencies = TRUE)
   library(dplyr)
}

df1 <- df1 %>% group_by(ensembl_gene_name) %>% summarise(sum(dBet6_1h))
```

## Merge multiple columns from different datasets together based on similar row values in a column
```{r}
m1 <- merge(df1, df2, "ensembl_gene_name")
```

## Turn +/- Inf values into some arbitrary number
```{r}
dm1[dm1==Inf] <- 2
dm1[dm1==-Inf] <- -2
```

## Turn numbers greater than 10 to 10 and numbers smaller than -10 to -10
```{r}
dm1 <- ifelse(dm1 < -10, -10, dm1)
dm1 <- [dm1>10] <- 10
```

## Delete duplicated rows based on one column
```{r}
df1 <- df1[!duplicated(df1$Gene_symbol),]
```

## Subset dates
```{r}
date = "1997-01-01"
df$year <- substring(df$date, 1, 4)
df$month <- substring(df$date, 6, 7)
```

# Trim extra whitespaces with a single blank space
```{r}
stringr::str_replace_all(text, pattern = "\\s+", " ")
```

# Detecting patterns with `str_detect()` 
```{r}
library(stringr)
some_objs <- c("pen", "pencil", "marker", "spray")
str_detect(some_objs, "pen")
some_objs[str_detect(some_objs, "pen")]

strings = c("12 Jun 2002", "8 September 2004", "22-July-2009 ", "01 01 2001", "date", "02.06.2000", "xxx-yyy-zzzz", "$2,600")
dates = "([0-9]{1,2})[- .]([a-zA-Z]+)]- .]([0-9]{4})"
```

# Reverse a string by characters
```{r}
reverse_chars <- function(string){
   string_split = strsplit(as.character(string), split = "")
   reversed_split = string_split[[1]][nchar(string):1]
   paste(reversed_split, collapse="")
   }
```

# Reverse a string by words
```{r}
reverse_words <- function(string){
   string_split = strsplit(as.character(string), split = " ")
   string_length = length(string_split[[1]])
   if (string_length == 1) {
      reversed_string = string_split[[1]]
     }
   else {
      reversed_split = string_split[[1]][string_length:1]
      reversed_string = paste(reversed_split, collapse = " ")
   }
   return(reversed_string)
}

lapply(c("the big bang theory", "atmosphere"), reverse_words)
```

# Make bold titles and add a little space at the baseline
```{r}
g + theme(plot.title = element_text(size = 20, face="bold", margin = margin(10, 0 , 10, 0)))
```
## Use other fonts in title
```{r}
library(extrafont)
g+theme(plot.title = element_text(size=30,lineheight=.8, 
  vjust=1,family="Bauhaus 93"))
```

## Multi-line title?
```{r}
g<-g+ggtitle("This is a longer\ntitle than expected")
g+theme(plot.title = element_text(size=20, face="bold", vjust=1, lineheight=0.6))
```

## Temperature in axis
```{r}
y=expression(paste("Temperature ( ", degree ~ F, " )")), title="Temperature")
```

## Change size of and rotate axis test
```{r}
g + theme(axis.text.x=element_text(angle=50, size=20, vjust=0.5))
```

## Treat a discrete variable like character as factor 
```{r}
g<-ggplot(nmmaps, aes(date, temp, color=factor(season)))+geom_point()
```

## Change the title of a legend
```{r}
g+theme(legend.title = element_text(colour="chocolate", size=16, face="bold"))+
  scale_color_discrete(name="This is\nchocolate season!?")
```

## Change the colors of fill/color
```{r}
g+geom_line()+ scale_colour_manual(name='', values=c('Player1'='grey', 'Player2'='red'))
```

## Use Tableau colors
```{r}
library(ggthemes)
g+scale_colour_tableau()
```

## Modify color choice with continuous variables (sequential color scheme)
```{r}
g+scale_color_gradient(low="darkkhaki", high="darkgreen")
```
## Modify color choice with continuous variables that are normally distributed (diverging color scheme)
```{r}
mid<-mean(nmmaps$o3)
ggplot(nmmaps, aes(date, temp, color=o3))+
geom_point()+
scale_color_gradient2(midpoint=mid, low="blue", mid="white", high="red" )
```

## Add jittered points to violin for distribution
```{r}
g+geom_violin(alpha=0.5, color="gray")+
geom_jitter(alpha=0.5, aes(color=season), position = position_jitter(width = 0.1))+
coord_flip()
```

## Turn off legend title
```{r}
g+theme(legend.title=element_blank())
```
