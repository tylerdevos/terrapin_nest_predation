## FIGURE 3
### Faceted count distribution graphs using a five day average + uniform overlay of two additional averaged count distributions

Activate packages:
```
library(ggplot2)
library(zoo)
library(ggridges)
library(reshape2)
library(car)
```
Import data:
```
counts_2020 <- read.csv("~/Desktop/Spring 2022/Nest predation project/Clean Data/count_data_2020.csv")
```
Convert NA values to zeros (as needed) and calculate five-day averages for three count categories ("total", "raccoon", & "skunk") and two overlay count categories ("nesting" & "hatching"):
```
temp.total <-zoo(counts_2020$predators_total,counts_2020$julian_day)
total.average <- rollmean(temp.total, 5, fill=list(NA,NULL,NA))
temp.raccoon <-zoo(counts_2020$raccoon,counts_2020$julian_day)
raccoon.average <- rollmean(temp.raccoon, 5, fill=list(NA,NULL,NA))
temp.skunk <-zoo(counts_2020$skunk, counts_2020$julian_day)
skunk.average <- rollmean(temp.skunk, 5, fill=list(NA,NULL,NA))
counts_2020$nesters_total[is.na(counts_2020$nesters_total)] <- 0
temp.nesting <-zoo(counts_2020$nesters_total,counts_2020$julian_day)
nesting.average <- rollmean(temp.nesting, 5, fill=list(NA,NULL,NA))
temp.hatching <-zoo(counts_2020$hatched_total, counts_2020$julian_day)
hatching.average <- rollmean(temp.hatching, 5, fill=list(NA,NULL,NA))
```
Create new dataframe containing only averaged counts (for categories that will be faceted):
```
averages <- as.data.frame(counts_2020$julian_day)
averages$julian_day <- counts_2020$julian_day
averages$`counts_2020$julian_day` <- NULL
averages$total <- coredata(total.average)
averages$raccoon <- coredata(raccoon.average)
averages$skunk <- coredata(skunk.average)
```
Create new dataframes containing only averaged counts (for categories that will be used as overlays on all graphs):
```
averages2 <- as.data.frame(counts_2020$julian_day)
averages2$julian_day <- counts_2020$julian_day
averages2$`counts_2020$julian_day` <- NULL
averages2$nesting <- coredata(nesting.average)
```
```
averages3 <- as.data.frame(counts_2020$julian_day)
averages3$julian_day <- counts_2020$julian_day
averages3$`counts_2020$julian_day` <- NULL
averages3$hatching <- coredata(hatching.average)
```
Reformat all dataframes for graphing:
```
long_averages = melt(averages, "julian_day")
```
```
nesting = melt(averages2, "julian_day")
nesting$variable <- NULL
```
```
hatching = melt(averages3, "julian_day")
hatching$variable <- NULL
```
Generate graph and export to working directory as a PDF:
```
graph_labels <- c("total"="Total Predator Detections","raccoon"="Raccoon Detections","skunk"="Skunk Detections")
p3 <- ggplot(long_averages, aes(julian_day, value, fill=variable)) +
  geom_line(data=nesting, aes(julian_day, value, fill=NULL, color="nesting females"), alpha=0.5, linetype="solid") +
  geom_line(data=hatching, aes(julian_day, value, fill=NULL, color="emerged nests"), alpha=0.5, linetype="solid") +
  geom_line(show.legend=FALSE) +
  geom_density_line(stat="identity", alpha=0.6, show.legend=FALSE) +
  scale_x_continuous(breaks=seq(150,276,25)) +
  scale_y_continuous(breaks=seq(0,25,5), limits=c(0,27)) +
  facet_wrap(~variable, ncol=1, labeller=labeller(variable=graph_labels), scales="free_x") +
  scale_fill_manual(name='', values=c("total"="grey90", "raccoon"="grey60", "skunk"="black")) +
  scale_color_manual(name="Terrapin Activity", values=c("nesting females" = "blue", "emerged nests" = "green4")) +
  labs(x="Julian Day", y="Count") +
  ggtitle("2020") +
  theme_minimal() +
  theme(plot.title=element_text(hjust=0.5), axis.line.x=element_line(), axis.line.y.left=element_line(), panel.grid.major=element_blank(), panel.grid.minor=element_blank(), text=element_text(family="Times New Roman"))
ggsave(p3, filename="2020_predators.pdf", devic=cairo_pdf, width=5.25, height=5.25, units="in")
```
