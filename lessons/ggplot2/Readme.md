---
title: "The graphic world of ggplot2"
subtitle: "Yes, there was an original ggplot, once upon a time, in a far, far away galaxy"
author: "Juliano Palacios Abrantes"
date: "02/21/2019"
output: 
  html_document: 
    keep_md: yes
---

# Introduction


This session will focus on creating plots with `ggplot2`, a great way to make graphs and data visualization in *R*. `ggplot2` is a very powerful tool to know as you can make from very basic plots to very advanced, dynamic ones. For this session we will review the basic structure of `ggplot2` (i.e. geoms, aesthetics, etc.). 

## Why using ggplot2

- `ggplot2` is part of [tidyverse](https://www.tidyverse.org). This means that you can combine functions of `dplyr`, `tidyr`and other packages with `ggplot2` using the pipe operator (%>%).

- It is very straightforward and easy to use as it has a very strong base structure

- Allows to combine multiple tasks (i.e. visualizing a third variable, changing colors, saving your plot, etc.) in ways that are easy and clean.

- Creates beautiful plots, is very flexible, and can be combined with several other packages

# Instructions

1. Session Materials. If you already gave our *Studygroup* repository on your computer then simply `Git-push`to update the materials, the `ggplot2` session is in the "lessons" folder. If you haven't forked the repository yet, click on the "*fork*" button to create an independent copy within your own GitHub account.  Alternately, click on the "clone or download" button.

2. Data files. We will use Sea Around Us [SAU](http://www.seaaroundus.org) data, feel free to go to the webpage and download any country's catch data. 

Note: if you followed step 1, the data will be already loaded

3. Packages needed. Make sure you have the following packages installed to run the documents (note, you can simply run the chunk bellow)


```r
# Function to load multiple packages, it will install them if you don't have it already
ipak <- function(pkg){
  new.pkg <- pkg[!(pkg %in% installed.packages()[, "Package"])]
  if (length(new.pkg)) 
    install.packages(new.pkg, dependencies = TRUE,repos = "http://cran.us.r-project.org")
  sapply(pkg, require, character.only = TRUE)
}


#### Library needed for this session ####
packages <- c(
  "dplyr", # Data manipulation
  "tidyr", # Data manipulation
  "ggplot2", #Nice grpahs and spatial analysis
  "rgdal",
  "RColorBrewer", # for plot colors
  "ggrepel",
  "gridExtra" # For edditing plot grids
)

ipak(packages)

##________________________________________________##
```

# Ressources

These are just some of the resources available out there, feel free to add links I've missed!

##### I am completley lost!

- [The official website](https://ggplot2.tidyverse.org/reference/index.html)

- [The official cheatsheet](https://www.rstudio.com/wp-content/uploads/2015/03/ggplot2-cheatsheet.pdf)

##### I need some inspiration!

- [The R Graph Gallery](https://www.r-graph-gallery.com/)
- [R-Visualization](http://rvisualization.com/r-scripts/)

##### I love movies 
- [The Wes Anderson Palettes](https://github.com/karthik/wesanderson)

##### I need more ggplot2!

- [Multiple Graphs in one](http://www.sthda.com/english/articles/24-ggpubr-publication-ready-plots/81-ggplot2-easy-way-to-mix-multiple-graphs-on-the-same-page/)
- [FLow Charts](http://corybrunson.github.io/ggalluvial/reference/geom_flow.html)

-
