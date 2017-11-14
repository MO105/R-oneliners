#R-oneliners

Useful R one-liners for bioinformatics

# Add more color's to brewer.pal's 9 color limit with colorRampPalette
color = colorRampPalette(rev(brewer.pal(n = 9, name = "BuPu")))(100)

# Rename a column 
if (!require("plyr")) {
   install.packages("plyr", dependencies = TRUE)
   library(plyr)
}

df1 <- plyr::rename(df1,c("log2FC"="dBet6_1h"))

# If a column has many measurements for one variable (duplicate row names with different values in different column) group them and take the sum
if (!require("dplyr")) {
   install.packages("dplyr", dependencies = TRUE)
   library(dplyr)
}

df1 <- df1 %>% group_by(ensembl_gene_name) %>% summarise(sum(dBet6_1h))

# Merge multiple columns from different datasets together based on similar row values in a column
m1 <- merge(df1, df2, "ensembl_gene_name")

# Turn +/- Inf values into some arbitrary number
dm1[dm1==Inf] <- 2
dm1[dm1==-Inf] <- -2

# Turn numbers greater than 10 to 10 and numbers smaller than -10 to -10
dm1 <- ifelse(dm1 < -10, -10, dm1)
dm1 <- [dm1>10] <- 10

# Delete duplicated rows based on one column
df1 <- df1[!duplicated(df1$Gene_symbol),]
