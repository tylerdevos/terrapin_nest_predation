## FIGURE 5
### Faceted series of scatterplots with linear regression lines and 95% confidence intervals

<img src="/Graphics/Figure_5.jpg" alt="Figure 2"/>

Activate packages:
```
library(ggplot2)
```
Import data:
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
p5 <- ggplot(ordered, aes(x=predator_count, y=predated_total, fill=species)) +
  geom_point(pch=21, cex=2) +
  geom_smooth(method="lm", color="red4", lwd=0.5, linetype="dashed") +
  facet_wrap(~species, scales='free') +
  scale_x_continuous(limits=c(0,5), breaks=seq(0,5,1)) +
  scale_y_continuous(limits=c(0,33)) +
  labs(x="Sightings on camera", y="Predated nests", color="Species") +
  scale_fill_manual(values=c('#b8b8b8','#353535','#db8098','#c1701f','#d0b98b')) +
  theme_minimal() +
  theme(axis.line=element_line(), legend.position='none', panel.grid.major=element_blank(), panel.grid.minor=element_blank(), text=element_text(family="Times New Roman"))
ggsave(p5, filename="species_regressions.pdf", devic=cairo_pdf, width=6, height=4, units="in")
```
