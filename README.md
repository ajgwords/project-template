# project-template
A template repository for future projects.

This template provides a layout of directories useful for my coding related projects. The assumption is that Python will be the coding language and Jupyter notebooks will be heavily used for investigative work. 

## Initialisation

In GitHub fork this repo if you want to use it in your own account. Make sure that the repo is set to be a template (see https://docs.github.com/en/repositories/creating-and-managing-repositories/creating-a-template-repository). Then use that template to create new repositories (see https://docs.github.com/en/repositories/creating-and-managing-repositories/creating-a-repository-from-a-template), clone and use as usual. 

Make sure that you update the .gitignore file to work as you want it to.


# Geospatial Project

## Setup

This project uses pixi for Python environment management.

### Installation

1. Install dependencies:
   ```bash
   pixi install
   ```

2. Activate the environment:
   ```bash
   pixi shell
   ```

### Usage

- Start Jupyter Lab: `pixi run jupyter`
- Start Jupyter Notebook: `pixi run notebook`

## Project Structure

```
├── data/           # Data files (not in version control)
│   ├── raw/        # Original, unmodified data
│   ├── processed/  # Cleaned and processed data
│   └── outputs/    # Analysis outputs
├── src/            # Source code
│   ├── scripts/    # Standalone Python scripts
│   ├── notebooks/  # Jupyter notebooks
│   └── utils/      # Reusable utility functions
├── docs/           # Documentation (not in version control)
├── qgis-projects/  # QGIS project files (not in version control)
└── README.md       # This file
```

## Notes

- Data, documentation, and QGIS project folders are excluded from git
- Use `pixi install` to set up the environment
- All dependencies are managed through pixi.toml

## VSCode Integration

To use VSCode with Jupyter notebooks in the pixi environment:

1. Install the Python and Jupyter extensions in VSCode
2. Open VSCode in your project directory
3. Open the command palette (Ctrl+Shift+P) and select "Python: Select Interpreter"
4. Navigate to `.pixi/envs/default/bin/python` (this path appears after running `pixi install`)
5. Create or open a `.ipynb` file
6. VSCode will automatically use the pixi Python interpreter for notebooks

Alternatively, you can:
- Run `pixi shell` to activate the environment, then `code .` to open VSCode
- Use the integrated terminal in VSCode and run `pixi shell` there
- Just run `positron`

## Python Project Structure for Sequential Execution

To set up a main execution script that runs multiple code files in sequence:

### Example Structure:
```
src/
├── main.py           # Main execution script  
├── scripts/
│   ├── __init__.py   # Makes scripts a package
│   ├── step1_data_prep.py
│   ├── step2_analysis.py
│   └── step3_visualization.py
└── utils/
    ├── __init__.py
    └── helpers.py
```

### main.py Example:
```python
#!/usr/bin/env python3
"""
Main execution script for geospatial analysis pipeline
"""

import sys
from pathlib import Path

# Add src to path so we can import our modules
src_path = Path(__file__).parent
sys.path.append(str(src_path))

from scripts import step1_data_prep, step2_analysis, step3_visualization

def main():
    """Run the complete analysis pipeline"""
    print("Starting geospatial analysis pipeline...")
    
    try:
        print("Step 1: Data preparation...")
        step1_data_prep.run()
        
        print("Step 2: Analysis...")  
        step2_analysis.run()
        
        print("Step 3: Visualization...")
        step3_visualization.run()
        
        print("Pipeline completed successfully!")
        
    except Exception as e:
        print(f"Pipeline failed: {e}")
        sys.exit(1)

if __name__ == "__main__":
    main()
```

### Individual Script Example (step1_data_prep.py):
```python
"""Data preparation module"""

def run():
    """Main function for this step"""
    print("  Loading and cleaning data...")
    # Your data prep code here
    pass

def load_data():
    """Helper function"""
    pass

# Allow script to be run standalone for testing
if __name__ == "__main__":
    run()
```

### Execution:
- Run the full pipeline: `pixi run python src/main.py`
- Run individual steps: `pixi run python src/scripts/step1_data_prep.py`
- From activated shell: `python src/main.py`

## pixi

pixi is a package management tool leveraging the existing _conda_ ecosystem to obtain packages written in Python, C, C++, and many other languages. Like _uv_ it allows reproducibility through dedicated, isolated environments that can be easily recreated. It ensures compatibility across Linux, macOS, Windows, and more. Recommended for geospatial work due to the packages in conda-forge.

It is useful for compatability across users to version control both the pixi.lock and pixi.toml files.


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

* Running Jupyter
```
pixi add jupyterlab
pixi run jupyter lab
```

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
  * Works with Jupyter and Positron as well as others (just choose the correct kernel)
    * On a Debian test system VS Code found the Pixi kernel automatically. On Fedora Sway it was unable to. Using `pixi add ipykernel` followed by `python -m ipykernel install --user --name=<your-env-name> --display-name "Pixi (<your-env-name>)"` the issue was fixed. The kernel was saved to `Installed kernelspec <your-env-name> in /home/al/.local/share/jupyter/kernels/<your-env-name>`. 
