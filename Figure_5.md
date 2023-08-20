## FIGURE 5

<img src="/Graphics/Figure_5.jpg" alt="Figure 2" width="700"/>

### Faceted series of scatterplots with linear regression lines and 95% confidence intervals for frequently observed species

Activate packages:
```
library(ggplot2)
library(dplyr)
```
Import data (see example data [here](https://github.com/tylerdevos/terrapin_nest_predation/blob/main/Data/species_predation_regression_counts.csv)):
```
reg_counts.0 <- read.csv("~/Desktop/Spring 2022/Nest predation project/Clean Data/species_predation_regression_counts.csv")
```
Remove rows with missing values:
```
reg_counts <- subset(reg_counts.0, reg_counts.0$predated_total!='NA')
```
Reorder groups to graph in a specific sequence:
```
ordered <- reg_counts
ordered$species <- factor(ordered$species, levels=c("raccoon","skunk","opossum","fox","coyote"))
```
Generate graph and export to working directory as a PDF:
```
graph_labels <- c("raccoon"="Raccoon","skunk"="Skunk","opossum"="Opossum","fox"="Fox","coyote"="Coyote")
p5 <- ggplot(ordered, aes(x=predator_count, y=predated_total, color="grey50")) +
  geom_point(pch=20, cex=2, color="black") +
  geom_smooth(data=ordered %>% filter(species %in% "raccoon"), method="lm", color="red4", lwd=0.5, linetype="dashed") +
  geom_smooth(data=ordered %>% filter(species %in% "skunk"), method="lm", color="red4", lwd=0.5, linetype="dashed") +
  facet_wrap(~species, scales='free', labeller=labeller(species=graph_labels)) +
  scale_x_continuous(limits=c(0,5), breaks=seq(0,5,1)) +
  scale_y_continuous(limits=c(0,33)) +
  labs(x="Sightings on camera", y="Predated nests") +
  theme_minimal() +
  theme(axis.line=element_line(), legend.position='none', panel.grid.major=element_blank(), panel.grid.minor=element_blank(), text=element_text(family="Times New Roman"))
ggsave(p5, filename="species_regressions.pdf", device=cairo_pdf, width=6, height=4, units="in")
```
### [<<< Back to Figure 4](https://github.com/tylerdevos/terrapin_nest_predation/blob/main/Figure_4.md) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; [To Figure 6 >>>](https://github.com/tylerdevos/terrapin_nest_predation/blob/main/Figure_6.md)
