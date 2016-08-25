# iBokeh-examples

## Table of Content   
[Check versions](#versions)    
[simple line chart](#simple-line)    
[log axis 4 line with markers chart](#log-axis)
[Vectorized colors and sizes](vectorize-color-size)    
[linked panning between different plots by range](link-x-y-by-range)    
[linked brush selection between different plots by columndatasource](#link-x-y-by-columndatasource)     
[stock chart: datetime, average line, close price](#stocks-chart)    
[area chart](#area)    
[bar multiple](#bars)     



### timeseries
```python
import pandas as pd

from bokeh.charts import TimeSeries, show, output_file
from bokeh.layouts import column

# read in some stock data from the Yahoo Finance API
AAPL = pd.read_csv(
    "http://ichart.yahoo.com/table.csv?s=AAPL&a=0&b=1&c=2000&d=0&e=1&f=2010",
    parse_dates=['Date'])
MSFT = pd.read_csv(
    "http://ichart.yahoo.com/table.csv?s=MSFT&a=0&b=1&c=2000&d=0&e=1&f=2010",
    parse_dates=['Date'])
IBM = pd.read_csv(
    "http://ichart.yahoo.com/table.csv?s=IBM&a=0&b=1&c=2000&d=0&e=1&f=2010",
    parse_dates=['Date'])

data = pd.DataFrame(data=dict(AAPL=AAPL['Adj Close'][:1000],
                              MSFT=MSFT['Adj Close'][:1000],
                              IBM=IBM['Adj Close'][:1000],
                              Date=AAPL['Date'][:1000])).set_index('Date')

TOOLS="pan,wheel_zoom,box_zoom,reset,save"

# line simple
tsline = TimeSeries(
    data, y=['IBM', 'MSFT', 'AAPL'], legend=True,
    title="Timeseries (Line)", tools=TOOLS, ylabel='Stock Prices',
    xlabel='Date')

# line explicit
tsline2 = TimeSeries(
    data, y=['IBM', 'MSFT', 'AAPL'], legend=True,
    color=['IBM', 'MSFT', 'AAPL'], dash=['IBM', 'MSFT', 'AAPL'],
    title="Timeseries (Line Explicit)", tools=TOOLS, ylabel='Stock Prices',
    xlabel='Date')

# step
tsstep = TimeSeries(
    data, y=['IBM', 'MSFT', 'AAPL'], legend=True, builder_type='step',
    title="Timeseries (Step)", tools=TOOLS, ylabel='Stock Prices',
    xlabel='Date')

# point
tspoint = TimeSeries(
    data, y=['IBM', 'MSFT', 'AAPL'], legend=True, builder_type='point',
    marker=['IBM', 'MSFT', 'AAPL'], color=['IBM', 'MSFT', 'AAPL'],
    title="Timeseries (Point)", tools=TOOLS, ylabel='Stock Prices',
    xlabel='Date')

output_file("timeseries.html", title="timeseries.py example")

show(column(tsline, tsline2, tsstep, tspoint))

```



### steps
```python
""" This example uses the U.S. postage rate per ounce for stamps and
postcards.

Source: https://en.wikipedia.org/wiki/History_of_United_States_postage_rates
"""

from bokeh.charts import Step, show, output_file

# build a dataset where multiple columns measure the same thing
data = dict(stamp=[
                .33, .33, .34, .37, .37, .37, .37, .39, .41, .42,
                .44, .44, .44, .45, .46, .49, .49],
            postcard=[
                .20, .20, .21, .23, .23, .23, .23, .24, .26, .27,
                .28, .28, .29, .32, .33, .34, .35],
            )

# create a line chart where each column of measures receives a unique color and dash style
line = Step(data, y=['stamp', 'postcard'],
            dash=['stamp', 'postcard'],
            color=['stamp', 'postcard'],
            title="U.S. Postage Rates (1999-2015)", ylabel='Rate per ounce', legend=True)

output_file("steps.html", title="steps.py example")

show(line)

```



### stacked bar 
```python
from bokeh.charts import Bar, output_file, show
from bokeh.charts.attributes import cat, color
from bokeh.charts.operations import blend
from bokeh.charts.utils import df_from_json
from bokeh.sampledata.olympics2014 import data

# utilize utility to make it easy to get json/dict data converted to a dataframe
df = df_from_json(data)

# filter by countries with at least one medal and sort by total medals
df = df[df['total'] > 0]
df = df.sort("total", ascending=False)

bar = Bar(df,
          values=blend('bronze', 'silver', 'gold', name='medals', labels_name='medal'),
          label=cat(columns='abbr', sort=False),
          stack=cat(columns='medal', sort=False),
          color=color(columns='medal', palette=['SaddleBrown', 'Silver', 'Goldenrod'],
                      sort=False),
          legend='top_right',
          title="Medals per Country, Sorted by Total Medals",
          tooltips=[('medal', '@medal'), ('country', '@abbr')])


output_file("stacked_bar.html", title="stacked_bar.py example")

show(bar)

```


### scatter multiple
```python
import pandas as pd

from bokeh.charts import Scatter, output_file, defaults, show
from bokeh.charts.operations import blend
from bokeh.charts.utils import df_from_json
from bokeh.layouts import gridplot
from bokeh.sampledata.autompg import autompg as df
from bokeh.sampledata.iris import flowers
from bokeh.sampledata.olympics2014 import data

defaults.plot_width = 450
defaults.plot_height = 400

scatter0 = Scatter(
    df, x='mpg', title="x='mpg'", xlabel="Miles Per Gallon")

scatter1 = Scatter(
    df, x='mpg', y='hp', title="x='mpg', y='hp'",
    xlabel="Miles Per Gallon", ylabel="Horsepower", legend='top_right')

scatter2 = Scatter(
    df, x='mpg', y='hp', color='cyl', title="x='mpg', y='hp', color='cyl'",
    xlabel="Miles Per Gallon", ylabel="Horsepower", legend='top_right')

scatter3 = Scatter(
    df, x='mpg', y='hp', color='origin', title="x='mpg', y='hp', color='origin', with tooltips",
    xlabel="Miles Per Gallon", ylabel="Horsepower",
    legend='top_right', tooltips=[('origin', "@origin")])

scatter4 = Scatter(
    df, x='mpg', y='hp', color='cyl', marker='origin', title="x='mpg', y='hp', color='cyl', marker='origin'",
    xlabel="Miles Per Gallon", ylabel="Horsepower", legend='top_right')

# Example with nested json/dict like data, which has been pre-aggregated and pivoted
df2 = df_from_json(data)
df2 = df2.sort('total', ascending=False)

df2 = df2.head(10)
df2 = pd.melt(df2, id_vars=['abbr', 'name'])

scatter5 = Scatter(
    df2, x='value', y='name', color='variable', title="x='value', y='name', color='variable'",
    xlabel="Medals", ylabel="Top 10 Countries", legend='bottom_right')

scatter6 = Scatter(flowers, x=blend('petal_length', 'sepal_length', name='length'),
                   y=blend('petal_width', 'sepal_width', name='width'), color='species',
                   title='x=petal_length+sepal_length, y=petal_width+sepal_width, color=species',
                   legend='top_right')

output_file("scatter_multi.html", title="scatter_multi.py example")

show(gridplot(scatter0,  scatter2, scatter3, scatter4,
              scatter5, scatter6, ncols=2))

```

### pallette
```python
import numpy as np

from collections import OrderedDict

from bokeh.charts import Area, show
from bokeh.layouts import gridplot
from bokeh.palettes import (Blues9, BrBG9, BuGn9, BuPu9, GnBu9, Greens9,
                            Greys9, OrRd9, Oranges9, PRGn9, PiYG9, PuBu9,
                            PuBuGn9, PuOr9, PuRd9, Purples9, RdBu9, RdGy9,
                            RdPu9, RdYlBu9, RdYlGn9, Reds9, Spectral9, YlGn9,
                            YlGnBu9, YlOrBr9, YlOrRd9, Inferno9, Magma9,
                            Plasma9, Viridis9, Accent8, Dark2_8, Paired9,
                            Pastel1_9, Pastel2_8, Set1_9, Set2_8, Set3_9)

standard_palettes = OrderedDict([("Blues9", Blues9), ("BrBG9", BrBG9),
                                 ("BuGn9", BuGn9), ("BuPu9", BuPu9),
                                 ("GnBu9", GnBu9), ("Greens9", Greens9),
                                 ("Greys9", Greys9), ("OrRd9", OrRd9),
                                 ("Oranges9", Oranges9), ("PRGn9", PRGn9),
                                 ("PiYG9", PiYG9), ("PuBu9", PuBu9),
                                 ("PuBuGn9", PuBuGn9), ("PuOr9", PuOr9),
                                 ("PuRd9", PuRd9), ("Purples9", Purples9),
                                 ("RdBu9", RdBu9), ("RdGy9", RdGy9),
                                 ("RdPu9", RdPu9), ("RdYlBu9", RdYlBu9),
                                 ("RdYlGn9", RdYlGn9), ("Reds9", Reds9),
                                 ("Spectral9", Spectral9), ("YlGn9", YlGn9),
                                 ("YlGnBu9", YlGnBu9), ("YlOrBr9", YlOrBr9),
                                 ("YlOrRd9", YlOrRd9), ("Inferno9", Inferno9),
                                 ("Magma9", Magma9), ("Plasma9", Plasma9),
                                 ("Viridis9", Viridis9), ("Accent8", Accent8),
                                 ("Dark2_8", Dark2_8), ("Paired9", Paired9),
                                 ("Pastel1_9", Pastel1_9),
                                 ("Pastel2_8", Pastel2_8), ("Set1_9", Set1_9),
                                 ("Set2_8", Set2_8), ("Set3_9", Set3_9)])


def create_area_chart(data, palette):
    return Area(data,
                title=palette,
                stack=True,
                palette=standard_palettes.get(palette),
                legend=None,
                xlabel='',
                ylabel='',
                xgrid=False,
                ygrid=False,
                tools='')

data = np.random.random_integers(low=5, high=13, size=[9, 20])
area_charts = [create_area_chart(data, palette) for palette in standard_palettes.keys()]
grid = gridplot(area_charts, ncols=3, plot_width=300, plot_height=300)
show(grid)

```



### line simple
```python
from bokeh.charts import Line, show, output_file

# build a dataset where multiple columns measure the same thing
data = dict(python=[2, 3, 7, 5, 26, 221, 44, 233, 254, 265, 266, 267, 120, 111],
            pypy=[12, 33, 47, 15, 126, 121, 144, 233, 254, 225, 226, 267, 110, 130],
            jython=[22, 43, 10, 25, 26, 101, 114, 203, 194, 215, 201, 227, 139, 160],
            test=['foo', 'bar', 'foo', 'bar', 'foo', 'bar', 'foo', 'bar', 'foo', 'bar',
                  'foo', 'bar', 'foo', 'bar']
            )

# create a line chart where each column of measures receives a unique color and dash style
line = Line(data, y=['python', 'pypy', 'jython'],
            dash=['python', 'pypy', 'jython'],
            color=['python', 'pypy', 'jython'],
            legend_sort_field = 'color',
            legend_sort_direction = 'ascending',
            title="Interpreter Sample Data", ylabel='Duration', legend=True)

output_file("line_single.html", title="line_single.py example")

show(line)

```


### line multiple 
```python
import pandas as pd

from bokeh.layouts import gridplot
from bokeh.charts import Line, show, output_file, defaults

defaults.plot_width = 450
defaults.plot_height = 400

# build a dataset where multiple columns measure the same thing
data = dict(python=[2, 3, 7, 5, 26, 221, 44, 233, 254, 265, 266, 267, 120, 111],
            pypy=[12, 33, 47, 15, 126, 121, 144, 233, 254, 225, 226, 267, 110, 130],
            jython=[22, 43, 10, 25, 26, 101, 114, 203, 194, 215, 201, 227, 139, 160],
            test=['foo', 'bar', 'foo', 'bar', 'foo', 'bar', 'foo', 'bar', 'foo', 'bar', 'foo', 'bar', 'foo', 'bar']
            )
df = pd.DataFrame(data)

# add a column with a range of dates, as if the values were sampled then
df['date'] = pd.date_range('1/1/2015', periods=len(df.index), freq='D')

# default behavior for dataframe input is to plot each numerical column as a line
line = Line(df)

# build the line plots
line0 = Line(df, y=['python', 'pypy', 'jython'],
             title="Interpreters (y=['python', 'pypy', 'jython'])", ylabel='Duration', legend=True)

line1 = Line(df, x='date', y=['python', 'pypy', 'jython'],
             title="Interpreters (x='date', y=['python', 'pypy', 'jython'])", ylabel='Duration', legend=True)

line2 = Line(df, x='date', y=['python', 'pypy', 'jython'],
             dash=['python', 'pypy', 'jython'],
             title="Interpreters (x='date', y, dash=['python', 'pypy', 'jython'])", ylabel='Duration', legend=True)

line3 = Line(df, x='date', y=['python', 'pypy', 'jython'],
             dash=['python', 'pypy', 'jython'],
             color=['python', 'pypy', 'jython'],
             title="Interpreters (x='date', y, dash, color=['python', 'pypy', 'jython'])", ylabel='Duration', legend=True)

line4 = Line(df, x='date', y=['python', 'pypy', 'jython'],
             dash='test',
             color=['python', 'pypy', 'jython'],
             title="Interpreters (x='date', y, color=['python', 'pypy', 'jython'], dash='test') with tooltips", ylabel='Duration',
             legend=True, tooltips=[('series', '@series'), ('test', '@test')])

output_file("line_multi.html", title="line examples")

show(gridplot(line, line0, line1, line2, line3, line4, ncols=2))

```




### iris simple
```python
from bokeh.charts import Scatter, output_file, show
from bokeh.sampledata.iris import flowers as data

scatter = Scatter(data, x='petal_length', y='petal_width',
                  color='species', marker='species',
                  title='Iris Dataset Color and Marker by Species',
                  legend=True)

output_file("iris_simple.html", title="iris_simple.py example")

show(scatter)
```




### iris blend
```python
""" This example uses the Iris data to demonstrate the specification of
combined variables using chart operations.

This specific instance uses a blend, which stacks columns, and renames
the combined column. This can be used where the column itself is a type
of categorical variable. Here, length and width are derived from the
petal and sepal measurements.
"""

from bokeh.charts import Scatter, output_file, show
from bokeh.charts.operations import blend
from bokeh.sampledata.iris import flowers as data

scatter = Scatter(data,
                  x=blend('petal_length', 'sepal_length', name='length'),
                  y=blend('petal_width', 'sepal_width', name='width'),
                  color='species',
                  title='x=petal_length+sepal_length, y=petal_width+sepal_width, color=species',
                  legend='top_right')

output_file("iris_blend.html", title="iris_blend.py example")

show(scatter)
```




### hover span 
```python
import pandas as pd

from bokeh.charts import Line, Scatter, show, output_file, defaults
from bokeh.layouts import gridplot
from bokeh.models import HoverTool
from bokeh.sampledata.degrees import data

defaults.width = 500
defaults.height = 300

TOOLS='box_zoom,box_select,hover,crosshair,reset'

TOOLTIPS = [ ("y", "$~y"), ("x", "$~x") ]

data = data[['Biology', 'Business', 'Computer Science', "Year"]]
data = pd.melt(data, id_vars=['Year'],
               value_vars=['Biology', 'Business', 'Computer Science'],
               value_name='Count', var_name='Degree')

vline = Line(data, y='Count', color='Degree', title="Lines VLine", ylabel='measures',
             tools=TOOLS)

hline = Line(data, y='Count', color='Degree', title="Lines HLine",
             ylabel='measures', tools=TOOLS)

int_vline = Line(data, y='Count', color='Degree', title="Lines VLine Interp",
                 ylabel='measures', tools=TOOLS)

int_hline = Line(data, y='Count', color='Degree', title="Lines HLine Interp",
                 ylabel='measures', tools=TOOLS)

scatter_point = Scatter(data, x='Year', y='Count', color='Degree',
                        title="Scatter mouse", ylabel='measures', legend=True,
                        tools=TOOLS)

scatter = Scatter(data, x='Year', y='Count', color='Degree',
                  title="Scatter V Line", ylabel='measures', legend=True, tools=TOOLS)

int_point_line = Line(data, x='Year', y='Count', color='Degree',
                      title="Lines Mouse Interp.", ylabel='measures', tools=TOOLS)

point_line = Line(data, x='Year', y='Count', color='Degree',
                  title="Lines Mouse", ylabel='measures', tools=TOOLS)


hhover = hline.select(HoverTool)
hhover.mode = 'hline'
hhover.line_policy = 'next'

vhover = vline.select(HoverTool)
vhover.mode = 'vline'
vhover.line_policy = 'nearest'

int_hhover = int_hline.select(HoverTool)
int_hhover.mode = 'hline'
int_hhover.line_policy = 'interp'

int_vhover = int_vline.select(HoverTool)
int_vhover.mode = 'vline'
int_vhover.line_policy = 'interp'

iphover = int_point_line.select(HoverTool)
iphover.mode = 'mouse'
iphover.line_policy = 'interp'

tphover = point_line.select(HoverTool)
tphover.mode = 'mouse'

shover = scatter.select(HoverTool)
shover.mode = 'vline'

shoverp = scatter_point.select(HoverTool)
shoverp.mode = 'mouse'

# set up tooltips
int_vhover.tooltips = int_hhover.tooltips = TOOLTIPS
tphover.tooltips = iphover.tooltips = TOOLTIPS
shover.tooltips = shoverp.tooltips = TOOLTIPS
vhover.tooltips = hhover.tooltips = TOOLTIPS

output_file("hover_span.html", title="hover_span.py example")

show(gridplot(hline, vline, int_hline, int_vline,
              int_point_line, point_line, scatter_point, scatter,
              ncols=2))

```



### horizon folds
```python
import numpy as np

from bokeh.charts import Horizon, output_file, show

x = np.linspace(0, np.pi*4, 137)
y = (2*np.random.normal(size=137) + x**2)
xx = np.hstack([-1*x[::-1], x])
yy = np.hstack([-1*y[::-1], y])

data = dict([
    ('x', xx), ('y', yy), ('y2', yy),
    ('y3', yy), ('y4', yy), ('y5', yy)
])

hp = Horizon(data, x='x', title="test horizon", ylabel='Random')

output_file("horizon_folds.html", title="horizon_folds.py example")

show(hp)

```



### horizon
```python
import pandas as pd

from bokeh.charts import Horizon, output_file, show

# read in some stock data from the Yahoo Finance API
AAPL = pd.read_csv(
    "http://ichart.yahoo.com/table.csv?s=AAPL&a=0&b=1&c=2000&d=0&e=1&f=2010",
    parse_dates=['Date'])

MSFT = pd.read_csv(
    "http://ichart.yahoo.com/table.csv?s=MSFT&a=0&b=1&c=2000&d=0&e=1&f=2010",
    parse_dates=['Date'])

IBM = pd.read_csv(
    "http://ichart.yahoo.com/table.csv?s=IBM&a=0&b=1&c=2000&d=0&e=1&f=2010",
    parse_dates=['Date'])

data = pd.DataFrame(data=dict(AAPL=AAPL['Adj Close'],
                              MSFT=MSFT['Adj Close'],
                              IBM=IBM['Adj Close'],
                              Date=AAPL['Date'])).set_index('Date')

hp = Horizon(data, plot_width=800, plot_height=300,
             title="horizon plot using stock inputs", xlabel='Date')

output_file("horizon.html", title="horizon.py example")

show(hp)

```



### histogram single
```python
from bokeh.charts import Histogram, output_file, show
from bokeh.sampledata.autompg import autompg as df

df.sort('cyl', inplace=True)

hist = Histogram(df, values='hp', color='cyl',
                 title="HP Distribution by Cylinder Count", legend='top_right')

output_file("histogram_single.html", title="histogram_single.py example")

show(hist)

```


### histogram multiple 
```python
from bokeh.charts import Histogram, defaults, show, output_file
from bokeh.layouts import gridplot
from bokeh.sampledata.autompg import autompg as df

defaults.plot_width = 400
defaults.plot_height = 350

# input options
hist  = Histogram(df['mpg'], title="df['mpg']")
hist2 = Histogram(df, 'displ', title="df, 'displ'")
hist3 = Histogram(df, values='hp', title="df, values='hp'", density=True)

hist4 = Histogram(df, values='hp', color='cyl',
                  title="df, values='hp', color='cyl'", legend='top_right')

hist5 = Histogram(df, values='mpg', bins=50,
                  title="df, values='mpg', bins=50")
hist6 = Histogram(df, values='mpg', bins=[10, 15, 25, 100], tooltips=[('Bin', "@label")],
                  title="df, values='mpg', bins=[10, 15, 25, 100]")

output_file("histogram_multi.html", title="histogram_multi.py example")

show(gridplot(hist,  hist2, hist3, hist4,
              hist5, hist6, ncols=2))

```


### heatmap
```python
import pandas as pd

from bokeh.charts import HeatMap, bins, output_file, show
from bokeh.layouts import column, gridplot
from bokeh.palettes import RdYlGn6, RdYlGn9
from bokeh.sampledata.autompg import autompg
from bokeh.sampledata.unemployment1948 import data

# setup data sources
del data['Annual']
data['Year'] = data['Year'].astype(str)
unempl = pd.melt(data, var_name='Month', value_name='Unemployment', id_vars=['Year'])

fruits = {'fruit': ['apples', 'apples', 'apples', 'apples', 'apples',
                    'pears', 'pears', 'pears', 'pears', 'pears',
                    'bananas', 'bananas', 'bananas', 'bananas', 'bananas'],
          'fruit_count': [4, 5, 8, 12, 4, 6, 5, 4, 8, 7, 1, 2, 4, 8, 12],
          'year': [2009, 2010, 2011, 2012, 2013, 2009, 2010, 2011, 2012, 2013, 2009, 2010,
                   2011, 2012, 2013]}
fruits['year'] = [str(yr) for yr in fruits['year']]

hm1 = HeatMap(autompg, x=bins('mpg'), y=bins('displ'))

hm2 = HeatMap(autompg, x=bins('mpg'), y=bins('displ'), values='cyl', stat='mean')

hm3 = HeatMap(autompg, x=bins('mpg'), y=bins('displ', bins=15),
              values='cyl', stat='mean')

hm4 = HeatMap(autompg, x=bins('mpg'), y='cyl', values='displ', stat='mean')

hm5 = HeatMap(autompg, y=bins('displ'), x=bins('mpg'), values='cyl', stat='mean',
              spacing_ratio=0.9)

hm6 = HeatMap(autompg, x=bins('mpg'), y=bins('displ'), stat='mean', values='cyl',
              palette=RdYlGn6)

hm7 = HeatMap(autompg, x=bins('mpg'), y=bins('displ'), stat='mean', values='cyl',
              palette=RdYlGn9)

hm8 = HeatMap(autompg, x=bins('mpg'), y=bins('displ'), values='cyl',
              stat='mean', legend='top_right')

hm9 = HeatMap(fruits, y='year', x='fruit', values='fruit_count', stat=None)

hm10 = HeatMap(unempl, x='Year', y='Month', values='Unemployment', stat=None,
              sort_dim={'x': False}, width=900, plot_height=500)

output_file("heatmap.html", title="heatmap.py example")

show(column(
  gridplot(hm1, hm2, hm3, hm4, hm5, hm6, hm7, hm8, hm9,
           ncols=2, plot_width=400, plot_height=400),
  hm10)
)

```


### dots multiple
```python
from bokeh.charts import Dot, output_file, show, defaults
from bokeh.layouts import gridplot
from bokeh.sampledata.autompg import autompg as df

df['neg_mpg'] = 0 - df['mpg']

defaults.plot_width = 450
defaults.plot_height = 350

dot_plot = Dot(df, label='cyl', title="label='cyl'")

dot_plot2 = Dot(df, label='cyl', title="label='cyl' dot_width=0.4")

dot_plot3 = Dot(df, label='cyl', values='mpg', agg='mean', stem=True,
                title="label='cyl' values='mpg' agg='mean'")

dot_plot4 = Dot(df, label='cyl', title="label='cyl' color='DimGray'", color='dimgray')

# multiple columns
dot_plot5 = Dot(df, label=['cyl', 'origin'], values='mpg', agg='mean',
                title="label=['cyl', 'origin'] values='mpg' agg='mean'")

dot_plot6 = Dot(df, label='origin', values='mpg', agg='mean', stack='cyl',
                title="label='origin' values='mpg' agg='mean' stack='cyl'",
                legend='top_right')

dot_plot7 = Dot(df, label='cyl', values='displ', agg='mean', group='origin',
                title="label='cyl' values='displ' agg='mean' group='origin'",
                legend='top_right')

dot_plot8 = Dot(df, label='cyl', values='neg_mpg', agg='mean', group='origin',
                color='origin', legend='top_right',
                title="label='cyl' values='neg_mpg' agg='mean' group='origin'")

# infer labels from index
df = df.set_index('cyl')
dot_plot9 = Dot(df, values='mpg', agg='mean', legend='top_right', title='inferred labels')

output_file("dots_multi.html", title="dots_multi.py example")

show(gridplot(dot_plot,  dot_plot2, dot_plot3, dot_plot4,
              dot_plot5, dot_plot6, dot_plot7, dot_plot8,
              dot_plot9, ncols=2))

```


### dots
```python
from bokeh.charts import Dot, show, output_file

# best support is with data in a format that is table-like
data = {
    'sample': ['1st', '2nd', '1st', '2nd', '1st', '2nd'],
    'interpreter': ['python', 'python', 'pypy', 'pypy', 'jython', 'jython'],
    'timing': [-2, 5, 12, 40, 22, 30],
}

# x-axis labels pulled from the interpreter column, stacking labels from sample column
dots = Dot(data, values='timing', label='interpreter',
           group='sample', agg='mean',
           title="Python Interpreter Sampling",
           legend='top_right', width=600)

output_file("dots.html", title="dots.py example")

show(dots)

```


### donuts mulitple
```python
from bokeh.charts import Donut, show, output_file
from bokeh.layouts import column
from bokeh.sampledata.autompg import autompg

import pandas as pd

# simple examples with inferred meaning

# implied index
d1 = Donut([2, 4, 5, 2, 8])

# explicit index
d2 = Donut(pd.Series([2, 4, 5, 2, 8], index=['a', 'b', 'c', 'd', 'e']))

# given a categorical series of data with no aggregation
d3 = Donut(autompg.cyl.astype(str))

# given a categorical series of data with no aggregation
d4 = Donut(autompg.groupby('cyl').displ.mean())

# given a categorical series of data with no aggregation
d5 = Donut(autompg.groupby(['cyl', 'origin']).displ.mean(),
           hover_text='mean')

# no values specified
d6 = Donut(autompg, label='cyl', agg='count')

# explicit examples
d7 = Donut(autompg, label='cyl',
           values='displ', agg='mean')

# nested donut chart for the provided labels, with colors assigned
# by the first level
d8 = Donut(autompg, label=['cyl', 'origin'],
           values='displ', agg='mean')

# show altering the spacing in levels
d9 = Donut(autompg, label=['cyl', 'origin'],
           values='displ', agg='mean', level_spacing=0.15)

# show altering the spacing in levels
d10 = Donut(autompg, label=['cyl', 'origin'],
           values='displ', agg='mean', level_spacing=[0.8, 0.3])

output_file("donut_multi.html", title="donut_multi.py example")

show(column(d1, d2, d3, d4, d5, d6, d7, d8, d9, d10))

```


### donuts 
```python
from bokeh.charts import Donut, show, output_file
from bokeh.charts.utils import df_from_json
from bokeh.sampledata.olympics2014 import data

import pandas as pd

# utilize utility to make it easy to get json/dict data converted to a dataframe
df = df_from_json(data)

# filter by countries with at least one medal and sort by total medals
df = df[df['total'] > 8]
df = df.sort("total", ascending=False)
df = pd.melt(df, id_vars=['abbr'],
             value_vars=['bronze', 'silver', 'gold'],
             value_name='medal_count', var_name='medal')

# original example
d = Donut(df, label=['abbr', 'medal'], values='medal_count',
          text_font_size='8pt', hover_text='medal_count')

output_file("donut.html", title="donut.py example")

show(d)

```


### chord
```python
import pandas as pd
from bokeh.charts import output_file, Chord
from bokeh.io import show
from bokeh.sampledata.les_mis import data

nodes = data['nodes']
links = data['links']

nodes_df = pd.DataFrame(nodes)
links_df = pd.DataFrame(links)

source_data = links_df.merge(nodes_df, how='left', left_on='source', right_index=True)
source_data = source_data.merge(nodes_df, how='left', left_on='target', right_index=True)
source_data = source_data[source_data["value"] > 5]

chord_from_df = Chord(source_data, source="name_x", target="name_y", value="value")
output_file('chord_from_df.html', mode="inline")
show(chord_from_df)

```

### boxplot single
```python
from bokeh.charts import BoxPlot, output_file, show
from bokeh.sampledata.autompg import autompg as df

# origin = the source of the data that makes up the autompg dataset
title = "MPG by Cylinders and Data Source, Colored by Cylinders"

# color by one dimension and label by two dimensions
# coloring by one of the columns visually groups them together
box_plot = BoxPlot(df, label=['cyl', 'origin'], values='mpg',
                   color='cyl', title=title)

output_file("boxplot_single.html", title="boxplot_single.py example")

show(box_plot)

```


### boxplot multiple
```python
from bokeh.charts import BoxPlot, output_file, show, defaults
from bokeh.layouts import gridplot
from bokeh.sampledata.autompg import autompg as df

defaults.plot_width = 400
defaults.plot_height = 400

box_plot = BoxPlot(df, label='cyl', values='mpg',
                   title="label='cyl', values='mpg'")

box_plot2 = BoxPlot(df, label=['cyl', 'origin'], values='mpg',
                    title="label=['cyl', 'origin'], values='mpg'")

box_plot3 = BoxPlot(df, label='cyl', values='mpg', color='cyl',
                    title="label='cyl' values='mpg'")

# use constant fill color
box_plot4 = BoxPlot(df, label='cyl', values='displ',
                    title="label='cyl' color='blue'",
                    color='blue')

# color by one dimension and label by two dimensions
box_plot5 = BoxPlot(df, label=['cyl', 'origin'], values='mpg',
                    title="label=['cyl', 'origin'] color='cyl'",
                    color='cyl')

# specify custom marker for outliers
box_plot6 = BoxPlot(df, label='cyl', values='mpg', marker='cross',
                    title="label='cyl', values='mpg', marker='cross'")

# color whisker by cylinder
box_plot7 = BoxPlot(df, label='cyl', values='mpg', whisker_color='cyl',
                    title="label='cyl', values='mpg', whisker_color='cyl'")

# remove outliers
box_plot8 = BoxPlot(df, label='cyl', values='mpg', outliers=False,
                    title="label='cyl', values='mpg', outliers=False, tooltips=True",
                    tooltips=True)

output_file("boxplot_multi.html", title="boxplot_multi.py example")

show(gridplot(box_plot,  box_plot2, box_plot3, box_plot4,
              box_plot5, box_plot6, box_plot7, box_plot8,
              ncols=2))

```


### bars 
```python
from bokeh.charts import Bar, output_file, show, defaults
from bokeh.layouts import gridplot
from bokeh.sampledata.autompg import autompg as df

df['neg_mpg'] = 0 - df['mpg']

defaults.plot_width = 400
defaults.plot_height = 400

bar_plot = Bar(df, label='cyl', title="label='cyl'")
bar_plot.title.text_font_size = '10pt'

bar_plot2 = Bar(df, label='cyl', bar_width=0.4, title="label='cyl' bar_width=0.4")
bar_plot2.title.text_font_size = '10pt'

bar_plot3 = Bar(df, label='cyl', values='mpg', agg='mean',
                title="label='cyl' values='mpg' agg='mean'")
bar_plot3.title.text_font_size = '10pt'

bar_plot4 = Bar(df, label='cyl', title="label='cyl' color='DimGray'", color='dimgray')
bar_plot4.title.text_font_size = '10pt'

# multiple columns
bar_plot5 = Bar(df, label=['cyl', 'origin'], values='mpg', agg='mean',
                title="label=['cyl', 'origin'] values='mpg' agg='mean'")
bar_plot5.title.text_font_size = '10pt'

bar_plot6 = Bar(df, label='origin', values='mpg', agg='mean', stack='cyl',
                title="label='origin' values='mpg' agg='mean' stack='cyl'",
                legend='top_right')
bar_plot6.title.text_font_size = '10pt'

bar_plot7 = Bar(df, label='cyl', values='displ', agg='mean', group='origin',
                title="label='cyl' values='displ' agg='mean' group='origin'",
                legend='top_right')
bar_plot7.title.text_font_size = '10pt'

bar_plot8 = Bar(df, label='cyl', values='neg_mpg', agg='mean', group='origin',
                title="label='cyl' values='neg_mpg' agg='mean' group='origin'",
                legend='top_right')
bar_plot8.title.text_font_size = '9pt'

# infer labels from index
df = df.set_index('cyl')
bar_plot9 = Bar(df, values='mpg', agg='mean', legend='top_right', title='inferred labels')
bar_plot9.title.text_font_size = '10pt'

output_file("bar_multi.html", title="bar_multi.py example")

show(gridplot(bar_plot,  bar_plot2, bar_plot3, bar_plot4,
              bar_plot5, bar_plot6, bar_plot7, bar_plot8,
              bar_plot9, ncols=2))

```


### area
```python
from bokeh.charts import Area, show, output_file, defaults
from bokeh.layouts import row

defaults.width = 400
defaults.height = 400

# create some example data
data = dict(
    python=[2, 3, 7, 5, 26, 221, 44, 233, 254, 265, 266, 267, 120, 111],
    pypy=[12, 33, 47, 15, 126, 121, 144, 233, 254, 225, 226, 267, 110, 130],
    jython=[22, 43, 10, 25, 26, 101, 114, 203, 194, 215, 201, 227, 139, 160],
)

area1 = Area(data, title="Area Chart", legend="top_left",
             xlabel='time', ylabel='memory')

area2 = Area(data, title="Stacked Area Chart", legend="top_left",
             stack=True, xlabel='time', ylabel='memory')

output_file("area.html", title="area.py example")

show(row(area1, area2))

```



### versions
```python
from IPython import __version__ as ipython_version
from pandas import __version__ as pandas_version
from bokeh import __version__ as bokeh_version
print("IPython - %s" % ipython_version)
print("Pandas - %s" % pandas_version)
print("Bokeh - %s" % bokeh_version)
```


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


