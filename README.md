# Useful R <s>one-liners</s> knowledge
***
> The streamlining of the big dollar stores opens up, for other outlets, their original source of cheap merchandise: distressed goods, closeouts, overstock, salvage merchandize, department-store returns, liquidated goods, discontinued lines, clearance items, ex-catalog stock, freight-damaged goods, irregulars, salvage cosmetics, test-market items and bankruptcy inventories.

Here is my dollar-store inventory of how to do stuff in R. Plans are eventually to split this into sections (e.g. visualization with ggplot2, manipulation with dplyr, etc.) and make this `.Rmd` pretty but I may never.

## Install/load many packages at once
install.packages('needs')
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

## Subset dates
date = "1997-01-01"
df$year <- substring(df$date, 1, 4)
df$month <- substring(df$date, 6, 7)

# Detecting patterns with `str_detect()` 
library(stringr)
some_objs <- c("pen", "pencil", "marker", "spray")
str_detect(some_objs, "pen")
some_objs[str_detect(some_objs, "pen")]

strings = c("12 Jun 2002", "8 September 2004", "22-July-2009 ", "01 01 2001", "date", "02.06.2000", "xxx-yyy-zzzz", "$2,600")
dates = "([0-9]{1,2})[- .]([a-zA-Z]+)]- .]([0-9]{4})"

# Make bold titles and add a little space at the baseline
g + theme(plot.title = element_text(size = 20, face="bold", margin = margin(10, 0 , 10, 0)))

## Use other fonts in title
library(extrafont)
g+theme(plot.title = element_text(size=30,lineheight=.8, 
  vjust=1,family="Bauhaus 93"))
  
## Multi-line title?
g<-g+ggtitle("This is a longer\ntitle than expected")
g+theme(plot.title = element_text(size=20, face="bold", vjust=1, lineheight=0.6))

## Temperature in axis
y=expression(paste("Temperature ( ", degree ~ F, " )")), title="Temperature")

## Change size of and rotate axis test
g + theme(axis.text.x=element_text(angle=50, size=20, vjust=0.5))

## Treat a discrete variable like character as factor 
g<-ggplot(nmmaps, aes(date, temp, color=factor(season)))+geom_point()

## Change the title of a legend
g+theme(legend.title = element_text(colour="chocolate", size=16, face="bold"))+
  scale_color_discrete(name="This is\nchocolate season!?")

## Change the colors of fill/color
g+geom_line()+ scale_colour_manual(name='', values=c('Player1'='grey', 'Player2'='red'))

## Use Tableau colors
library(ggthemes)
g+scale_colour_tableau()

## Modify color choice with continuous variables (sequential color scheme)
g+scale_color_gradient(low="darkkhaki", high="darkgreen")

## Modify color choice with continuous variables that are normally distributed (diverging color scheme)
mid<-mean(nmmaps$o3)
ggplot(nmmaps, aes(date, temp, color=o3))+
geom_point()+
scale_color_gradient2(midpoint=mid, low="blue", mid="white", high="red" )

## Add jittered points to violin for distribution
g+geom_violin(alpha=0.5, color="gray")+
geom_jitter(alpha=0.5, aes(color=season), position = position_jitter(width = 0.1))+
coord_flip()

## Turn off legend title
g+theme(legend.title=element_blank())
