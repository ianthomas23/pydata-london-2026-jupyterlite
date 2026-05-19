---
marp: true
theme: default
paginate: true
style: |
  .columns {
    display: grid;
    grid-template-columns: repeat(2, minmax(0, 1fr));
    gap: 1rem;
  }
  .centre {
    text-align: center;
  }
  a {
    color: royalblue;
  }
---

<style>
section::after {
  content: attr(data-marpit-pagination) '/' attr(data-marpit-pagination-total);
}
img[alt~="centre"] {
  display: block;
  margin: 0 auto;
}
img[alt~="float-left"] {
  float: left;
}
img[alt~="float-right"] {
  float: right;
}
</style>

![float-right w:200 JupyterLite logo](https://raw.githubusercontent.com/jupyterlite/jupyterlite/main/docs/_static/icon.svg)

# JupyterLite: run all your code in a web browser using WebAssembly

<hr>

![float-right height:300 qr code for slides](https://raw.githubusercontent.com/ianthomas23/pydata-london-2026-jupyterlite/main/images/qr.png)

## Ian Thomas, QuantStack

Github repository:
https://github.com/ianthomas23/pydata-london-2026-jupyterlite

---

# About me

<div class="columns">
<div>

## Ian Thomas

Open Source developer
Caerphilly

<div>
<img height="70" alt="Matplotlib logo" src="https://raw.githubusercontent.com/ianthomas23/pydata-london-2026-jupyterlite/main/images/matplotlib-logo.svg">
<img height="70" alt="Bokeh logo" src="https://raw.githubusercontent.com/bokeh/pm/main/assets/logos/SVG/bokeh-icon.svg">
<img height="70" alt="ContourPy logo" src="https://raw.githubusercontent.com/contourpy/branding/main/logo/output/contourpy_logo.svg">
<img height="70" alt="Datashader logo" src="https://raw.githubusercontent.com/holoviz/datashader/main/doc/_static/datashader-logo.png">
<img height="70" alt="Jupyter logo" src="https://raw.githubusercontent.com/jupyterlab/jupyterlab/main/docs/source/_static/jupyter_logo.svg">
<img height="70" alt="Emscripten-forge logo" src="https://raw.githubusercontent.com/emscripten-forge/recipes/main/docs/assets/icon_none_color.svg">
</div>

</div>
<div>

## QuantStack

Jupyter, Mamba, Arrow
France, Austria, Germany, India, Spain, UK

![h:70 QuantStack logo](https://raw.githubusercontent.com/ianthomas23/jupytercon2025-subshells/main/images/QuantStack-logo-website.svg)

</div>
</div>

---

![float-right w:250 JupyterLite logo](https://raw.githubusercontent.com/jupyterlite/jupyterlite/main/docs/_static/icon.svg)

# What is JupyterLite?

- JupyterLite is a JupyterLab distribution that runs entirely in the web browser, backed by in-browser language kernels
- Like JupyterLab but **everything** runs in your web browser

---

# Comparison of JupyterLab and JupyterLite 1

## JupyterLab

diagram 1

---

# Comparison of JupyterLab and JupyterLite 2

## JupyterLite

diagram 2

---

# Comparison of JupyterLab and JupyterLite 3

Why or Benefits

- much easier to deploy and scale

example of tutorial for the audience......

vs Binder

Capytale link ?

---

# Live demonstration of basic functionality

https://jupyter.org/

lorenz example uses widgets and mpl

    - try jupyter is actually lite not lab!
    - list all the different kernels available
    - run `sys.platform` etc, and some file access to show shared filesystem with file browser
    - some visualisations e.g. matplotlib, ipywidgets

---

# How it works 1

- Frontend (UI in browser) almost the same as JupyterLab
- Jupyter server also runs in the browser, is JavaScript instead of Python
    - Create and delete kernels
    - Communication to and from kernels via Jupyter messaging protocol
- Kernels also run in the browser
    - Each in its own thread (Web Worker)
    - Python kernel is CPython compiled to WebAssembly using Emscripten


---

# How it works 2: what is Python?

- Python is an executable that runs on your Operating System
- CPython is C source code compiled into an executable
    - e.g. using the `clang` toolchain to compile for `macOS ARM`
- Pass it Python code (human-readable text) that it parses, interprets and executes
- Use other Python packages:
    - Pure Python packages (`noarch`) are also text
    - Packages containing compiled code (e.g. `numpy`) must be pre-compiled to your architecture

---

# How it works 3: Python in the browser

- WebAssembly (`wasm`) is a executable format that runs in the browser
    - This is our architecture/platform
- Emscripten is a toolchain used to compile to WebAssembly
- We use the Emscripten toolchain to compile CPython to WebAssembly
- Pass it Python code as usual
    - Pure Python packages (`noarch`) can be used unmodified
    - Packages containing compiled code must be pre-compiled to WebAssembly
- Packages for kernels are downloaded and cached in your browser

---

# JupyterLite kernels

<div class="columns">
<div>

- `pyodide` = Python
- `xeus-python`
- `xeus-r`
- `xeus-cpp`
- `xeus-octave`
- `xeus-sqlite`

</div>
<div>

- `p5` = JavaScript
- `xeus-haskell`
- `xeus-lua`
- `xeus-ocaml`
- `xeus-shakespearelang`

</div>
</div>

More in progress ...

---

# There are two Python kernels? :anguished:

- `pyodide`
    - Used outside of JupyterLite in web apps and web pages, including in PyScript
    - Wrapped to understand Jupyter messaging protocol as `pyodide-kernel`

- `xeus-python`
    - One of the xeus family of kernels, for use in JupyterLite only

- Developed in parallel
- Differ in how they are designed, and how they are packaged

---

# Python kernel design 1

![centre height:500](../images/kernel.svg)

---

# Python kernel design 2

![centre](../images/pyodide-xeus.svg)

---

# Python kernel packaging

- pyodide
    - `pip`-based packaging
    - For Python only
    - Packages served from pyodide-specific site, switching to PyPI
- xeus-python (and other xeus kernels)
    - `conda`-based packaging
    - For everything, not just Python
    - `noarch` packages from conda-forge
    - `emscripten-wasm32` packages from Emscripten-forge
- Same as choice of `pip` or `conda` environments for Python on real hardware

---

# How do I choose between the Python kernels?

- Mostly you can use either
- Switch between them easily using JupyterLite UI
- `pyodide`: usually rely on pyodide distribution for curated choice of packages
- `xeus-python`: usually choose packages and versions at deployment time
- Install new packages at runtime:
    - `pyodide` use `!pip install` from PyPi
    - `xeus-python` use `!mamba install` from conda-forge or Emscripten-forge
- Often different versions of packages available

<!--Demo switching kernels, and different versions of scipy available on try-jupyter-->

---

![float-right w:200 Emscripten-forge logo](https://raw.githubusercontent.com/emscripten-forge/recipes/main/docs/assets/icon_none_color.svg)

# Emscripten-forge packages

- xeus-based wasm packages are built on Emscripten-forge
    - conda-forge does not support `emscripten-wasm32` platform
    - Emscripten-forge is conda-forge for WebAssembly
- Install such packages using `mamba` or `pixi`
    - `conda` does not support `emscripten-wasm32` plaform
- Recipes at https://github.com/emscripten-forge/recipes
- Served from `prefix.dev` channel https://prefix.dev/channels/emscripten-forge-4x

---

- Key features such as shared in-browser filesystem

    - static website
    - filesystem shared between kernels, in-browser (by default)

---

- More detailed demos such as installing packages on the fly

    - installing packages on the fly (pip, mamba, etc)
    - C++ compiling on the fly, so the compiler toolchain `llvm` compiled to WebAssembly

    - maybe jupytergis demo ?

---

- What it is good and bad at

good
    - trying things out (without creating a venv, installing packages)
    - education - students start of with copies of files, modify them locally
    - keeping data secure - health data, finance
bad at
    - single version of python/whatever at a time
    - cannot do multithreading
    - large datasets

---

- Terminal for vim, git, etc

![float-right w:250 JupyterLite terminal logo](https://raw.githubusercontent.com/jupyterlite/terminal/main/docs/_static/terminal_logo.svg)

Logo

---

- Jupyterlite AI

show mistral ai key, demo code generation, explain fully sandboxed

---

- Use in project documentation using jupyterlite-sphinx

    - jupyterlite sphinx in project docs
    - repl similar
        - ? embed repl example (see https://jtp.io/jupytercon-2023-jupyterlite/files/index.html#14)
    - voici ?

---

- Deployment, including live demo

    - simple local deployment
        - show some of directory structure
    - more complicated local deployment
    - show where docs are, sections on configuring and so on.
    - github pages

---

# Making it easier to deploy and share using notebook.link

![float-right w:250 Hero astronaut image](https://raw.githubusercontent.com/notebook-link/notebook.link//main/assets/hero-astro.svg)

- deployment is trivial, almost single-click
- your files stored so that they can be used from multiple machines
- sharing of notebooks with anyone

https://notebook.link/

---

# Where JupyterLite is going

- Make JupyterLite experience as close to JupyterLab as possible
- Combining with JupyterLab codebase
- Hybrid JupyterLab/JupyterLite supporting local (JupyterLite in browser) and remote (JupyterLab) kernels
- More kernel languages, including compiled languages
- Missing Emscripten-forge packages

---

# Summary/conclusions
