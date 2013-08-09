---
title       : Introduction to Radyant
subtitle    : Marketing analytics using Shiny
author      : Vincent Nijs
job         : Assistant Professor of Marketing, Rady School of Management, UCSD
framework 	: impressjs
widgets     : [mathjax]     # {mathjax, quiz, bootstrap}
highlighter : highlight.js  # {highlight.js, prettify, highlight}
mode  			: selfcontained





--- #title x:-700 y:-700

__Introduction to Radyant__<br>
_Marketing analytics using Shiny_

#### Credits

* [Rady](http://rady.ucsd.edu)
* [Radyant code](http://github.com/mostly-harmless/radyant)
* [Presentation](http://github.com/mostly-harmless/mostly-harmless.github.io)
* [Slidify](http://www.slidify.org)
* [impress.js](http://github.com/bartaz/impress.js)

--- #me x:700 y:-700

Vincent Nijs<br>
Assistant Professor of Marketing<br>
Rady school of Management, UCSD<br>
vnijs@rady.ucsd.edu

--- #mgt202 x:-1050 y:700

At Rady I teach a class titled _Research for Marketing Decisions_ to 2nd year MBA students.

--- #spss x:0 y:700

For years I used SPSS. SPSS has many tools my students won't need and defaults that may not apply to applications in marketing.

--- #deducer x:1050 y:700

I wanted to use R but without the steep learning curve. I spent __many__ hours programming a custom analysis menu for R using [Deducer](http://www.deducer.org/pmwiki/index.php?n=Main.DeducerManual?from=Main.HomePage). Deducer relies on [JGR](http://rforge.net/JGR/).

--- #shiny x:0 y:0 scale:3

Shiny






--- #what-is x:3000 y:0 scale:3

What is [Shiny](http://www.rstudio.com/shiny/)?

--- #credit x:2300 y:-700

An open-source R-package created and maintained by RStudio, Inc. First released in November 2012. [Shiny intro by Joe Cheng](http://joecheng.com/R/SeattleMay2013/assets/fallback/index.html).

--- #app x:3700 y:-700

## A Shiny app 

* __ui.r__ defines the user interface
* __server.r__ specifies the logic behind the user interface

--- #killer x:2300 y:700

## Killer features (for me)

* If you know R you can create a UI in Shiny
* Runs in a (modern) browser (i.e., _not_ IE)
* _Reactivity_ makes traditional GUIs look clumsy

--- #edu x:3700 y:700

## Applications in education

* Create and disseminate knowledge
* Make research results accessible
* Customize the user-experience (e.g., intermediate steps)





--- #radyant x:0 y:2500 scale:3

Rady + Shiny = [Radyant](http://localhost:8100)


--- #applets x:1750 y:1800

## Applets 2.0

[Applets without Java](http://localhost:8200)
<!-- <iframe width="1024" height="900" src="http://localhost:8200"></iframe> -->


--- #applets.ui.r x:2100 y:1800 scale:.3

## ui.R


```r
shinyUI(
  pageWithSidebar(
  
    headerPanel('Regression applet'),
    
    sidebarPanel(
      numericInput('b0', 'Intercept', min = -5, max = 5, 
      	value = 0, step = .1),
      numericInput('b1', 'Slope', min = -5, max = 5, 
      	value = 0.1, step = .1),
      checkboxInput('showFit', 'Show OLS fit'),
      helpText('Adapted from appstat by Yihui Xie')
    ),
    
    mainPanel( plotOutput('plot') )
  )
)
```


--- #applets.server.r x:2380 y:1800 scale:.3

## server.R


```r
N = 30
B0 = runif(1, -5, 5)
B1 = runif(1, -5, 5)
x = seq(-3, 3, length = N)
df = data.frame(x = x, y = B0 + B1 * x + rnorm(N))

shinyServer(function(input, output) {
    
    output$plot = renderPlot({
        
        plot(y ~ x, data = df)
        abline(input$b0, input$b1, col = "red")
        
        if (input$showFit) {
            fit = lm(y ~ x, data = df)
            abline(fit, col = "green")
        }
        
    }, width = 700, height = 500)
    
})
```






--- #modular x:1750 y:2360

## Modular

--- #modular.stata.menu x:2100 y:2360 scale:.3

## Stata menu

<img src="assets/img/stata-menu.png" style="width: 600px;"/>


--- #modular.navbar.html x:2380 y:2360 scale:.3

## [Radyant](http://localhost:8100) navbar

* navbar.html
``` html
<li><a href="#" data-value="hclustering">Hierarchical</a></li>
<li><a href="#" data-value="kmeansClustering">K-means</a></li>
```

* ui.R


```r
getTool <- function(inputId) {
  tagList(
    tags$head(tags$script(src = "js/navbar.js"))
    tags$html(includeHTML('www/navbar.html'))
  )
}
...
getTool('tool')     # assign data-value from navbar to input$tool
...
uiOutput("ui_analysis")
```


--- #modular.server.r x:2730 y:2360 scale:.3

## server.R


```r
# Multiple apps on different ports or a dynamic UI
sourceDirectory("tools", recursive = TRUE)

output$ui_analysis <- renderUI({
    if (input$tool == "dataview") 
        return()
    get(paste("ui_", input$tool, sep = ""))()
})
```



--- #modular.naming x:3080 y:2360 scale:.3

## Naming conventions

* Suppose _mytool_ is the name of the tool
* analysis reactive called _mytool_
* summary._mytool_ summary of text output
* plot._mytool_ one or more plots
* ui__mytool_ describes the UI for the summary and plot views


--- #modular.mytool x:3360 y:2360 scale:.3

## mytool example

* Add the line below to www/navbar.html


``` html
<li><a href="#" data-value="mytool">My awesome tool</a></li>
```


* Then create a file called mytool.R and drop it into the tools-directory

--- #modular.mytool.r.1 x:3640 y:2360 scale:.3

## mytool.R UI


```r
ui_mytool <- function() {
	numericInput('b0', 'Intercept', min = -5, max = 5, 
  		value = 0, step = .1),
	numericInput('b1', 'Slope', min = -5, max = 5, 
  		value = 0.1, step = .1),
	checkboxInput('showFit', 'Show OLS fit'),
	helpText('Adapted from appstat by Yihui Xie')
}
```


--- #modular.mytool.r.2 x:3920 y:2360 scale:.3

## Analysis, summary, and plot functions


```r
mytool <- reactive({
    lm(y ~ x, data = df)
})

summary.mytool <- function(result) summary(result)

plot.mytool <- function(result) {
    plot(y ~ x, data = df)
    abline(input$b0, input$b1, col = "red")
    if (input$showFit) 
        abline(result, col = "green")
}
```

--- #modular.mytool.in.radyant x:4200 y:2360 scale:.3

## Lets take a [look](http://localhost:8100)


--- #Todo x:1750 y:2850

## Todo


--- #Todo.summarize x:2100 y:2850 scale:.3

## Summarize data

* [plyr](http://plyr.had.co.nz/)
* [reshape](http://had.co.nz/reshape/)

## Visualize data

* [d3.js](http://d3js.org/)
* [rCharts](http://ramnathv.github.io/rCharts/)
* [googleVis](https://code.google.com/p/google-motion-charts-with-r/)

--- #Todo.reproduce x:2450 y:2850 scale:.3

## Reproducible research 

* [Knitr notebook](http://ramnathv.github.io/rNotebook/)
* Save-and-reload __input__


--- #me.end x:0 y:4500 scale:2

Vincent Nijs<br>
Assistant Professor of Marketing<br>
Rady school of Management, UCSD<br>
vnijs@rady.ucsd.edu

--- #credits x:-400 y:5000

## Credits

* [Rady](http://rady.ucsd.edu)
* [Slidify](http://www.slidify.org)
* [impress.js](http://github.com/bartaz/impress.js)

<!-- 

# Check

* Add sound?
* Slide notes? There is an extension to impress.js that does something like this.
* Add a background image through css
* Link to another slidified presentation?
* Include RMD slides (Rmd) into the Django app as static files?
* Setup personal git repo


# commands for slidify
require(slidify)
setwd('/Users/vnijs/Dropbox/mostly-harmless.github.io')
slidify('r-user-radyant.Rmd')


# launch from R-gui
require(shiny)
runApp('../radyant/inst/marketing/', launch.browser=FALSE)


# launch from terminal
require(shiny)
setwd('/Users/vnijs/Dropbox/radyant/inst/')
runApp('applet-ols', port=8200L, launch.browser=FALSE)

require(shiny)
setwd('/Users/vnijs/Dropbox/radyant/inst/')
runApp('finance', port=8300L, launch.browser=FALSE)

-->
