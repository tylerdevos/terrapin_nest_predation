## FIGURE 6

<img src="/Graphics/Figure_6.jpg" alt="Figure 2" width="600"/>

### Panels A & B: Count distribution graphs using a five day average + labelled vertical lines

Activate packages:
```
library(ggplot2)
library(zoo)
library(ggridges)
library(reshape2)
```
Import data (see example data [here](https://github.com/tylerdevos/terrapin_nest_predation/blob/main/Data/fence_predation_counts.csv)):
```
counts_2021 <- read.csv("~/Desktop/Spring 2022/Nest predation project/Clean Data/fence_predation_counts.csv")
```
Convert NA values to zeros (as needed):
```
counts_2021$predated_fenced[is.na(counts_2021$predated_fenced)] <- 0
```
Calculate five-day averages:
```
temp.predation <-zoo(counts_2021$predated_fenced,counts_2021$julian_day)
predation.average <- rollmean(temp.predation, 5, fill=list(NA,NULL,NA))
```
Create new dataframe containing only averaged counts:
```
averages <- as.data.frame(counts_2021$julian_day)
averages$julian_day <- counts_2021$julian_day
averages$`counts_2021$julian_day` <- NULL
averages$predated <- coredata(predation.average)
```
Reformat dataframe for graphing:
```
long_averages = melt(averages, "julian_day")
```
Specify locations and values for numeric labels:
```
annotation <- data.frame(x=c(147,176,192,208), y=c(7.25,7.25,7.25,7.25), label=c("1","2","3","4"))
```
Generate graph and export to working directory as a PDF:
```
p6A <- ggplot(long_averages, aes(julian_day, value)) +
  geom_line(show.legend=FALSE) +
  geom_density_line(stat="identity", alpha=0.3, show.legend=FALSE, fill="red") +
  scale_x_continuous(breaks=seq(150,225,25), limits=c(147,225)) +
  scale_y_continuous(breaks=seq(0,8,2), limits=c(0,8)) +
  labs(x="Julian Day", y="Count") +
  geom_vline(xintercept=147, color="red3", linetype="longdash") +
  geom_vline(xintercept=176, color="red3", linetype="longdash") +
  geom_vline(xintercept=188, color="red3", linetype="longdash") +
  geom_vline(xintercept=196, color="red3", linetype="longdash") +
  geom_vline(xintercept=204, color="red3", linetype="longdash") +
  geom_vline(xintercept=212, color="red3", linetype="longdash") +
  annotate("rect", xmin=188, xmax=196, ymin=-Inf, ymax=Inf, fill="grey", alpha=0.5) +
  annotate("rect", xmin=204, xmax=212, ymin=-Inf, ymax=Inf, fill="grey", alpha=0.5) +
  geom_label(data=annotation, aes(x=x, y=y, label=label), color="black", size=4, fontface="bold") +
  ggtitle("Predated Nests Within the Electric Fence") +
  theme_minimal() +
  theme(text=element_text(family="Times New Roman"), plot.title=element_text(hjust=0.5), axis.line.x=element_line(), axis.line.y.left=element_line(), panel.grid.major=element_blank(), panel.grid.minor=element_blank())
ggsave(p6A, filename="predated_fenced.pdf", device=cairo_pdf, width=3.2, height=2, units="in")
```

### Panel C: Line graph + labelled vertical lines
Activate packages:
```
library(ggplot2)
library(zoo)
library(ggridges)
library(reshape2)
library(car)
```
Import data (see example data [here](https://github.com/tylerdevos/terrapin_nest_predation/blob/main/Data/predator_locations.csv)):
```
predator_locations <- read.csv("~/Desktop/Spring 2022/Nest predation project/Clean Data/predator_locations.csv")
```
Specify locations and values for numeric labels:
```
annotation <- data.frame(x=c(4,6,8.5), y=c(0.875,0.875,0.875), label=c("2","3","4"))
```
Generate graph and export to working directory as a PDF:
```
p6C <- ggplot(predator_locations, aes(week, proportion_inside_fence, color=species)) +
  geom_vline(xintercept=4, color="red3", linetype="longdash") +
  geom_vline(xintercept=6, color="red3", linetype="longdash") +
  geom_vline(xintercept=8.5, color="red3", linetype="longdash") +
  geom_line() +
  geom_point() +
  geom_label(data=annotation, aes(x=x, y=y, label=label), color="black", size=4, fontface="bold") +
  scale_x_continuous(breaks=seq(1,11,1)) +
  scale_y_continuous(breaks=seq(0,1, 0.1), limits=c(0,1)) +
  scale_color_manual(name="Species", values=c("raccoon"="grey60", "skunk"="black")) +
  labs(x="Week", y="Proportion observed within fence boundary") +
  theme_minimal() +
  theme(plot.title=element_text(hjust=0.5), axis.line.x=element_line(), axis.line.y.left=element_line(), panel.grid.major=element_blank(), panel.grid.minor=element_blank(), text=element_text(family="Times New Roman"))
ggsave(p6C, filename="fence_positions.pdf", device=cairo_pdf, width=7, height=3.3, units="in")
```
### [<<< Back to Figure 5](https://github.com/tylerdevos/terrapin_nest_predation/blob/main/Figure_5.md)
