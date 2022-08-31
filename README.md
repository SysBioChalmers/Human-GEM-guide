# Human-GEM-guide

Guide for Human-GEM - [View the guide here](https://sysbiochalmers.github.io/Human-GEM-guide).

### Note: This is the repository hosting the Human-GEM **guide**. The Human-GEM **model** repository can be found [here](https://github.com/SysBioChalmers/Human-GEM).

### Editing the guide

**Do not directly edit any files in the top-level `docs/` directory. Such edits will be overwritten!**  
Changes should ONLY be made to files in the `mkdocs/` directory. A GitHub action is set up to automatically regenerate all documents in the top-level `docs/` directory every time a commit is made to the `main` branch.

### Preview changes

To preview changes to the guide on your local machine before pushing commits to `main`, you will need to install [MkDocs](https://www.mkdocs.org/) using pip:

```
pip install mkdocs mkdocs-material
```

Preview your local version of the guide by running the `serve` command from within the `mkdocs/` directory:

```
cd mkdocs/
mkdocs serve
```

Changes made to files in the `mkdocs/` directory will automatically be updated in the local preview.
