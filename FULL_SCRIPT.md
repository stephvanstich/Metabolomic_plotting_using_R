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

# Volvano plots using fold change and p-value tables from metabolomic study(metabolon)

```
library("MASS")
library("ggplot2")
library("calibrate")
library("ggrepel")
library("dplyr")
```
Import the [table](plasmavolcanoADI1x.txt)

```
foldchange1xplasma <- read.table("plasmavolcanoADI1x.txt", sep="\t", header=TRUE)
```
```
Color <- c (">1" = "dodgerblue4", "<1" = "red2", "p-value<=0.005 & 4-Foldchange"= "Black", "-"= " White") 
```
Setting up the statistical significance for labeling of the vaolcano plot
```
foldchange1xplasma$FoldChangeValue <- ifelse(log2(foldchange1xplasma$FoldChange) > 0, ">1", "<1")
foldchange1xplasma$pvalueSignificant <-ifelse(abs(log2(foldchange1xplasma$FoldChange))>=2 & foldchange1xplasma$pvalue <= 0.005, "p-value<=0.005 & 4-Foldchange", "-")

foldchange1xplasma.subset.sign <- subset(foldchange1xplasma, foldchange1xplasma$pvalueSignificant == "p-value<=0.005 & 4-Foldchange")
```
Volcano plot
```
pdf("Volcano_plot_ADI1x_plasma.pdf", width = 20)
ggplot(data=foldchange1xplasma, aes(x=log2(FoldChange), y=-log10(pvalue), colour= FoldChangeValue)) + xlab("log2(foldchange)") + ylab("-log10 (pvalue)") + scale_color_manual(values = Color) + labs(title = "ADI1x Plasma")+theme(text = element_text(size=20))+ geom_point(size=3, alpha = 0.5) + geom_point(data=foldchange1xplasma.subset.sign, aes(x=log2(FoldChange), y=-log10(pvalue), colour = pvalueSignificant), size = 1, shape = 8 ) + scale_color_manual(values = Color) + xlim(c(-7,7)) + ylim(c(0, 13)) + geom_text_repel(data = subset(foldchange1xplasma, abs(log2(FoldChange)) >=2 & pvalue <= 0.005),aes(label = Biochem),color = "Black", size = 5, box.padding = unit(0.35, "lines"),point.padding = unit(0.35, "lines"), segment.size = 0.1, segment.color = "Black", force =2)
dev.off()
```
Here is the Volcano [plot](Volcano_plot_ADI1x_plasma.pdf)






