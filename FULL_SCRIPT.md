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
```
foldchange1xplasma <- read.table("plasmavolcanoADI1x.txt", sep="\t", header=TRUE)
```
```
Color <- c (">1" = "dodgerblue4", "<1" = "red2", "p-value<=0.005 & 4-Foldchange"= "Black", "-"= " White") 
```
```
foldchange1xplasma$FoldChangeValue <- ifelse(log2(foldchange1xplasma$FoldChange) > 0, ">1", "<1")
foldchange1xplasma$pvalueSignificant <-ifelse(foldchange1xplasma$pvalue <= 0.005, "p-value<=0.005", "-")

foldchange1xplasma.subset.sign <- subset(foldchange1xplasma, foldchange1xplasma$pvalueSignificant == "p-value<=0.005")

foldchange1xplasma.subset.sign.pos <- subset(foldchange1xplasma.subset.sign, foldchange1xplasma.subset.sign$ FoldChangeValue == ">1")

foldchange1xplasma.subset.sign.neg <- subset(foldchange1xplasma.subset.sign, foldchange1xplasma.subset.sign$ FoldChangeValue == "<1")
```
```
ADI1xmerge.plasma.pos<- rbind(foldchange2xplasma.subset.sign.pos, foldchange1xplasma.subset.sign.pos)
ADI1xcommon.plasma.pos<-ADI1xmerge.plasma.pos[duplicated(ADI1xmerge.plasma.pos$Biochem_No),]

ADI1xmerge.plasma.neg<- rbind(foldchange2xplasma.subset.sign.neg, foldchange1xplasma.subset.sign.neg)
ADI1xcommon.plasma.neg<-ADI1xmerge.plasma.neg[duplicated(ADI1xmerge.plasma.neg$Biochem_No),]
```
```
pdf("Volcano plot ADI1x plasma w label on common metab.pdf", width = 15)
ggplot(data=foldchange1xplasma, aes(x=log2(FoldChange), y=-log10(pvalue), colour= log2(FoldChange))) + xlab("log2(foldchange)") + ylab("-log10 (pvalue)") + labs(title = "ADI1x Plasma")+theme(text = element_text(size=30))+ geom_point(size=5, alpha = 1) + scale_color_gradient2(low = "red", high = "deepskyblue1", mid = "slateblue", midpoint = 0) + geom_point(data=ADI1xcommon.plasma, aes(x=log2(FoldChange), y=-log10(pvalue), colour = pvalueSignificant), colour = "Black", size = 2, shape = 8 )  + xlim(c(-6,6)) + ylim(c(0, 9)) + geom_text_repel(data = ADI1xcommon.plasma, aes(label = Biochem),color = "Black", size = 5, box.padding = unit(0.35, "lines"),point.padding = unit(0.35, "lines"), segment.size = 0.1, segment.color = "Black", force =2)
dev.off()
```





