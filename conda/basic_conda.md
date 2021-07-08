<a href="https://sharma-bharat.github.io/" style="float: right;
">*Homepage*</a> \

To install anaconda use this [link](https://docs.anaconda.com/anaconda/install/index.html)

Cheat-sheet of commands: [link](conda-cheatsheet.pdf)

My latest conda environment yaml file: [link](carbon.yml)
`conda env create -f carbon.yml `

## Basic Commands

Create environment

```
conda create -n myenv
conda create -n myenv python=3.6  \# with a specific python version
conda create -n myenv scipy       \# with a specific python package
conda env create -f myenv.yml   \# from a .yml file
```

List environments
```conda env list```

Activate environment
```conda activate myenv```

Deactivate environment
```conda deactivate```

View a list of the packages in an environment
```
conda list
conda list -n myenv
```

Export a .yml environment file
```
conda env export > myenv.yml
```

Remove an environment
```conda env remove --name myenv```

Check conda version
```conda info```

Update conda
``` conda update conda```

Update a package
```conda upgrade xarray```