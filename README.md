# xlma-python
A future, Python-based version of xlma?

XLMA is a venerable IDL GUI program that diplays VHF Lightning Mapping Array data. It was originally written by New Mexico Tech in the late 1990s. The purpose of this repository is to collect ideas, sketches of code, and a community that could facilitate rewriting xlma in Python.

Please use the issues tracker to discuss ideas and pull requests to contribute examples.

# Technical architecture

We envision a two-part package that keeps a clean separation between the core data model, analysis, and display. XLMA utilized a large, global `state` structure that stored all data, as well as the current data selection corresponding to the view in the GUI. Analysis then operated on whatever data was in the current selection.

## Data model and subsetting

`xarray` is the obvious choice for the core data structure, because it supports multidimensional data with metadata for each variable, subsetting of all varaibles sharing a dimension, and fast indexing. Data can be easily saved and read from the NetCDF format, and converted from ASCII to `Dataset` using standard tools.

## Analysis

Some core features of LMA data analysis will be built in, TBD after surveying capabilities in XLMA.

## Display

Keeping the core data structure and selection operations separate from dislpay is good programming practice. It is doubly important in Python, where there is not one obvious solution for high performance *and* publication-quality graphics as in IDL.

### Plotting library

There are many options, so we want a design that:
1. Permits a GUI to provide the bounds of the current view (or a polygon lasso) to the data model, changing the subset
2. Allows many GUIs to read from the data model so that we maintain loose coupling as the GUI and plotting landscape evolves.

- Matplotlib: publication quality figures, mature, and can plot everything, including weather symbols. Perhaps too slow for interactive, colormapped scatterplots of O(1e6) points, as is common for LMA data.
- Vispy: OpenGL, so can display plots quickly, but will require more low-level coding. See the SatPy/PyTroll/SIFT efforts.
- ipyvolume: originally written for volume rendering, but has grown to display points, etc. Browser/notebook based.
- Bokeh/holoviews/geoviews: browser-based; Javascript and WebGL front end with Python backend. Works well with Binder.
- Datashader might be useful as a method of data reduction prior to visualization even if we don't use Bokeh.

### GUI

There is no obvious choice here, either.

- Jupyter notebooks
- JupyterLab with custom plugin
- PyQT

# Prior art

- [`lmatools`](https://github.com/deeplycloudy/lmatools/)
  - Includes readers for LMA and NLDN data (using older methods from 2010)
  - Flash sorting and gridding
  - Has code for all necessary coordinate transforms.
- [`brawl4d`](https://github.com/deeplycloudy/brawl4d/) A working version of the basic GUI functionality of xlma.
  - Based on matplotlib; plots can be drag-repositioned. Slow for large numbers of data points.
  - Includes charge analyis that auto-saves to disk
  - At one point, could display radar data underneath LMA data
  - Built around a data pipeline, with a pool of data at the start, subsetting, projection, and finally display.
