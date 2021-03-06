Data Products ShinyApp Presentation
========================================================
author: KK
date: Nov 7, 2018
autosize: true

First Slide
========================================================

This Shiny web application is written for analysing how 'miles per gallon' of cars is associated with # of cyliinders, transmission and # of gears. 

## Summary 
You will observe the following outcome from this Shiny App
- Lesser the # of cylinder better is the mpg
- Manual transmission cars has a higher mpg than Automatic transmission cars
- 4 gear vehicles have higher mpg than that of 3 and 5 geared vehicles


Where is the app?
========================================================

The app URL is https://krish-learn.shinyapps.io/ddp_project/



Code for the UI part
========================================================


```r
library(shiny)

# Define UI for miles per gallon application
shinyUI(pageWithSidebar(
  
  # Application title
  headerPanel("Miles Per Gallon"),
  
  # Sidebar with controls to select the variable to plot against mpg
  # and to specify whether outliers should be included
  sidebarPanel(
    selectInput("variable", "Variable:",
                list("Cylinders" = "cyl", 
                     "Transmission" = "am", 
                     "Gears" = "gear")),
    
    checkboxInput("outliers", "Show outliers", FALSE)
  ),
  
  # Show the caption and plot of the requested variable against mpg
  mainPanel(
    h3(textOutput("caption")),
    
    plotOutput("mpgPlot")
  )
))
```

<!--html_preserve--><div class="container-fluid">
<div class="row">
<div class="col-sm-12">
<h1>Miles Per Gallon</h1>
</div>
</div>
<div class="row">
<div class="col-sm-4">
<form class="well">
<div class="form-group shiny-input-container">
<label class="control-label" for="variable">Variable:</label>
<div>
<select id="variable"><option value="cyl" selected>Cylinders</option>
<option value="am">Transmission</option>
<option value="gear">Gears</option></select>
<script type="application/json" data-for="variable" data-nonempty="">{}</script>
</div>
</div>
<div class="form-group shiny-input-container">
<div class="checkbox">
<label>
<input id="outliers" type="checkbox"/>
<span>Show outliers</span>
</label>
</div>
</div>
</form>
</div>
<div class="col-sm-8">
<h3>
<div id="caption" class="shiny-text-output"></div>
</h3>
<div id="mpgPlot" class="shiny-plot-output" style="width: 100% ; height: 400px"></div>
</div>
</div>
</div><!--/html_preserve-->

Code for the Server part
========================================================


```r
library(datasets)
library(shiny)

mpgData <- mtcars
mpgData$am <- factor(mpgData$am, labels = c("Automatic", "Manual"))

# Define server logic required to plot various variables against mpg
shinyServer(function(input, output) {
  
  # Compute the forumla text in a reactive expression since it is 
  # shared by the output$caption and output$mpgPlot expressions
  formulaText <- reactive({
    paste("mpg ~", input$variable)
  })
  
  # Return the formula text for printing as a caption
  output$caption <- renderText({
    formulaText()
  })
  
  # Generate a plot of the requested variable against mpg and only 
  # include outliers if requested
  output$mpgPlot <- renderPlot({
    boxplot(as.formula(formulaText()), 
            data = mpgData,
            outline = input$outliers)
  })
})
```

