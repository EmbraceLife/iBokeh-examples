# iBokeh-examples

## Table of Content   
[simple line chart](#simple-line)    
[log axis 4 line with markers chart](#log-axis)
[Vectorized colors and sizes](vectorize-color-size)    
[linked panning between different plots by range](link-x-y-by-range)    
[linked brush selection between different plots by columndatasource](#link-x-y-by-columndatasource)     
[stock chart: datetime, average line, close price](#stocks-chart)    

### stocks chart
[video: stock chart](https://youtu.be/ZgyapbXsxl4)     
- import dataset from bokeh library
- access dict values by keys
- convert list to numpy array 
- convert string list to numpy datetime array 
- create window-size and window 
- calc montly data from daily data using np.convolve(aapl, window, 'same')   
- create figure with width, height and x_axis_type
- create circle with x, y, size, color, alpha, legend
- create line with x, y, color, legend
- set plot title
- set legend location
- set x-axis-label
- set ygrid.band-fill-color
- set ygrid.band-fill-alpha
```python
import numpy as np

from bokeh.plotting import figure, output_file, show
from bokeh.sampledata.stocks import AAPL

# prepare some data
aapl = np.array(AAPL['adj_close'])
aapl_dates = np.array(AAPL['date'], dtype=np.datetime64)

window_size = 30
window = np.ones(window_size)/float(window_size)
aapl_avg = np.convolve(aapl, window, 'same')

# output to static HTML file
output_file("stocks.html", title="stocks.py example")

# create a new plot with a a datetime axis type
p = figure(width=800, height=350, x_axis_type="datetime")

# add renderers
p.circle(aapl_dates, aapl, size=4, color='darkgrey', alpha=0.2, legend='close')
p.line(aapl_dates, aapl_avg, color='navy', legend='avg')

# NEW: customize by setting attributes
p.title.text = "AAPL One-Month Average"
p.legend.location = "top_left"
p.grid.grid_line_alpha=0
p.xaxis.axis_label = 'Date'
p.yaxis.axis_label = 'Price'
p.ygrid.band_fill_color="olive"
p.ygrid.band_fill_alpha = 0.1

# show the results
show(p)
```



### link x y by columndatasource
[video: linked pan vs linked brush](https://youtu.be/x3Tab5j8Ers)      
[video: how to build linked brush](ttps://youtu.be/5o12UrTMYiM)      
- import ColumnDataSource from bokeh.models
- create arrays using np.linspace, np.sin, np.cos, for x, y0, y1
- create html
- create ColumnDataSource(data=dict(columnName = arrayName, columnName = arrayName,columnName = arrayName))
- create figure
- create circles with columnNames and source
- bring subplots into a gridplot
```python
import numpy as np
from bokeh.plotting import *
from bokeh.models import ColumnDataSource

# prepare some date
N = 300
x = np.linspace(0, 4*np.pi, N)
y0 = np.sin(x)
y1 = np.cos(x)

# output to static HTML file
output_file("linked_brushing.html")

# NEW: create a column data source for the plots to share
source = ColumnDataSource(data=dict(x=x, y0=y0, y1=y1))

TOOLS = "pan,wheel_zoom,box_zoom,reset,save,box_select,lasso_select"

# create a new plot and add a renderer
left = figure(tools=TOOLS, width=350, height=350, title=None)
left.circle('x', 'y0', source=source)

# create another new plot and add a renderer
right = figure(tools=TOOLS, width=350, height=350, title=None)
right.circle('x', 'y1', source=source)

# put the subplots in a gridplot
p = gridplot([[left, right]])

# show the results
show(p)
```




### link x y by range
[video](https://youtu.be/LFA9CQPTTWg)     
- import gridplot from bokeh.layouts 
- create arrays with np.linspace, np.sin, np.cos, np.pi
- create figure with width, height and title
- create circle with x, y, size, color, alpha
- create figure 2nd with width, height, x-y-range, title
- create triangel with x, y, size, color, alpha
- create figure 3rd with width, height, x-range, title
- create square with x, y, size, color, alpha
- put three figures into a single gridplot 
- hiding toolbar with toolbar_location = None

```python
import numpy as np

from bokeh.layouts import gridplot
from bokeh.plotting import figure, output_file, show

# prepare some data
N = 100
x = np.linspace(0, 4*np.pi, N)
y0 = np.sin(x)
y1 = np.cos(x)
y2 = np.sin(x) + np.cos(x)

# output to static HTML file
output_file("linked_panning.html")

TOOLS = "pan,wheel_zoom,box_zoom,reset,save,box_select,lasso_select"

# create a new plot
s1 = figure(tools=TOOLS, width=250, plot_height=250, title=None)
s1.circle(x, y0, size=10, color="navy", alpha=0.5)

# NEW: create a new plot and share both ranges
s2 = figure(tools=TOOLS, width=250, height=250, x_range=s1.x_range, y_range=s1.y_range, title=None)
s2.triangle(x, y1, size=10, color="firebrick", alpha=0.5)

# NEW: create a new plot and share only one range
s3 = figure(tools=TOOLS, width=250, height=250, x_range=s1.x_range, title=None)
s3.square(x, y2, size=10, color="olive", alpha=0.5)

# NEW: put the subplots in a gridplot
p = gridplot([[s1, s2, s3]]) # toolbar_location = None -> to hide toolbar

# show the results
show(p)
```


### Vectorize color size
[video](https://youtu.be/wLgRuNB7O4o)    
- set number of data 
- create arrays with random numbers for x, y
- create arrays for colors and radius   
- create html file with title and mode 
- set tools 
- create figure with tools, ranges for x and y
- create circles with x, y, radius, fill_color, fill_alpha, line_color
- set line_color to be none, as default is blue? 

```python
import numpy as np

from bokeh.plotting import figure, output_file, show

# prepare some data
N = 4000
x = np.random.random(size=N) * 100
y = np.random.random(size=N) * 100
radii = np.random.random(size=N) * 1.5
# colors = [
#  [int(r), int(g), 150] for r, g in zip(50+2*x, 30+2*y)
# ]
# print(colors[0:5])

colors = [
    "#%02x%02x%02x" % (int(r), int(g), 150) for r, g in zip(50+2*x, 30+2*y)
]
# print(colors1[0:5])
# output to static HTML file (with CDN resources)
output_file("color_scatter.html", title="color_scatter.py example", mode="cdn")

TOOLS="resize,crosshair,pan,wheel_zoom,box_zoom,reset,box_select,lasso_select"

# create a new plot with the tools above, and explicit ranges
p = figure(tools=TOOLS, x_range=(0,100), y_range=(0,100))

# add a circle renderer with vectorized colors and sizes
p.circle(x,y, radius=radii, fill_color=colors, fill_alpha=0.6, line_color=None)

# show the results
show(p)
```


### log axis
[video](https://youtu.be/uHXgx1TO7Xk)
- import libraries and functions 
- create y array with `for in` and x array 
- create html file 
- create figure: set tools, y_axis_type, y_range, title, x-axis-label
- create line plot, circle plot
- set legend, fill_color, line_color, size (for circle), line_width, line_dash

```python
from bokeh.plotting import figure, output_file, show

# prepare some data
x = [0.1, 0.5, 1.0, 1.5, 2.0, 2.5, 3.0]
y0 = [i**2 for i in x]
y1 = [10**i for i in x]
y2 = [10**(i**2) for i in x]

# output to static HTML file
output_file("log_lines.html")

# create a new plot
p = figure(
   tools="pan,box_zoom,reset,save",
   y_axis_type="log", y_range=[0.001, 10**11], title="log axis example",
   x_axis_label='sections', y_axis_label='particles'
)

# add some renderers
p.line(x, x, legend="y=x")
p.circle(x, x, legend="y=x", fill_color="white", line_color = 'red', size=8)
p.line(x, y0, legend="y=x^2", line_width=3)
p.line(x, y1, legend="y=10^x", line_color="red")
p.circle(x, y1, legend="y=10^x", fill_color="red", size=12)
p.line(x, y2, legend="y=10^x^2", line_color="orange", line_dash="7 7")

# show the results
show(p)
```


### simple line 
[video](https://youtu.be/8NLo4_R7CkI)    
- import bokeh libraries and functions 
- create data 
- create output html file 
- create a plot with title and axis label
- create a line with legend and width 
- show plot 

```python
from bokeh.plotting import figure, output_file, show

# prepare some data
x = [1, 2, 3, 4, 5]
y = [6, 7, 2, 4, 5]

# output to static HTML file
output_file("lines.html")

# create a new plot with a title and axis labels
p = figure(title="simple line example", x_axis_label='x', y_axis_label='y')

# add a line renderer with legend and line thickness
p.line(x, y, legend="Temp.", line_width=2)

# show the results
show(p)
```


