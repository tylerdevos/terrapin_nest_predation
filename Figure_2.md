## FIGURE 2

<img src="/Graphics/Figure_2.jpg" alt="Figure 2"/>

### Faceted count distribution graphs using a five day average

Activate packages:
```
library(ggplot2)
library(zoo)
library(ggridges)
library(reshape2)
```
Import data (see example data [here](https://github.com/tylerdevos/terrapin_nest_predation/blob/main/Data/count_data_2020.csv)):
```
counts_2020 <- read.csv("~/Desktop/Spring 2022/Nest predation project/Clean Data/count_data_2020.csv")
```
Convert NA values to zeros (as needed) and calculate five-day averages for three count categories ("nesting", "hatching", & "predated"):
```
counts_2020$nesters_total[is.na(counts_2020$nesters_total)] <- 0
temp.nesting <-zoo(counts_2020$nesters_total,counts_2020$julian_day)
nesting.average <- rollmean(temp.nesting, 5, fill=list(NA,NULL,NA))
counts_2020$predated_total[is.na(counts_2020$predated_total)] <- 0
temp.predation <-zoo(counts_2020$predated_total,counts_2020$julian_day)
predation.average <- rollmean(temp.predation, 5, fill=list(NA,NULL,NA))
temp.hatching <-zoo(counts_2020$hatched_total, counts_2020$julian_day)
hatching.average <- rollmean(temp.hatching, 5, fill=list(NA,NULL,NA))
```
Create new dataframe containing only averaged counts:
```
averages <- as.data.frame(counts_2020$julian_day)
averages$julian_day <- counts_2020$julian_day
averages$`counts_2020$julian_day` <- NULL
averages$nesting <- coredata(nesting.average)
averages$predated <- coredata(predation.average)
averages$hatched <- coredata(hatching.average)
```
Reformat dataframe for graphing:
```
long_averages = melt(averages, "julian_day")
```
Generate graph and export to working directory as a PDF:
```
graph_labels <- c("nesting"="Nesting Females","predated"="Predated Nests","hatched"="Emerged Nests")
p2 <- ggplot(long_averages, aes(julian_day, value, fill=variable)) +
  geom_line(show.legend=FALSE) +
  geom_density_line(stat="identity", alpha=0.3, show.legend=FALSE) +
  scale_x_continuous(breaks=seq(150,276,25)) +
  scale_y_continuous(breaks=seq(0,25,5), limits=c(0,25)) +
  facet_wrap(~variable, ncol=1, labeller=labeller(variable=graph_labels), scales="free_x") +
  scale_fill_manual(name='', values=c("nesting"="blue", "predated"="red", "hatched"="green3")) +
  labs(x="Julian Day", y="Count") +
  ggtitle("2020") +
  theme_minimal() +
  theme(plot.title=element_text(hjust=0.5), axis.line.x=element_line(), axis.line.y.left=element_line(), panel.grid.major=element_blank(), panel.grid.minor=element_blank(), text=element_text(family="Times New Roman"))
ggsave(p2, filename="2020_turts.pdf", devic=cairo_pdf, width=4.00, height=5.25, units="in")
```
