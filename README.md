
# my-mc1

**N.B. The project is visionable** <a href="https://sthimarkearthquakes.000webhostapp.com/">here</a>

<img widh=800 src="main_dashboard.JPG"/>

## Project setup
```
npm install
```

### Compiles and hot-reloads for development
```
npm run serve
```

### Compiles and minifies for production
```
npm run build
```

### Lints and fixes files
```
npm run lint
```

### Customize configuration
See [Configuration Reference](https://cli.vuejs.org/config/).


# St.Himark
## Introduction 
### Vast Challenge 2019
The project presents an Earthquake Damage Report Interactive Dashboard for the [Vast Challenge 2019](https://vast-challenge.github.io/2019/), [micro challenge 1](https://vast-challenge.github.io/2019/MC1.html). In this challenge we have an imaginary situation of earthquakes hitting a city, called St.Himark. In this city, citizens have a mobile application that can report the shake intensity and the damage around them. The city's emergency responders want to use this information to find where to help first and they should make the decisions with degree of certainty. 

### Data
We had over 80,000 reports from citizens over five days. Records are provided in a csv file, containing informations about the district in which the damage has been detected, the timestamp, and six different categorical values of how violent the shaking was/how bad the damage was (0 - lowest, 10 - highest; missing data allowed):
- Power
- Buildings
- Sewer and Water
- Shake Intensity
- Roads and Bridges
- Medical
These data had been pre-aggregated by district in a json file, using three different aggregation functions: SUM(), COUNT(), AVG();  respectively called on the dashboard as “total”, “number of”, “average”.
 Also, global function had been used to observe these metrics at a higher level of aggregation, for the entire city. At this file has been added an svg path for each district, (and one for St.Himark), in order to offer a map view of the earthquakes. 
Another data pre-process has been used in the component PlotTS, in order to show time series for each categorical value. This approach will be discussed later.






## The App

The web application has been developed with the help of the Vue javascript, a progressive framework for building user interfaces. capable of powering sophisticated Single-Page Applications when used in combination with modern tooling and supporting libraries.

### Libraries
 Additional libraries had been used such as:
- D3: helpful in creating the choropleth map, and other charts, providing also graceful animations, colours palette, and a sophisticated way to integrate data and manage dom elements.
- BootstrapVue: a Vue integrated version of the popular front-end toolkit, essential to give the app a structure and keep components inline, and to supply readymade directives to offer a better navigation.
- Plotly: one of the most common libraries of this type, offers an easy framework to create beautiful and interactive charts of all types.

### Structure
The window is composed by a fixed navbar, with title, logo, two selection forms and one info button; by a body containing all the charts; and a footer with classical contacts and the university of Pisa logo. Also a toggle sidebar is present, with some damage info; users can open it from the body components. 
The body is shared in two bootstrap rows. In turn, the top row is divided into two columns, which can interact. In this way we have got three separated sections. Each section has been built as bootstrap tabs, a useful directive by bootstrap vue, that allows users to navigate between more “windows”. In this way each section hosts two components:
- The top tab on the left, contains two different plotly charts, a line chart and a radar chart.
- The top tab on the right, contains the choropleth map of the city, but also a list of all districts.
- The bottom row contains the pareto chart and the scatter plots, made by d3.

Clicking shapes of the graphs, where a toolkit appears is possible to open the toggle sidebar. It can be moved side by side just clicking on. And shows the navigation between the elements in blur, that can be bars, points or borders, representing districts. 

### Colours 
Choice of the colours is classical and essential. White background is the easiest choice to display plots and graphs in a clear format. It does not conflict with the default colours for plotly graphs, it does not tire your eyes, and it does not look bad next to primary colours. 
Primary colours have been used for the navbar and the footer, for the d3 graphs and for the map.
Bootstrap primary blue has been used for the navbar, while another blue, picked from the unipi logo, has been used for the footer (see the first page of this document). In both text colour is white, and so the page logo. The logo consists of a white shape representing the city of St.Himark created using gimp2.0.  As an aesthetic device, there is a shadow under the navbar. 
Other blue shades had been used in the body. Some buttons adopt bootstrap info variant colouration, while buttons in the Plotly charts, in agreement with Plotly blue, use again bootstrap primary. The bars and the points of the d3 plots are instead using a steel blue. 
While main shapes of the d3 plots are blue, additive information had been plotted both in the bar chart and in the scatter plots, this time using red, in order to not get confused with the rest of the figure. 
 
However, the most fundamental use of red, was made in the choropleth map. Each district has a darker red colour the higher the value it represents. The palette has been retrieved by d3. 
I should also mention the use of the dark bootstrap variant both in the districts list and mostly in the sidebar. There you can find a squared frame with rounded corners using this grey colouration. The district shape inside is of the same colour, and the info table immediately above uses the dark theme too, in such a way you cannot distinguish any border between them. 

Finally I want to mention the icon of the web page; gimp2.0 has been used to mount the Vue logo with the St.Himark map to create the image present on the top right corner of this page. 









## Components
Since a Vue project is made of different Vue components, there is always a root component. In this app, the root component contains the navbar, the footer and the dashboard. All other components are nested on the latter.
In the next paragraph, we will describe all components one by one, their function and their interactions. 
### NavBar 
The navbar has left aligned the logo and the title “St.Himark Earthquake Map”. Right aligned there are two selection forms, one to choose the categorical measurement to visualize, and one for the aggregation function desired. Changing this parameter each component of the dashboard changes accordingly (except for the sidebar). The bar is positioned as fixed. So, during the navigation user can always assess it and change the measure of its interest. The selection forms are in toggle version from mobile devices. On the right corner there is an info button, it opens a bootstrap modal with information about how the data had been collected.
### Footer
It is basically for aesthetic; moreover, contains links, to reach:
- Unipi website
- Visual Analytics course I attended
- Vast challenge 2019 instruction 
- My mail 
- My github, in which you can find a repo of this project 

### Dashboard
Is the body of the application. Contains the SideBar and the structure with the three sections and tabs already mentioned. Here the json file states.json is fetched, and data are distributed to the nested components as props.  While the page is loading, a spinner appears in the centre of the page. 
### SideBar
Since other components allow us to navigate and move the mouse over some objects representing the various districts, sidebar responses of mouse click, mouse enter and mouse leave. It can be opened by clicking on those causes is a toggle sidebar. The position is toggle too, in fact you can move it on the side you prefer. It is characterized by a mini-map with the shape of the district selected or in evidence. Below there is an info table with all the aggregated info we have about his district.

### Map 
Is a typical choropleth map used in this occasion, made with d3 help. Colours represent the selected measure from the navbar. Is doted of a legend, and of two switch boxes, one to display the position of the hospital on the map, and one to see the power plant. These positions are obtained by reusing more times the same svg inside the map, in agreement with the legend. 
In order to encourage interaction with the page, drag and drop has been implemented. Districts are draggable; drop it inside the plotly charts and it will be plotted.
For an easier navigation, tooltip appears with the mouse on a district, to show its name and its damage value.

### DistrictsList
In alternative of the map, here we can see the name of each district, and the numeric value for the measure selected. Buttons are present that allow to plot the districts on the Plotly. In this way, avoid using drag and drop, for those who do not like it, and is also the easiest solution to interact with the Plotly chart by mobile devices.

### RadarChart
Radar chart, made with Plotly, is perfect to compare different objects at the same time for different values. So, because all the measures are described by this chart, the component changes only in the base of the aggregation function selected. Changes are accompanied by Plotly animation.
 Both the Plotly components allow in comparing different districts, and in both is possible to hide the average value for St.Himark (for each aggregation function) by a switch box. In both there is also a button to clean them and an info button. We can say that they work in parallel. Districts are added in the same moment to both graphs, and cleaning one also the other one is cleaned.

### PlotTS
The other plotly component is a more traditional line chart, used for plot time series. The data used for this component are not the same used for others. In fact, from the original data, we need a different aggregation in order to plot time series. Practically, the original csv has been partitioned in one csv for each district. Then, records, inside each csv, have been aggregated hour by hour, and so has been possible to have hour on columns and values(aggregated) on rows. When a district is dropped here, the corresponding csv is loaded, and the time series appears on the chart.


### Pareto
This component, made by d3, is an order bar chart, from the highest value to the lowest, one bar for district. While the left y axe shows the value of the bar, another y axe, on the right, shows percentage: a red Lorenz curve, from the left to the right, take in evidence the cumulative percentage. D3 transitions take the bars in order when the same parameter is changed on the navbar.
### ScatterPlot
The second component made with d3 has been, reused more times in order to show together the scatter plots of the selected category value with all the other values. Since each point represents a district, tooltips help to understand names and values retrieved. A red linear regression line is plotter for each plot, to show the slope.
## Conclusions
### Summary
The app is projected in order to always have the Plotly charts next to the map or to the districts lists, for a better interaction between them. Navigation on the choropleth map is easier thanks to the tooltip. The colour interpolation immediately gives an idea of the size of the damages in each district, and we can choose based on which districts we want to compare. So just drag it inside the plot. Usually drag and drop involves the user at a greater level. The districts list is useful to interact also from mobile.  
As already said, the NavBar is fixed: from each y position of the page is always possible to change the measure of interest and/or its aggregation function. So, it is possible to change attributes also while watching the Pareto chart (that is in the bottom).
On the PlotTS, we can compare for different districts, how the damage reports have changed during the five days analysed. On the Polar chart we can compare the damage size of the districts, for all the measurements at the same time. 
Another fast way to see the main problems is the pareto chart. You know which are the most affected districts, and you can stratify your response faster, to allocate resources where it is more necessary.  
Scatter plots give a group vision of all the districts for each couple of e measures. We can compare the district between them, but also see if any type of damage is more problematic than others.



### Final Considerations

After this summary I leave some consideration: 
- Has been particularly important in the implementation of drag and drop: each modern application has it; is not a way to show the data but is only a way to take the user in concentration. Not everybody likes it but most people. 
- The scatter plots can also be transformed on a solitary scatter plot with one more select form for the second attribute measure (while the first is from the NavBar). 
- The approach used to have more times the same component (the scatter plot) is particular; is important always using refs in a reusable Vue component.
- On some occasions d3 data and v-for can be used to do the same thing. Sometimes using v-for allows you to use v-directives in the right way. While managing to use both d3 and Vue can be not trivial.
- The data had been pre-processed in python, the granularity chosen is the lowest possible in order to show something; the loading is very easy, but as shown for the time series necessity, using a higher granularity (e.g.  districts and day by day), allow to more sophisticated analysis without performance worsening.
- The choropleth map has been implemented for simplicity using simple svg data in the form of string; however, it is possible to use geological data (geojson or topojson) and d3 projection. It can be better for the modules and functions implemented by d3 library.

