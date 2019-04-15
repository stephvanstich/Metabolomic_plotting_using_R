# **Welcome to metabolomic plotting using R**

In a folder named /R create a table with the metabolites using the following column(see example [here]())

-	First column: PARAM_SUBJECT_ID (sample ID)
-	Second column: PARAM_TX (Treatment/genotype…)
-	Third column to end: Metabolites

Installing packages
```
```

Open library
```
library(devtools)
library(ggbiplot)
library(ggplot2)
library(ellipse)
library(ggfortify)
library(vegan)
```

#Set up environment:

```
getwd()
setwd("/R")
getwd()
```
Colors<-c("Ctrl"="#17202a","ADI1x"="#f1c40f","ADI2x"="#cb4335")


#Import data table in R:


plasma <- read.table("plasma.txt", header = TRUE, row.names = 1, check.names=F)


