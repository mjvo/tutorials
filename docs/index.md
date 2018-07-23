---
title:  Using MkDocs for Wired Documentation
description:    This document outlines how to setup and use MkDocs for Wired Documentation.
---

As an alternative to PDFs and Google Docs, this is an exploration of MkDocs for hosting Wired documentation on Github.  

For full documentation of the platform, visit [mkdocs.org](http://mkdocs.org).

## Installation

MkDocs is a Python package and requires Python 2.7, 3.3, 3.4, 3.5 or pypy. 

### Installing MkDocs

Full installation documentation is available here: [https://www.mkdocs.org/#installation](https://www.mkdocs.org/#installation)

If you have Python installed, simply run the following from the command line:

```
pip install mkdocs
```

For OS X, MkDocs is also available as a [HomeBrew](https://brew.sh/) installation package:

```
brew install mkdocs 
```

For Windows, MkDocs can be installed using the [Chocolatey](https://chocolatey.org/) package manager:

```
choco install mkdocs 
```


### Installating Material for MkDocs theme

The proposed new documentation platform uses the [Material for MkDocs theme](https://squidfunk.github.io/mkdocs-material/).

Full installation instructions:  [https://squidfunk.github.io/mkdocs-material/getting-started/#installation](https://squidfunk.github.io/mkdocs-material/getting-started/#installation)

If you have Python installed, simply run the following from the command line:

```
pip install mkdocs-material
```

Windows users can use Chocolatey to install the Material theme:

```
choco install mkdocs-material
```

!!! note
    Unfortunately, as of this writing there is **no** Homebrew package available for the Material theme but you can [clone it from Github](https://squidfunk.github.io/mkdocs-material/getting-started/#cloning-from-github).

### Using
## Commands

* `mkdocs new [dir-name]` - Create a new project.
* `mkdocs serve` - Start the live-reloading docs server.
* `mkdocs build` - Build the documentation site.
* `mkdocs help` - Print this help message.

## Project layout

    mkdocs.yml    # The configuration file.
    docs/
        index.md  # The documentation homepage.
        ...       # Other markdown pages, images and other files.

## Some Sample Markdown

### Test Checklist

* [x] Lorem ipsum dolor sit amet, consectetur adipiscing elit
* [x] Nulla lobortis egestas semper
* [x] Curabitur elit nibh, euismod et ullamcorper at, iaculis feugiat est
* [ ] Vestibulum convallis sit amet nisi a tincidunt
    * [x] In hac habitasse platea dictumst
    * [x] In scelerisque nibh non dolor mollis congue sed et metus
    * [x] Sed egestas felis quis elit dapibus, ac aliquet turpis mattis
    * [ ] Praesent sed risus massa
* [ ] Aenean pretium efficitur erat, donec pharetra, ligula non scelerisque
* [ ] Nulla vel eros venenatis, imperdiet enim id, faucibus nisi

