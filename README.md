
Figure Handbook (Part I - Basics)
===
This is a guidline for generating figures in Python. Originally written for student in our lab ([Maddox lab](https://github.com/maddoxlab)). We will keep this file updating.

Author: Madeline Cappelloni, Tong Shan, Thomas Stoll(Maddox Lab)

Edit: Feb 18, 2024

# Python

Most graphics created in Python are created with the matplotlib package (which contains several functions modeled after MATLAB). Full documentation for matplotlib is found [here](https://matplotlib.org/stable/api/).

```Python
import matplotlib.pyplot as plt
```

## Figures and Axes

In python, each figure is a window in which graphics can be drawn. To create a figure:

```Python
plt.figure()
```
It is good practice to assign a figure "number" as an argument, which can then be used to reference that figure later.

Additionally, figures should be initialized with a size (by default, in inches).

```python
plt.figure(figsize=(3, 2)) # creates a 3"x2" figure window
```

Axes are the container for all graphics objects in python. Each figure can contain one or more axes. Axes can be referenced by a handle (that is set when the axes are created) or, if the axes in question have been modified last, they can be referenced by simply using "plt" or the handle "gca" short for "get current axes."

For example, the command "xlim" is used to limit the range of the x axis values. Any of the following usages are correct:

```python
ax = plt.figure(5)

ax.xlim([0, 100]) # option 1
plt.xlim([0, 100]) # option 2
plt.gca().xlim([0, 100]) # option 3
```
Using `plt.subplots()` is another great option to generate figures. This is widely used when ploting multiple plots in grids.
```python
fig, ax = plt.subplots()
fig.set_size_inches(3,2)
```
### Multiple axes in one figure

Often, it can be useful to partition a given figure into several axes. This can be done using the subplot function to divide the figure into an m by n grid of equally sized axes *or* with a more custom grid using gridspec. Note: if you're spending too much time customizing your grid layout, you might decide to assemble several smaller figures in another program.

#### Subplot
[Subplot](https://matplotlib.org/stable/api/_as_gen/matplotlib.pyplot.subplot.html) creates a set of axes that can be placed a simple m x n grid and specifies a position in that grid.

```python
plt.subplot(2, 3, 4) # a 2 x 3 grid, with the 4th position selected
plt.subplot(234) # an equivalent syntax for grids with <10 axes
```
Another way to do this is through `plt.subplots()`
```python
fig, axs = plt.subplots(2, 3)
```
The axs variable will be a numpy array containing the different axes (shape is [row, column], e.g. `axs[0, 0]` is the top left plot and `axs[-1, -1]` is bottom right).These are very useful when looping through the axes to plot.

Note that `plt.subplots` can also be used to make a 1x1 figure or a 1xn figure and axs will either be a single axis (not an array) or a 1D array, respectively, making it easier to index.

#### GridSpec
[GridSpec](https://matplotlib.org/stable/api/_as_gen/matplotlib.gridspec.GridSpec.html) can be used to create a custom grid, with different axes having different sizes.

First, the GridSpec object must be initialized. Here, we are setting up a 2x2 grid where the columns have the same width, but the top row is 3x taller than the bottom row.
```python
gs = GridSpec(2, 2, height_ratios=[3, 1])
```
Then, the axes within that object can be created by indexing the GridSpec object.
```python
ax = plt.add_subplot(gs[0]) # selects top left axes from grid

ax = plt.add_subplot(gs[0, :]) # creates a set of axes that includes entire top row
```
#### Keeping Axes Consistent

If your multiple panel figure involves common axes labels, those axes likely should all span the same range.

To link axes, you can use the sharex and sharey keyword arguments of the subplot function. For example:

```python
ax1 = plt.subplot(211)

plt.subplot(212, sharex=ax1, sharey=ax1)
```

Alternatively you may use the xlim and ylim functions (though this is considerably less efficient).

```python
xlims = [-10e-3, 40e-3]
ylims = [-12, 12]

plt.subplot(211)

plt.xlim(xlims)
plt.ylim(ylims)

plt.subplot(212)

plt.xlim(xlims)
plt.ylim(ylims)
```

If each panel in a *row* has the same *y*-axis label, the label can be omitted on all panels except the *leftmost* panel. If each panel in a *column* has the same *x*-axis label, the label can be omitted on all panels except the *bottom* panel.

Anther way to do it is through `plt.subplots()`:
```python
fig, axs = plt.subplots(2, 3, sharex=True, sharey=True, figsize=(3,4))
```

#### Spacing

Although matplotlib endeavors to space figure elements in a sensible way, it often allocates more white space than is needed (or crops things oddly). The tight_layout function maximizes the space taken up by the actual figure and its labels.

```python
# all other code to generate and customize your figure goes before tight_layout()

plt.tight_layout()
```
or passing it when saving plots in the `bbox_inches` arg:
```python
plt.savefig(..., bbox_inches='tight')
```

If your figure is particularly complex or has larger text, the `tight_layout()` function may also fail to give you a sensible layout. The `subplots_adjust()` function can remedy this situation.

First, click the spacing options (icon between the magnifying glass and jagged arrow). Here, manually adjust the borders and spacings options until you like the look of the figure.

![](https://i.imgur.com/brTJHyO.png=450x)

Then, you can add these values (as many or as few as you wish to change) to your code:
```python
plt.subplots_adjust(right=0.95, hspace=0.2)
```

### Spines
By default, each set of axes created by matplotlib is bounded by "spines." Typically, the right and top spines are useless---they do not correspond to the x or y axis--and thus should be removed. The following code will achieve this:

```python
plt.gca().spines['top'].set_visible(False)
plt.gca().spines['right'].set_visible(False)
```
In rare cases you may wish to also remove the spines corresponding to the normal placement of the x- and y-axis. Doing the following will remove the spines but not the ticks:

```python
plt.gca().spines['left'].set_visible(False)
plt.gca().spines['bottom'].set_visible(False)
```

To also remove the ticks of the x- and y-axis:

```python
plt.gca().xaxis.set_visible(False)
plt.gca().yaxis.set_visible(False)
```
For plots in log scale like this, sometimes you may want to remove the minor ticks

<img src="https://i.stack.imgur.com/qgW3g.png" width="250" height="250" />

you can do this via `ax.minorticks_off()` or `ax.xaxis.set_tick_params(which='minor', bottom=False)`.


### Grid Lines

For some figure, vertical and/or horizontal grid lines may help viewers better assess the size of an effect or estimate timecourses.

To add grid lines:

```python
# by default, both horizontal and vertical grid lines are included
plt.grid(True)
# horizontal gridlines only
plt.grid(True, axis='y')
# horizontal gridlines only
plt.grid(True, axis='x')
```
For specific axis, this can be done by `ax.grid(True)`.

### Saving figures
After carefully creating your figure, you can save in python using the savfig() function. This function will save the figure every time you update it and run your analysis code.

Importantly, the type of file should depend on whether and how you are editing it in another program. If the figure will be edited in Adobe Illustrator or Adobe Photoshop, save it as a pdf. If it will be edited in Microsoft PowerPoint, save it as a png. If the file is going directly into a manuscript or onto slides, it should also be saved as a png (unless you're using LaTeX for your manuscript/slide preparation).

Always specify the resolution of images in DPI (dots per inch). 300 is sufficient for most applications, but check the resolution requirements of the journal. Also, if your saved figure looks blurry, increase the DPI.

```python
# all figure creation code
 # if editing in Adobe programs afterwards or adding to a LaTeX document
plt.savefig('path/filename.pdf', dpi=300)
# if next destination is part of Microsoft Office
plt.savefig('path/filename.png', dpi=300)
```
> [!TIP]
> Recent journals prefer line art to be vector graph with format like `eps` or  `tiff` using high dpi (>1000). A vector image use mathematical expressions  instead of pixels. But you can still use `png` in your text document for initial submission.


## Text

Text is a critical part of all figures. It should be brief but descriptive, consistently formatted, and, above all, legible.

### Font and size

The font and size of text is critical for making it legible. Most figures are viewed via a screen, and thus should use a sans serif font---Arial is generally a good choice.

The size of the text will depend on the application, see chart comparing figures for manuscripts, posters, and slides.

**Always check the author guides for minimum text size in figures

Text can be formatted for all new figures using the rcParams and rc packages contained in matplotlib.

```python
from matplotlib import rcParams
from matplotlib import rc

rcParams['font.sans_serif'] = "Arial"
rc('font', size=12)
```
When you need a specific font, use `matplotlib.font_manager.addfont()`.

The size of individual elements can also be changed when they are created or modified. Some examples:

```python
plt.legend(('Label 1', 'Label 2'), prop={'size': 8})
plt.xticks(np.arange(10), fontsize=8)
plt.title('My title', fontdict={'fontsize': fontsize})
```

### Numbers, Special Characters, and Mathematical Formating

In some cases, you may wish to include "non-string" data in your figure labels.

#### Numbers
If you want to include a variable value in a label. This can be done with string formating using the {} characters as a placeholder for the numerical value, and the format() method to convert the value to a string representation.

```python
'Means n={}'.format(len(subjects))
'a={}, b={}'.format(a, b)
```

There are many options in string formatting: [a cheat sheet reference](https://kapeli.com/cheat_sheets/Python_Format_Strings.docset/Contents/Resources/Documents/index).

#### Special Characters

Often, we may wish to include a special character in our labels, (e.g., the degree symbol). Any symbol included in the [unicode](https://en.wikipedia.org/wiki/List_of_Unicode_characters) library can be rendered by python if the letter u is placed before the string.

```python
plt.xlabel(u'Degrees (°)')
```

#### Mathematical Formatting

Python is also able to render text with LaTeX formating. This is cued by flanking the rendered text with $ symbols. Some simple symbols can be rendered without explicitly enabling LaTeX rendering (and thus, in the specified font). More complex expressions require LaTeX rendering to be turned on.

```python
rcParams['text.usetex'] = True # enables LaTeX rendering

plt.ylabel('$V_0$') # creates a subscript 0
```

There are also many symbols possible in LaTeX: [a cheat sheet reference](https://kapeli.com/cheat_sheets/LaTeX_Math_Symbols.docset/Contents/Resources/Documents/index)

### Legends

Legend entries can be specified in two ways. First, the labels can be specified in the legend function itself in order of created linestyles.

```python
plt.plot([0, 1], [0, 1])
plt.plot([0, 1], [0, 2])

plt.legend(('Slope=1', 'Slope=2'))
```

Alternatively, labels can be assigned when the graphic elements are created. Then, the legend function will simply create a legend with the labels previously specified. This second method is often preferable when plotting individual subjects with the same line style.

```python
plt.plot([0, 1], [0, 1], label='Slope=1')
plt.plot([0, 1], [0, 2], label='Slope=2')

plt.legend()
```

A *useful* legend can be tricky to place in a well formatted figure due to the density of data, and size of a legend with truly descriptive labels. The first trick is to place the legend somewhere where it will not cover data by restricting it to one of the following positions: 'upper left', 'upper right', 'lower left', 'lower right', 'upper center', 'lower center', 'center left', 'center right', 'center'.

The string 'best' places the legend at a location deemed optimal by matplotlib.

If this fails, you can try to move the legend outside of the figure by including a bbox_to_anchor argument in conjunction with one of the strings. This will modify the location based on the anchor. The first element of the tuple corresponds to a shift in the x direction and the second element corresponds to the y direction. Some trial and error may be required to optimize the placement. Additionally, a subplots_adjust() command may be necessary to prevent the legend from falling off the edge of the figure.

```python
plt.legend(bbox_to_anchor=(1, 1), loc='upper left')
```

If all else fails, the size of the text in the legend can be reduced by a few points.

```python
plt.legend(prop={'size': 8})
```

There might be more lines/patches/etc. in the figures than you want in the legend. You can use something like `matplotlib.lines.Line2D` or `matplotlib.patches.Patch` to create an array of the lines/patches with the colors and styles you want. Then pass that into the legend function using the handles argument.

```python
legline = [Line2D([0], [0], color='r', linestyle='--', label='Test 1'),
           Line2D([0], [0], color='r', linestyle=':', label='Test 2'),
           Patch(color='g', label='Test 3')]
fig.legend(handles=legline)
```

### Annotations

Figures can also be annotated using the [annotate](https://matplotlib.org/stable/api/_as_gen/matplotlib.pyplot.annotate.html) method or [more advanced options](https://matplotlib.org/stable/tutorials/text/annotations.html#plotting-guide-annotation).

### X/Ytick labels

In addition to labeling axes (xlabel and ylabel), the ticks on those axes can be customized. First, you can customize the values marked:

```python
plt.xticks([0, 25, 50, 75, 100])
```
You can also change the text at these locations
```python
plt.xticks([0, 25, 50, 75, 100], ['abysmal', 'bad', 'meh', 'good', 'amazing'])
```
For xtick labels that are longer strings, the rotation parameter allows you to make that text vertical.
```python
plt.xticks([0, 25, 50, 75, 100], ['abysmal', 'bad', 'meh', 'good', 'amazing'],
          rotation=90)
```
For setting in axes:
```python
# for x ticks
ax.set_xticks()
# for y ticks
ax.set_xticks()
```


## Data elements

### Line Styles and Linewidth
For line plots, we use line styles and line width to indicate different groups/types of data. (See examples here.)
```python
# To draw a solid line with a linewidth of 1.5 point
ax.plot(x, y, linestyle="solid", linewidth=1.5)
# To draw a dashed line with a linewidth of 0.5 point
plt.plot(x, y, linestyle="--", linewidth=0.5)
```
You can use strings or symbols to specify simple line styles. E.g., `linestyle="dashed"`, which is equal to `linestyle='--'`.

| Line style  | Strings   | Symbols |
| --------    | --------  | --------|
| Solid       |`"solid" ` |`"-"`    |
| Dashed      |`"dashed"` | `"--"`  |
| Dotted      | `"dotted"`| `":"`   |
| Dash dotted |`"dashdot"`| `"-."`  |

To precisely control the line style, you also use a tuple in form of `(offset, (on_off_seq))`. For a loosely dash dotted line, `(0, (3, 10, 1, 10))` means a 3pt line with 10pt space and then a 1pt line with 10pt space.

### Marker and Marker Size
Marker styles are another way to distinguish different groups/types of data points, especially in scatter plot and line plot with errorbar.
```python
# To draw plot with circle as data points and size of 10pt
ax.plot(x, y, marker='o', markersize=10)
# To draw scatter plot with a dot as data points
plt.scatter(x, y, marker='.')
```
Some commonly used markers are:

| Description | Marker |
| --------    | --------  |
| Dot         |`"." ` |
| Circle      |`"o"` |
| triangle    | `"^"`|
| Inverted triangle |`"v"`|
| Square      |`"s"`|
| Star        |`"*"`|

All possible markers can be found [here](https://matplotlib.org/stable/api/markers_api.html#module-matplotlib.markers).

You can specify **marker fill styles** with the argument `fillstyle`. For example, to make a plot with 15pt triangle markers (`marker='^'`) are half-filled on left side (`fillstyle='left'`) with red (`markerfacecolor='r'`) and the other half side with blue (`markerfacecoloralt='b'`), combined with green dashed line style (`linestyle='--'`, `color='g'`):
```python
marker_fill_style = dict(marker='^', linestyle='--', markersize=15, color='g',
                          markerfacecolor='r', markerfacecoloralt='b')
ax.plot(x, y, fillstyle='left', **marker_fill_style)
```

You can also **customize** your own markers from TeX symbols and `Path` object.
```python
# To use the text "1" as the marker
ax.plot(x, y, marker='$1$')
# To use the mathematical symbol pi as the marker
ax.plot(x, y, marker='$\pi$')
```
```python
# To use the 6 vertex star created by Path as the marker
import matplotlib.path as mpath
star = mpath.Path.unit_regular_star(6)
ax.plot(x, y, marker=star)
```

### Mechanics of changing color
In Matplotlib, we can specify color for data, color for text, and facecolor. We usually specify colors in argument `c=' '` or `color=' '`.
```python
ax.plot(x, y, color='black') # draw a line with black
plt.plot(x, y, c='green') # draw a line with green
```
#### Types of color
There are several formats to specify colors in [Matplotlib](https://matplotlib.org/stable/tutorials/colors/colors.html).

**RGB or RGBA tuple** of float in the inteval of [0,1]:
```python
# (red, green, blue), value indicate intensity
c = (1, 1, 1) # Pure white
c = (0.5, 0.5, 0) # Olive
c = (0, 0, 0, 0.5) # Black with 0.5 alpha
```

**Hex RGB color code**: a six digit string in a form of `#RRGGBB`, each two-digit representing one color in hexadecimal. If including alpha, it will be an eight digit string in a form of `#RRGGBBAA`. For example, `#ffffff` is pure white, and `#808000` is olive.
```python
c = '#ffffff' # Pure white
c = '#808000' # Olive
c = '#0f0f0f80' # Black with 0.5 alpha
```
**Shorhand hex RGB / RGBA string** can also work if there are duplicated characters. For instance, `#aabbcc` is equivalent to `#abc`.

The Hex and RGB/RGBA tuples can be converted to each other using `marplotlib.colors.to_hex()` or `marplotlib.colors.to_rgb()`.

**Single character for basic colors**
```python
c = 'w' # white
c = 'r' # red
c = 'g' # green
```
**Matplorlib default color cycle is the Tableau Colors** from 'T10' categorical palette.
The default color cycle is [blue, orange, green, red, purple...] as shown in the palette below:

![](https://i.imgur.com/v80OVXN.png)

This color cycle dictates the default color, the color of plot on the same figure will in an order of the cycle. While this default cycle is acceptable for "internal" figures, specific colors should be assigned for any publication/presentation.
```python
c_blue = 'tab:blue'
c_blue = 'C0' # same as 'tab:blue'
c_orange = 'tab:orange'
c_orange = 'C1' # same as 'tab:orange'
```
You can define your own color cycle using `Axes.set_prop_cycle()`. For more information, please refer to the document [here](https://matplotlib.org/stable/tutorials/intermediate/color_cycle.html).


[**X11/CSS4 color names**](https://en.wikipedia.org/wiki/X11_color_names): map certain strings (with no spaces) to a color value.
e.g., `'aquamarine'`=`#7FFFD4`, `'mediumseagreen'`=`#3CB371`.

[**xkcd color**](https://xkcd.com/color/rgb/): Color name from xkcd color survey with `'xkcd:'` prefix.
e.g., `'xkcd:sky blue'`=`#75bbfd`, `'xkcd:olive'`=`#6e750e`.

#### Alpha
To change the transparancy of the art on your plot, we always use the argument `alpha=`. It has the interval of [0,1], where 0 means 0% opacity (completely transparent) and 1 means 100% opacity (completely opaque).



Alpha is useful when showing individual subject data behind the means. It is also often used in `fill_between`.
```python
ax.plot(x, y)
ax.fill_between(x, y-e, y+e, alpha=0.2)
```

#### Colorbar and Colormaps
Colormap and colorbar can be used when creating color visualization for scaler values, such as heatmap. Colormap can be specified in the argument `cmap=` when plotting.
For example, to create a scatterplot with a colorbar:
```python
plt.scatter(x, y, c=scaler, cmap="summer")
plt.colorbar(location="right", orientation="vertical")
```
To create a heatmap with a colorbar:
```python
im = ax.imshow(data, cmap="seismic")
plt.colorbar(im, ax=ax)
```
Find all the built-in colormaps [here](https://matplotlib.org/stable/tutorials/colors/colormaps.html
).
You can also create your own colormaps using the function `matplotlib.color. LinearSegmentedColormap.from_list()`. More information is available [here](https://matplotlib.org/stable/gallery/color/custom_cmap.html#sphx-glr-gallery-color-custom-cmap-py).

>[!NOTE]
Note that colorbars have their own axes, so all the previous functions can be used on them (e.g. set ticks, title, label, etc.).

### Error and CI		 

#### SEM
We often use **standard error (SE)** to estimate the variability of a population parameter (the mean, for example).
There are two points to note :
1. SE is **NOT** standard deviation (std). Std only gives the variability within a sample.
2. We usually calculate the SE of the mean (**SEM**) for statistical analysis, but SE can be calculated for other population estimates too, such as the median.

SEM is calculated by the equation
$$SEM={std \over \sqrt{n}}$$
where $std$ is the standard deviation of the sample and $n$ is the number of element in that sample.
To calculate SEM in python manually or using `scipy.stats` package:
```python
import numpy as np
import scipy.stats as st
# For example, we randomly generate 10 numbers from a normal distribution
sample = np.random.randn(10)
n = len(sample) # n=10 in this case
# To calculate the SEM manually
std = np.std(sample)
sem = std / np.sqrt(n)
# Or use scipy.stats
sem = st.sem(sample)
```
To show the SE of the mean (SEM), we use the function `errorbar` or `fill_between`.
Suppose that `sem` is the SEM of the sample. To plot with `errorbar`:
```python
plt.errorbar(x, y, yerr = sem, uplims=True, lolims=True)
```
> X axis error bar can be plotted by using argument `xerr=`.

To plot with `fill_between`:
```python
# Fisrt, plot the mean estimate
plt.plot(x, y)
# Then, plot the error with transparant color
plt.fill_between(x, y-sem, y+sem, alpha=0.2)
```


#### Analytical Confidence Intervals
SEM is one of the point estimate to show the population mean. Sometimes, we use **confidence interval (CI)**.
Definition of CI from *Oxford Languages*:
> Confidence interval (CI) is a range of values so defined that there is a specified probability that the value of a parameter lies within it.

CI for the estimated mean value is calculated by
$$\bar{X} \pm t_{df} {std \over \sqrt{n}}$$
where $\bar{X}$ is the averaged value of the sample. For $t_{df}$, use the t table with df=n-1.
To calculate CI in python:
```python
import numpy as np
import scipy.stats as st
# For example, we randomly generate 10 numbers from a normal distribution
sample = np.random.randn(10)
# To calculate the 95% CI
n = len(sample) # n=10 in this case
CI = st.t.interval(alpha=0.95, df=n-1, loc=np.mean(sample),
                  scale=st.sem(sample))
```
For large sample of data when the Central Limit Theorem apply, we can also replace $t$ value with $z$ value.

#### Numerical Estimation of Confidence Intervals

Confidence intervals of a between subjects mean can also be numerically determined by resampling from the subjects themselves many times (here we use 1000 as it is sufficiently large and makes indexing easy later).

The following example assumes the data from one condition is stored in an array called data with size Nx1. The same process can be extended for more complex data sets with multiple conditions---take care with dimensions.

```python
resampled_means = np.zeros((1000,))

for n in np.arange(1000):
    new_sample = np.random.choice(data, n_subjects, len(subjects), replace=True)
    # setting replace to True is incredibly important,
    # else you will just take the same sample every time
    resampled_means[n] = new_sample.mean()
```

Once you have generated samples, we can determine the range where 95% of the mean values lie. This can be achieved by sorting them and looking at the 25th (2.5%) and 975th (97.5%) values.

```python
lower_CI = np.sort(resampled_means)[24]
upper_CI = np.sort(resampled_means)[-25]
```

Then to plot this CI as an errorbar:
```python
plt.errorbar(x, data.mean(), y_err=[data.mean()-lower_CI, upper_CI-data.mean()])
```
Importantly, the errorbar function does not take lower and upper values of the CI directly, but rather wants the distance of the CI bounds from the mean.

To plot this CI using `fill_between`:
```python
plt.fill_betweem(x, lower_CI, upper_CI, alpha=0.2)
```

### Order

The default behavior of matplotlib when multiple data elements overlap is to put the most recent first. However, this order can be explicitly set with the zorder argument.

```python
# makes a horizontal line at 0 and send it to the back of the figure
plt.plot([0, 1], [0, 0], 'k:', zorder=-100)
# plots mean data and brings it forward
plt.scatter(x, y.mean(), zorder=50)
```

### Showing individual subjects & means on same plot
To show individual data points on top of global estimate (e.g., mean with error), we can create multiple data element on the same figure or axis. Typically, individual data can be shown as scattered points (markers) on top of a barplot, boxplot, or lines.

Depending on your purpose of the plot, be careful with the order of global estimates and individual data. Use color, alpha, line style/line width, and marker/marker size to discriminate them.

For example, to plot individual data points on top of a fitted regression line with shaded area of error:
> Suppose `y_est` is the estimated value based on the regression model, and `y_err` is the error
```python
# First, plot a solid blue regression line
ax.plot(x, y_est, '-', color='b')
# Second, plot the error of the regression line in transparant
ax.fill_between(x, y_est - y_err, y_est + y_err, color='b', alpha=0.2)
# Third, plot the individual observed data points as black dots
ax.plot(x, y, 'o', color='k')
```

## Styles
You can choose your preferred visual appearance to make your figure style consistent by using `rcParams`, built-in Style sheets or creating your own styles.
### rcParams
`rcParams` is used to change the runtime configuration. For example, to change the default line width and line style:
```python
import matplotlib as mpl
# Use rcParams
mpl.rcParams['lines.linewidth'] = 4
mpl.rcParams['lines.linestyle'] = '--'
# Use matplotlib.rc to change setup in a single line
mpl.rc('lines', linewidth=4, linestyle='--')
# You can also use plt.rc
plt.rc('lines', linewidth=4, linestyle='--')
```
All parameters associated with `rcParams` can be found [here](https://matplotlib.org/stable/api/matplotlib_configuration_api.html#matplotlib.rcParams).

### Style sheets
Some built-in styles that contains different `rcParams` setup is called style sheets. Use `plt.style.use()` to specify a style.
For example, use the style called "tableau-colorblind10":
```python
mpl.style.use("tableau-colorblind10")
# Or
plt.style.use("tableau-colorblind10")
```
You can also create your own style sheets. More information about this can be found [here](https://matplotlib.org/stable/tutorials/introductory/customizing.html#defining-your-own-style).
## Packages
### Seaborn
**Seaborn** is a useful library for statistical data visualization and stylization. It works well with objects in NumPy and [Pandas](https://pandas.pydata.org/) data structure. Full documentation is available [here](https://seaborn.pydata.org/).

### OpenCV (CV2)
**OpenCV** can be used for image manipulation. Full documentation is available [here](https://docs.opencv.org/4.x/d6/d00/tutorial_py_root.html).

### MNE
**MNE** is the package we use to manipulate and visualize EEG data.
Full documentation of MNE is available [here](https://mne.tools/stable/index.html).

Specifically, `Evoked` object is useful when visualizing both evoked potential and derived response from deconvolution.
An example of making `Evoked` object and then plotting is:
```python
import mne
# Give montage setup
montage = mne.channels.make_standard_montage("standard_1020")
# Give channel names: you can get channel names from raw.ch_names
info = mne.create_info(channel_names, sfreq=10000, ch_types='eeg')
# Suppose we have all the response with time starting from -0.2 s
Evoked = mne.EvokedArray(derived_response, info, tmin=-0.2)
Evoked.set_montage(montage)
Evoked.nave = 240 # response averaged from 240 epochs
# Make a joint plot of channel time and topographies at several time point
Evoked.plot_joint(times=[9.3e-3,11.4e-3,15.4e-3,20.5e-3,28e-3,35e-3,51e-3,95e-3,
  150e-3], ts_args=dict(ylim=dict(eeg=[-4, 6]),xlim=[-20, 250],
  units='Magnitude (AU)',time_unit='ms',gfp=True),
  topomap_args=dict(vmin=-4.5,vmax=6,time_unit="ms"),title=None)
```
The code will make a plot like this

![](https://i.imgur.com/IPr4jyv.png)


> We will focus on generating plots useing **MNE** in next tutorial! 
(Doc in prep!)


# Other programs

## Assembling multiple panel figures outside of python
For figures/images that need to be assembled into one figure with multiple panels, we could use graphic editing programs, such as Adobe Illustrator, Adobe Photoshop, Inkscape, MS PowerPoint, etc. There are also web-based editors, such as photopea.

Things need to be keep in mind when creating multiple panel figures:

- Try to keep the font and text size consistent across the panels.
- Plan the general layout and figure size for each panel figure before you assemble them. Check the manuscript/poster guid.
- Use required resolution (dpi) and figure size when creating a new working file.
- When importing individual panel figures, use `file -> import` instead of dragging.
- It is better to keep figures with their original sizes. Even if you need to resize them, do NOT change the ratio.
- Label each panels in order, so that we can refer to them in caption and munuscript. The labels are typically English letters.
- Try to make panels aligned well, vertically and horizontally. (In most programs, you can use "page grid" to aid lining up your panel figures.)

## Making methods/explanation figures
Additional figures can be created to explain the methods or other technical aspects of a project.

If you are depicting an analysis process, construct a flowchart.

To explain a trial format, the figure will likely follow the format of a timeline.

General principles are as follows:

- Avoid extraneous detail but label as many elements as possible
- Group related concepts by color, position, etc.
- Avoid using images of faces if possible (most journals require explicit consent in order to use these images)
- When a methods figure needs to show the top down position of a subject, we use a graphic from the classic [Mills 1958](https://asa.scitation.org/doi/pdf/10.1121/1.1909553) paper (file available on Box).

## Spacing/distributing/aligning elements

Most programs have options to ensure that graphical elements can be precisely aligned. Check the vertical and horizontal of all elements that are roughly in a row (viewers will notice discrepancies and consider them to be sloppy). Additionally, elements that are roughly evenly spaced should be precisely distributed.

## Exporting figures
Be sure that your exported figures meet the requirement of resolution and size. Most journals will require a vector format of figures that contains text / line art to keep the full resolution. So save those figure as `.eps` or `.pdf`. For other common figures, save as `.tiff`, `.png`, or `.jpg`.
# Good practices
## Differences between Figures for Manuscripts, Posters, and Slides



|      | Manuscript | Poster | Slides
| -------- | -------- | -------- | --------
| Figure Size Constraints     |1 Column: 3.5" wide<br />2 Column: 7.2" wide<br /><br />Figure cannot extend past the length of one page   | See conference guidelines for poster size     |  Widescreen (16:9) Slides: 13.33" x 7.5"<br /><br />Standard (4:3) Slides: 10" x 7.5"
| Text Size Minimums | 8 pt | 28 pt | 18 pt
|Resolution | See author guide | See printer and file type capabilities | See projector resolution capabilites
| Color Restrictions | Some print journals may require black and white/greyscale versions (or additional money for color) | Print friendly colors (CMYK) | Screen friendly colors (RGB), some projectors will impact hue---avoid similar colors
| Tools to guide viewers through the figure | Captions/Surrounding text<br /><br />Panel Labels (what order should viewers examine panels?) | Pointing<br /> <br />Annotations/Figure titles<br /><br />Captions/Surrounding text | Animation (step figures in with empty axes, then gradually add data elements)<br /><br />Annotations/Slide Titles

## Color

The two main concerns when picking colors is that they 1) are colorblind-friendly and 2) refer to consistent concepts.
### Colorblind-friendly Schemes


The following color schemes are excerpted from [Paul Tol's Colour Schemes document](https://personal.sron.nl/~pault/data/colourschemes.pdf) which is worth reading in full. The color schemes here can be considered a cheat sheet.

![](https://i.imgur.com/tNaXdNg.png)
![](https://i.imgur.com/vqx5m4i.png)

### Colors that Represent Concepts
Choosing consistent colors is a topic well covered by https://blog.datawrapper.de/colors/.

The highlights of this blog post are:
- colors should be used consistly throughout the work to represent the same calculation, condition, etc.
- use colors that are easily distinguishable
- exploit common associations of colors with ideas(e.g., red=right, blue=left)
## Caption guide

A caption contains several parts in the following order.

1. **A topic sentence:** what does the figure show? what conclusion should we make from viewing it?
2. **A description of each panel:** Using panel labels (A, B, C...) or positions (top, bottom left, center, etc.) introduce what is shown in each panel.
3. **General information about design language that is not included in the figure itself:** You might have shown means and individuals subjects but not specified linestyles in the legend (e.g., transparent thin lines show individual subjects, thick black lines show means across subjects.). You might have included error bars in the figure (e.g., errorbars show SEM.). You might have indicated statistical significance (e.g., asterisks show statistical differences with p<0.05). Or others?
## Good and bad figures examples

A bad figure:

![](https://i.imgur.com/cLApy5D.png)

The following corrections are necessary:
* meaningful color scheme applied
* spines removed
* figure size set
* text size set
* remove weird text rotation
* consistent y limits
* linewidth reduced
* redundant labels removed
* legend moved to not overlap data

A good figure:

![](https://i.imgur.com/ZUYi8KP.png)


Another bad figure:

![](https://i.imgur.com/vVVpx98.png)

This is a figure meant to show the auditory brainstem response (ABR) to music and speech.
The following corrections are necessary:
- line style discrimination between the two lines (in case the journal publish the papers in B&W).
- spines removed
- grid added
- a vertical line indicating the starting time point added
- text size set, legend text size match others
- unit for y label
- transparancy decreased for error region

A good figure:

![](https://i.imgur.com/t2uXYtK.png)



## Checklist

### Figure formatting
- [ ] Figure is appropriate size
- [ ] Each panel has labels
- [ ] Each panel has no upper or right spines

### Text
- [ ] Text is appropriately sized
- [ ] All axes are labeled (unless they are in a grid)
- [ ] If there are multiple data styles, there is a legend
- [ ] No broken text formatting

### Data
- [ ] Each "data element" has a specific color and style
- [ ] Markers and lines are appropriately sized
- [ ] Error in subject means is shown and/or individual subjects' data are shown
- [ ] The order of data overlay is correct

### Other
- [ ] Colors are colorblind friendly
- [ ] Manuscript/conference/other guidelines are followed
- [ ] Resolution is high enough
- [ ] The "design language" throughout the manuscript/poster/slides is consistent (e.g., text size/font, colors/line/marker styles, etc.)
