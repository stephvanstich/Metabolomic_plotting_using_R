# **Welcome to metabolomic stats & plotting using R**

In a folder named /R create a table with the metabolites using the following column(see example [here]())

-	First column: PARAM_SUBJECT_ID (sample ID)
-	Second column: PARAM_TX (Treatment/genotype…)
-	Third column to end: Metabolites

Installing packages
```
install.packages("ggfortify")
install.packages("ggplot2")
install.packages("ellipse")
```

Open library

```
library(ggfortify)
library(ggplot2)
library(ellipse)
```


Set up environment:

```
Colors<-c("Ctrl"="#17202a","ADI1x"="#f1c40f","ADI2x"="#cb4335")
```

Import data table

```
plasma <- read.table("plasma.txt", header = TRUE, row.names = 1, check.names=F)
```
# Distance matrix and PCA plot

Assign Value to the different part of the table:

```
treatment.plasma<-plasma[,1]
met.plasma<-plasma[,2:180]
```

calculate distance matrice and plot the PCA
```
log.met.plasma<-log(met.plasma)

PCA.plasma<-prcomp(log.met.plasma)


pdf("PCA plasma.pdf", width = 20)
ggplot(PCA.plasma, aes(PC1, PC2, color = treatment.plasma))  + stat_ellipse(geom = "polygon", alpha = 0.2, size = 2 ,linetype =2, aes(fill = treatment.plasma, color = treatment.plasma)) + geom_point(size = 10) + theme(text = element_text(size=30)) + scale_color_manual( values = Colors) + scale_fill_manual ( values=Colors)
dev.off()
```

# ADONIS test

```
adonis(log.met.plasma ~ PARAMTX,plasma, method='eu')
```
# Statistics

```
plasma_stat_treatment<-describeBy(plasma,plasma$PARAMTX,mat=TRUE)
plasma_stat_treatment<-plasma_stat_treatment[-c(1,2,3), ]
write.table(plasma_stat_treatment,"plasma_stat_treatment.txt",sep="\t")
```
