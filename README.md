# Human-GEM-guide
Guide for Human-GEM - [View the guide here](https://sysbiochalmers.github.io/Human-GEM-guide).

### Note: This is the repository hosting the Human-GEM **guide**. The Human-GEM **model** repository can be found [here](https://github.com/SysBioChalmers/Human-GEM).


### Required software
This user guide is built using [MkDocs](https://www.mkdocs.org/). Install MkDocs using pip:
```
pip install mkdocs
```

### Editing the guide
**Do not directly edit any files in the top-level `docs/` directory. Such edits will be overwritten!**

To preview changes to the guide before re-building the site, run the serve command from within the `mkdocs/` directory:
```
mkdocs serve
```

Once you are satisfied with the changes, the site pages need to be re-built. Run the following command **from within the `mkdocs/` directory** to build the site into the top-level `docs/` directory of the repository:
```
mkdocs build -d ../docs
```

Push the changes to the master branch and the site content will be updated shortly thereafter.

