# project-template
A template repository for future projects.

This template provides a layout of directories useful for my coding related projects. It also suggests different tools that could be used to manage the projects. The assumption is that Python will be the coding language and Jupyter notebooks will be heavily used for investigative work. 

## Initialisation



## pixi

pixi is a package management tool leveraging the existing _conda_ ecosystem to obtain packages written in Python, C, C++, and many other languages. Like _uv_ it allows reproducibility through dedicated, isolated environments that can be easily recreated. It ensures compatibility across Linux, macOS, Windows, and more.


* Install pixi if you don't have it already

`curl -fsSL https://pixi.sh/install.sh | sh`

See https://pixi.sh/latest/ for details.

* Initialize a new pixi project

`pixi init` 

or specify a project name:

`pixi init geo_project`

* Ensure _conda-forge_ is the default channel in pixi.toml
```
[workspace]
channels = ["conda-forge"]
name = "geo_project"
platforms = ["linux-64"]
version = "0.1.0"
description = "Geospatial analysis project"
```

* Add geospatial dependencies to the pixi.toml file in a section appended to that shown above
```
[dependencies]
python = ">=3.10"
gdal = ">=3.10.3,<4"
rasterio = "==1.4.3"
```

* Install dependencies defined in pixi.toml

`pixi install`

* Add more packages to your environment

`pixi add package_name`

Example: `pixi add hvplot`

* Running Jupyter notebooks

`pixi run jupyter notebook`

  * or define custom tasks in pixi.toml
```
[tasks]
notebook = "jupyter notebook"
lab = "jupyter lab"
process = "python scripts/process.py"
```

  * then run defined tasks

`pixi run lab`

* List installed packages

`pixi list`

* Update packages

`pixi update package_name`

* Other commands

`pixi export -f conda > environment.yml`

`pixi tree`

* Advantages for geospatial work

  * Excellent GDAL support (unlike uv)
    * Install using `pixi add gdal` and then use as `from osgeo import gdal`
  * Uses conda under the hood but with simpler syntax
  * Fast dependency resolution
  * Works well with complex C dependencies common in geospatial libraries



## uv

uv is an extremely fast Python package and project manager, written in Rust.designed to be a single tool to replace pip, pip-tools, pipx, poetry, pyenv, twine, virtualenv, and more. It provides comprehensive project management, with a universal lockfile, installs and manages Python versions and runs and installs tools published as Python packages on PyPI.

* Install uv if you don't have it already

`curl -LsSf https://astral.sh/uv/install.sh | sh`

See https://docs.astral.sh/uv/ for details

* Create a new virtual environment for your geospatial project

`uv venv .venv`
`source .venv/bin/activate` (Linux/macOS) or `.venv\Scripts\activate` (Windows)

* Create a requirements file (requirements.txt) for your dependencies e.g.
```
geopandas
rasterio
xarray
folium
shapely
pyproj
hvplot
pyogrio
```

* Install packages from requirements file

`uv install -r requirements.txt`

* Install a specific package

`uv install package_name`

* Running Jupyter notebooks

Install Jupyter: `uv  install notebook jupyter`

Or install and run JupyterLab: `uv run --with jupyter jupyter lab`


* Export dependencies

`uv pip freeze > requirements.txt`


* Generate lock file for reproducibility

`uv pip compile requirements.txt -o requirements.lock`

* Install from lock file

`uv pip install -r requirements.lock`

* Check for outdated packages

`uv pip list --outdated`

* Upgrade packages

uv pip install --upgrade package_name


* Performance advantages of uv
  * Much faster installation than pip (up to 10-100x)
  * Optimized dependency resolution
  * Excellent caching of package metadata

_Important Note_: GDAL doesn't work so well with uv

See https://github.com/astral-sh/uv/issues/11466

If your project directly requires GDAL, use pixi or mamba instead. 

`pyogrio` could be a replacement for direct use of GDAL (see https://github.com/geopandas/pyogrio)

## mamba

* Install mamba if you don't have it already

`curl -L -O "https://github.com/conda-forge/miniforge/releases/latest/download/Miniforge3-$(uname)-$(uname -m).sh"`

`bash Miniforge3-$(uname)-$(uname -m).sh`

* Create a new environment for your geospatial project

`mamba create -n geo_project`

`mamba activate geo_project`

* Configure conda-forge as the default channel

`mamba config --env --add channels conda-forge`

`mamba config --env --set channel_priority strict`


* Install key geospatial packages

`mamba install geopandas rasterio xarray netcdf4 folium shapely pyproj`


* Or set up an environment file (environment.yml) for reproducibility e.g. 
```
yamlname: geo_project
channels:
  - conda-forge
  - defaults
dependencies:
  - python=3.12
  - geopandas
  - rasterio
  - xarray
  - folium
  - shapely
  - rtree
  - pyproj
```

* Create environment from file

`mamba env create -f environment.yml`

* Update existing environment from file

`mamba env update -f environment.yml --prune`

* Export current environment to a file

`mamba env export > environment.yml`

* List installed packages

`mamba list`

* Search for available packages

`mamba search -c conda-forge <package_name>`


## Structure

