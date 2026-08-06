# Useful software
Below is a list of useful software packages (predominantly `python` packages) for performing different computations and analysis.

---

# Lagrangian trajectory analysis
[**Parcels**](https://parcels-code.org/) is a highly customisable Lagrangian analysis tool that can be used with many different configurations of gridded velocity field. Some basic examples of doing this with the B-SOSE dataset are [here](https://github.com/gmacgilchrist/parcels_basics).

---

# CMIP Model Analysis

[**intake-esgf**](https://intake-esgf.readthedocs.io/en/latest/) is an Intake-inspired Python package for discovering, searching, and downloading climate model output from the Earth System Grid Federation (ESGF). It provides a convenient interface for accessing CMIP5, CMIP6, and other ESGF-hosted datasets directly from Python, making it much easier to build reproducible climate analysis workflows without manually navigating ESGF portals.

---

# Model Regridding

[**xESMF**](https://xesmf.readthedocs.io/en/stable/) is a universal regridding package for geospatial data built on top of the Earth System Modeling Framework (ESMF). It provides a simple interface for regridding NumPy and xarray objects using conservative, bilinear, nearest-neighbour, and higher-order interpolation methods. It is the standard tool for putting climate model output onto a common grid before performing multi-model analyses.

[**xgcm**](https://xgcm.readthedocs.io/en/latest/) is a package for working with general circulation model (GCM) output using finite-volume methods. It understands staggered model grids (such as Arakawa C-grids) and provides convenient tools for interpolation, differentiation, integration, and grid-aware calculations, making it valuable for diagnostics involving fluxes, gradients, and conservation laws.

[**cftime**](https://unidata.github.io/cftime/) is a Python library for handling the non-standard calendars commonly used in climate models, including no-leap, 360-day, and Julian calendars. It allows seamless reading, writing, and manipulation of climate model time coordinates and helps ensure compatibility with NetCDF Climate and Forecast (CF) conventions when working with xarray and CMIP datasets.

---

# Plotting and Data Visualization

[**Cartopy**](https://cartopy.readthedocs.io/stable/) is the standard Python library for creating maps and geospatial visualizations. Built on top of Matplotlib, it provides support for map projections, coastlines, political boundaries, gridlines, and geospatial transformations, making it ideal for visualizing climate model output and observational datasets on regional and global maps.

[**Matplotlib**](https://matplotlib.org/stable/) is the core plotting library for Python and forms the foundation of much of the scientific Python visualization ecosystem. It provides extensive functionality for producing publication-quality figures, including line plots, scatter plots, histograms, contour plots, heatmaps, animations, and highly customizable multi-panel layouts. Most climate analysis packages, including xarray, Cartopy, and xESMF, integrate directly with Matplotlib for visualization.

---
# Diagnostics and Calculations

[**MetPy**](https://unidata.github.io/MetPy/latest/index.html) is a comprehensive Python library for reading, visualizing, and performing calculations with meteorological data. It includes routines for atmospheric thermodynamics, kinematics, interpolation, cross-sections, unit-aware calculations, and common meteorological diagnostics, making it particularly useful for working with weather and reanalysis datasets.

[**windspharm**](https://ajdawson.github.io/windspharm/) is a Python package for performing computations on global wind fields in spherical geometry using spherical harmonic transforms. It provides efficient tools for calculating quantities such as divergence, vorticity, streamfunction, and velocity potential, making it especially valuable for studying large-scale atmospheric circulation and tropical dynamics.

[**xclim**](https://xclim.readthedocs.io/en/stable/indices.html) is a library built on xarray for computing a wide range of climate indices and diagnostics from gridded datasets. It implements many standardized indices defined by the Expert Team on Climate Change Detection and Indices (ETCCDI), including temperature and precipitation extremes, drought metrics, growing season diagnostics, and heatwave statistics, while integrating seamlessly with Dask for scalable analysis.

[**GSW-Python**](https://teos-10.github.io/GSW-Python/) is the Python implementation of the TEOS-10 Gibbs SeaWater (GSW) Oceanographic Toolbox. It provides routines for calculating seawater thermodynamic properties such as Conservative Temperature, Absolute Salinity, density, potential density, sound speed, and other derived quantities. It is the standard library for physical oceanography and is widely used for analysing observations and ocean model output.

[**PyCO2SYS**](https://mvdh.xyz/PyCO2SYS/) is a Python implementation of the CO2SYS marine carbonate chemistry calculator. Given any two carbonate system variables (e.g., pH, dissolved inorganic carbon, total alkalinity, or partial pressure of CO₂), it computes the complete seawater carbonate system, including carbonate ion concentration, saturation states, and buffer factors. It is widely used in ocean biogeochemistry and ocean acidification research.

[**easyclimate**](https://easyclimate.readthedocs.io/) is a Python package designed to simplify climate data analysis by providing a collection of commonly used diagnostics, statistical methods, and visualization tools built around xarray. It aims to streamline workflows for analysing gridded climate datasets, including reanalysis and climate model output, by providing high-level functions for climatologies, anomalies, trends, spatial statistics, and climate indices while maintaining compatibility with the broader scientific Python ecosystem.

---
# Statistics

[**SciPy**](https://scipy.org/) is the core scientific computing library built on top of NumPy, providing a comprehensive collection of algorithms for numerical analysis. It includes modules for optimization, interpolation, numerical integration, linear algebra, signal processing, spatial algorithms, image processing, and statistics. In climate and ocean science, SciPy is commonly used for curve fitting, filtering, spectral analysis, interpolation, and solving ordinary and partial differential equations, making it one of the foundational libraries in the scientific Python ecosystem.

[**xskillscore**](https://xskillscore.readthedocs.io/en/stable/index.html) extends xarray with a wide range of statistical metrics commonly used in climate science and forecast verification. It provides efficient implementations of correlation coefficients, RMSE, MAE, bias, Nash–Sutcliffe efficiency, contingency metrics, probabilistic verification scores, and many other diagnostics that operate directly on labelled multidimensional arrays.

[**eofs**](https://ajdawson.github.io/eofs/) is a Python package for Empirical Orthogonal Function (EOF) analysis of spatiotemporal data. EOF analysis is a widely used technique for decomposing variability into orthogonal spatial patterns and their associated time series (principal components), making it useful for identifying dominant modes of climate variability such as ENSO, the NAO, or the PDO.

[**arch**](https://bashtage.github.io/arch/) is a statistical library that includes tools for time-series analysis, bootstrapping, unit root tests, and ARCH/GARCH models. For climate applications, its bootstrap implementations—such as stationary and moving block bootstraps—are particularly useful for estimating confidence intervals while accounting for temporal autocorrelation.

[**scikit-learn**](https://scikit-learn.org/stable/) is the standard machine learning library for Python, providing tools for classification, regression, clustering, dimensionality reduction, feature selection, model validation, and preprocessing. It also includes many utilities for building reproducible machine learning workflows and exploratory data analyses.

[**statsmodels**](https://www.statsmodels.org/stable/index.html) is a comprehensive statistical modelling library that provides implementations of linear and generalized linear models, time-series analysis, hypothesis testing, ANOVA, mixed-effects models, and many other classical statistical techniques. It is particularly useful when model inference, parameter uncertainty, and statistical testing are required.

[**uncertainties**](https://pythonhosted.org/uncertainties/) is a Python package for performing calculations with quantities that have associated uncertainties. It automatically propagates uncertainties through mathematical operations, greatly simplifying error propagation. While primarily designed for scalar values, it can be adapted for use with xarray through appropriate wrapping.

[**connected-components-3d**](https://pypi.org/project/connected-components-3d/) is a high-performance library for identifying and analysing connected regions in two- and three-dimensional boolean arrays. It is particularly useful for detecting and tracking coherent spatiotemporal features, such as precipitation systems, heatwaves, atmospheric rivers, or other clustered climate phenomena.