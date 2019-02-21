---
title: "The graphic world of ggplot2"
author: "Juliano Palacios Abrantes"
date: "02/21/2019"
output: 
  html_document: 
    keep_md: yes
---

# Libraries and data


```r
# Library ####

# Function to load multiple packages, it will install any that you don't have
ipak <- function(pkg){
  new.pkg <- pkg[!(pkg %in% installed.packages()[, "Package"])]
  if (length(new.pkg)) 
    install.packages(new.pkg, dependencies = TRUE,repos = "http://cran.us.r-project.org")
  sapply(pkg, require, character.only = TRUE)
}


#### Packages needed for this session ####
packages <- c(
  "dplyr", # Data manipulation
  "tidyr", # Data manipulation
  "ggplot2", #Nice grpahs and spatial analysis
  "rgdal",
  "RColorBrewer" # for plot colors
)

ipak(packages)

##________________________________________________##

# Data ####

# Load Sea Around Us catch data for Alaska
SAU_Alaska <- read.csv("~/GitHub/studyGroup/lessons/Lessons_Data/SAU_Alaska.csv")

Clean_Data <- SAU_Alaska %>% 
  group_by(year,area_name,scientific_name) %>% 
  summarise_if(is.numeric,sum,na.rm=T)
```


# The basic ggplot2 structure

OK, `ggplot2`follows a very simple structure made by to main components: 

- the aesthetics you want to plot (`aes()`);
- the geometric form you want to represent (i.e. `geom_point()`).

So any basic plot will have to have, at least, these two components, however, there are several ways to create that first plot. Note, in the example below, that ggplot2 uses the "+" sign to add components to the plot 


```r
# The basic structure
ggplot(data = Clean_Data, # tell ggplot your data source
       aes(x = year, # Note that the aesthetics set your x and y axis
           y = tonnes)
       ) + # ggplot "adds" components 
  geom_point() # finally, included the geometrics (points in this case)
```

![](ggplot2_session_files/figure-html/The_Basic_Structure-1.png)<!-- -->

```r
# Alternative way.

# I prefere this version because it allows to clearly see each layer you include to the plot

ggplot(data = Clean_Data) +
  geom_point( # Include everything within geom_point()
    aes(x = year, 
        y = tonnes)
    )  
```

![](ggplot2_session_files/figure-html/The_Basic_Structure-2.png)<!-- -->

```r
# And it can be as simple as...

ggplot(Clean_Data) + aes(year,tonnes) + geom_point()
```

![](ggplot2_session_files/figure-html/The_Basic_Structure-3.png)<!-- -->

## Geometrics 

