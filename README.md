# iBokeh-examples

## Table of Content   
[simple line chart](#simple-line)    
[log axis 4 line with markers chart](#log-axis)
[Vectorized colors and sizes](vectorize-color-size)    
### Vectorize color size

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