Ggplot2 offers a laaaaaaarge variety of geoms forms. From points, to lines, to maps to words, I keep finding new things to plot with `ggplot2`. Here are some of the basic Geom, check out the [official webpage](https://ggplot2.tidyverse.org/reference/index.html#section-layer-geoms) for a more complete list:

- `geom_point`(); scatter plots
- `geom_line`(); Line plots
- `geom_histogram`(); histograms, *note that these only need x*
- `geom_bar`(); Bar plots, this one is a little tricky, normally needs a "stat" component
- `geom_boxplot`(); Box plots

One of the beauties of `ggplot2` is that most of these (and the others) geometric use the same structure so you can easily change from one to another, according on your preference.


```r
# You can change between geometrics by simply commenting of the undesired one! 

ggplot(data = Clean_Data) + # call ggplot with your data
  geom_point(
    # geom_line(
    aes(x = year, 
        y = tonnes)
    )
```

![](ggplot2_session_files/figure-html/Geometrics-1.png)<!-- -->

```r
# Some of them only use x
ggplot(data = Clean_Data) + # call ggplot with your data
  geom_histogram(
    # geom_bar(
    aes(x = year)
    )
```

```
## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.
```

![](ggplot2_session_files/figure-html/Geometrics-2.png)<!-- -->


## Aesthetics

Another important component of ggplot2 are the aesthetics. 


```r
# I can combine functions within the aesthetics such as.

ggplot(data = subset(Clean_Data, scientific_name %in% c("Oncorhynchus kisutch","Oncorhynchus nerka","Oncorhynchus gorbuscha"))) + # I can subset my data within ggplot
  geom_point(
    aes(x = year/10, # I can devide variables
        y = log10(tonnes)) # plot tonnes in a log scale
    )  # ggplot "adds"" compo
```

![](ggplot2_session_files/figure-html/Aesthetics-1.png)<!-- -->

```r
# I can also change the order of axis...

ggplot(data = subset(Clean_Data, scientific_name  == "Oncorhynchus kisutch")) + # I choose one species
  geom_point(
    aes(x = reorder(year, tonnes), # Reorder the years from lowest catch to highest
        # x = reorder(year, desc(tonnes)), # desc will invert it!
        y = tonnes
        )
    )
```

![](ggplot2_session_files/figure-html/Aesthetics-2.png)<!-- -->

```r
# And other characteristics like size, transparency, type, etc...

ggplot(data = subset(Clean_Data, scientific_name  == "Oncorhynchus kisutch")) + # I choose one species
  geom_point(
    aes(x = year,
        y = tonnes,
        size = tonnes, # size
        alpha = year #transparency
        )
    )
```

![](ggplot2_session_files/figure-html/Aesthetics-3.png)<!-- -->


## Add some colors

`ggplot2`gives you the option to color all your variables or to color them by some third variable. Note that, when you want all of your points in one same color you have to call `color` (or `colour`, `ggplot2`does not discriminate!) outside the aesthetics (`aes()`). Alternatively, if you want to, say, set a color by species, this has to be within the `aes()`


```r
# Data for example

Example_Data <- Clean_Data %>% 
  filter(scientific_name %in% c("Oncorhynchus kisutch","Oncorhynchus nerka","Oncorhynchus gorbuscha"))

# ________________ #

# All points in one color
ggplot(data = Example_Data) + 
  geom_point(
    aes(x = year, 
        y = tonnes), 
    # colour = "red", # R is inclusive! 
    color = "blue"
    )  
```

![](ggplot2_session_files/figure-html/Colors-1.png)<!-- -->

```r
# Set color by species
ggplot(data = Example_Data) + 
  geom_point(
    aes(x = year, 
        y = tonnes,
        color = scientific_name
        ) # Note that we closed aes here!
    )
```

![](ggplot2_session_files/figure-html/Colors-2.png)<!-- -->

## Adding components

Now, the way `ggplot2` works is that you can keep adding components to your initial plot, lets see how this works by first naming our previews plot:


```r
# Base plot
P_One <- ggplot(data = Clean_Data) + # call ggplot with your data
  geom_point(
    aes(x = year, # Note that the aesthetics set your x and y variables
        y = tonnes/10000),
    colour = "grey"
    )

# Now if I call "P_One" I will ahve my plot
# P_One

## Now lets say we want to hilight the previous species in this plot...

P_One + 
  geom_line( data = Example_Data,
    aes(x = year, 
        y = tonnes/10000,
        color = scientific_name
        ) # Note that we closed aes here!
    )
```

![](ggplot2_session_files/figure-html/Adding_to_Plot-1.png)<!-- -->

```r
# You can also re-name your new plot

P_Two <- P_One + 
  geom_line( data = Example_Data,
    aes(x = year, 
        y = tonnes,
        color = scientific_name
        ) # Note that we closed aes here!
    )

# Call me! 
# P_Two
```

# Making your plot look B-E-A-utiful! 

## Axis

You can change, modify and edit all your scales from the actual number to the title of the axis. There are several ways that you can work out your plot edits, I use the `labs()` function, I think is more complete, but you have other alternatives. The `labs()`function has many options like:

 - Title and subtitle
- x and y axis titles
- Legend title
- Figure caption
- Figure tag; (i.e. Plot A and Plot B)

* Note: to exclude a label you can set it to NULL (i.e. `labs(x = NULL)`) or to blank (i.e. `labs(x = "")`)

![](ggplot2_session_files/figure-html/Axis_Labels-1.png)<!-- -->

We can also change the scales of our plot using `lims()`, `xlim()`, or/and `ylim()`.

* Note: when you limit your scales ggplot2 will give you a warning message that your plot is missing values, something like: `Removed 3908 rows containing missing values (geom_point).`



```
## Warning: Removed 3836 rows containing missing values (geom_point).
```

```
## Warning: Removed 150 rows containing missing values (geom_path).
```

![](ggplot2_session_files/figure-html/Axis_Scales-1.png)<!-- -->


## The "scales" family

There are over 50 functions that control different aspects of your scales and plot looks like the color of categories, titles, shapes, limits, steps, transparency, etc. The scale family can be used in many (all?) aesthetics, just match the aesthetic with the scale you want:

- `scale_fill_`, if you used `aes(fill = Variable)`
- `scale_color_`, if you used `aes(color = Variable)`
- `scale_alpha_`
- `scale_x_`, used to eddit breaks and limits on the x axis
- `scale_y_`, used to eddit breaks and limits on the y axis
A complete list can be found in the [Official Website](https://ggplot2.tidyverse.org/reference/index.html#section-scales). In addition, `scale_colour_brewer()`, provides a great set of pallets.
- 

![](ggplot2_session_files/figure-html/Scales_family-1.png)<!-- -->

```
## Warning: Removed 3836 rows containing missing values (geom_point).
```

```
## Warning: Removed 150 rows containing missing values (geom_path).
```

![](ggplot2_session_files/figure-html/Scales_family-2.png)<!-- -->


## Themes

The `theme()` function controls the overall appearance of the plot (background, grids, axis size, etc.). There are some pre made themes loaded in `ggplot2` like `theme_bw()` `theme_classic()` `theme_dark()` `theme_minimal()` and `theme_void()`. However, is you don't like any of those, or you want a very specific thing, you can also modify, any aspect of your plot. At the begging sounds horrible to write a line of code for every singly small detail (If you think about it it s the same as excel, is just that in excel you click and here we write).




```r
# Check out some of the themes
P_Three +
  # theme_bw() +
  # theme_classic() +
  # theme_dark() +
  # theme_minimal() 
  theme_void()
```

![](ggplot2_session_files/figure-html/Themes-1.png)<!-- -->

```r
# Manually change things
  P_Three + theme(
    plot.title = element_text(face = "bold",
                              colour = "blue"),
    plot.subtitle = element_text(face = "italic",
                                 colour = "lightblue"),
    panel.background = element_blank(), # clean background
    strip.background = element_blank(), # clean background
    panel.border     = element_blank(), # No pannel border
    panel.grid.minor = element_blank(), # No grids
    panel.grid.major = element_blank(), # No grids
    axis.ticks = element_blank(),
    axis.text.x = element_text(size = 10,
                               angle = 0,
                               face = "plain"),
    axis.text.y = element_text(size = 12),
    axis.title = element_text(size = 14),
    legend.key = element_rect(colour = "black", # Around boxes
                              fill = "grey90"), # legend boxes
    legend.position  = "bottom",
    legend.title = element_text(size = 12),
    legend.text = element_text(size = 10)
  )
```

![](ggplot2_session_files/figure-html/Themes-2.png)<!-- -->


### Create your own theme

So now, imagine you have a paper, your thesis or any other document where you are presenting > 5 plots, how annoying would it be to make sure they look the same in excel? Even worst, copy and paste that long-ass code and then change x5 every decision. Well, fear do not! We can create a function for that!!!!


![](ggplot2_session_files/figure-html/ggtheme_plot_fun-1.png)<!-- -->![](ggplot2_session_files/figure-html/ggtheme_plot_fun-2.png)<!-- -->

## Multiple plots in one

There are many ways to create two plots in one. We will go trough the basic version build in Ggplot2, the `facet`family (e.g. `facet_wrap()` or `facet_grid()`). However, there are more advanced options (allow for more flexibility) such as the `gridExtra` and `cowplot` packages. A nice tutorial for these two can be found [Here](http://www.sthda.com/english/articles/24-ggpubr-publication-ready-plots/81-ggplot2-easy-way-to-mix-multiple-graphs-on-the-same-page/)

![](ggplot2_session_files/figure-html/Facets-1.png)<!-- -->![](ggplot2_session_files/figure-html/Facets-2.png)<!-- -->![](ggplot2_session_files/figure-html/Facets-3.png)<!-- -->![](ggplot2_session_files/figure-html/Facets-4.png)<!-- -->

## One nice plot to end the day


```r
### Example Data ###

Example_Data <- SAU_Alaska %>% 
  group_by(year,area_name,scientific_name,fishing_sector) %>% 
  summarise_if(is.numeric,sum,na.rm=T) %>% 
  filter(scientific_name %in% c("Oncorhynchus kisutch","Oncorhynchus nerka","Oncorhynchus gorbuscha")) %>% 
  filter(fishing_sector != "Subsistence")

# Set te axis ticks
Breaks <- seq(1950,2010,10)

Final_Plot <- ggplot() +
  # First we creat main plot
  geom_line( data = Example_Data,
             aes(x = year, 
                 y = tonnes/1000,
                 colour = scientific_name
             ) 
  # Facet by fishing sector
  ) + facet_wrap(~fishing_sector, 
                 ncol = 3, 
                 scales = "free_y", 
                 strip.position = "top") + 
  # Now we include labels
  labs(title = "Landings statistics for Alaska between 1951 - 2010", 
       subtitle = "Presenting pink, coho , and sockeye salmon by sector",
       x = "Year \n(1951 - 2010)",
       y = "Total landings (tonnes)",
       color = "Species",
       caption = "*Data from The Sea Around Us",
       tag = "A") +
  # Choose your scale colour abd breaks
  scale_color_brewer(palette = "Set1") +
  scale_x_continuous(breaks = Breaks) + 
  # Now we eddit the apearence 
  theme(
    plot.title = element_text(face = "bold",
                              colour = "blue"),
    plot.subtitle = element_text(face = "italic",
                                 colour = "blue"),
    panel.background = element_blank(), 
    strip.background = element_rect(colour="black",
                                    fill = "lightgrey"), 
    panel.border     = element_blank(), 
    panel.grid.minor = element_line(colour="grey95"),
    panel.grid.major = element_blank(), 
    axis.ticks = element_line(colour = "black"),
    axis.text.x = element_text(size = 10,
                               angle = 45,
                               hjust = 1,
                               face = "plain"),
    axis.text.y = element_text(size = 8),
    axis.title = element_text(size = 14),
    legend.key = element_blank(),
    legend.position  = "bottom",
    legend.title = element_text(size = 12),
    legend.text = element_text(size = 10)
  )
  
# Run me!
Final_Plot
```

![](ggplot2_session_files/figure-html/The_Final_Plot-1.png)<!-- -->

```r
# And now we save our plot!

Plot_Name <- "Example_Plot.png"
Path_Save <- "./" # some path where you want to save the plot

ggsave(Plot_Name,
       plot = Final_Plot, # or you can say plot = last_plot()
       width = 9,
       height = 5,
       units = "in",
       path = Path_Save)
```



# Some other cool stuff you can do with ggplot2

So `ggplot2` is a powerful package that can create beautiful graphs, but it has also evolved in something way more powerful. Because it is part of the Tydyverse family, you can combine your data manipulation and pipe it ( %>% ) all the way to your final graph! You can also make dynamic plots with the `plotty` package; maps with `geom_sf()`, `geom_tile()` and `geom_polygon()`; and recently I discovered that you can make GIFs with `gganimate`. Here follow some cool things you can do!

## You can combine your data wrangling with a plot


```r
#  We can combine data wrangling with dplyr and tidyr with ggplot2 on a clean way!

# Start with data wrangling
SAU_Alaska %>% 
  group_by(year,area_name,scientific_name,fishing_sector) %>% 
  summarise_if(is.numeric,sum,na.rm=T) %>% 
  filter(scientific_name %in% c("Oncorhynchus kisutch","Oncorhynchus nerka","Oncorhynchus gorbuscha")) %>% 
  filter(fishing_sector != "Subsistence") %>% 
  # And then include your plot
  ggplot(., # The . in R means that you're using the data piped in the code
         aes(x = year, 
             y = tonnes,
             colour = scientific_name
         )
  ) + # notice that once we switch to ggplot2 we use + again
  geom_point() # Notice how we changed the ggplot structure now
```

![](ggplot2_session_files/figure-html/Dplyr_ggplot2-1.png)<!-- -->


## Mapping has never been this easy! 

You literally need two lines of code in ggplot2 to plot a map *NOTE: This chunk requires you to have a speciefic shapefile.*


```r
# Three lines of code is all you need!

# requires
# library(tools) # for file_path_sans_ext()

# First we read in the shapefile
path_world <- "~/Documents/UBC/Oceans_Project/Manuscript/Data/Spatial_Data/TM_WORLD_BORDERS"
file_name <- "TM_WORLD_BORDERS"

World_Land_sf <-st_read(dsn = path_world, 
                         layer = file_path_sans_ext(file_name)
)

# I'm gpoing to subset for fast plotting

Subset_Map <- World_Land_sf %>% 
  filter(NAME %in% c("Brazil","Argentina","Chile","Uruguay","Bolivia","Peru"))

# And now we plot with ggplot2
ggplot(Subset_Map) +
  geom_sf()
```


## Move your grpahs for presenations! 

`ggplot2` combines with `gganimate` to create dynamic plots, perfect for presentations! This is just a small example but you can find a more complete tutorial [Here](https://github.com/eco-data-science/eds-ggplot2-gganimate/blob/master/gganimate_key.Rmd) *Note that this chunk needs extra packages that you will have to install and load before runing it*


