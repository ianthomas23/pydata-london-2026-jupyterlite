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

![centre](https://raw.githubusercontent.com/ianthomas23/pydata-london-2026-jupyterlite/main/images/jupyterlab.svg)

---

# Comparison of JupyterLab and JupyterLite 2

## JupyterLite

![centre](https://raw.githubusercontent.com/ianthomas23/pydata-london-2026-jupyterlite/main/images/jupyterlite.svg)

---

# Why JupyterLite vs JupyterLab?

- Static web site
    - Easy to deploy
    - Scales well
- Example of tutorial for the audience
- [Capytale project](https://thenewstack.io/teaching-a-billion-people-to-code-how-jupyterlite-is-scaling-the-impossible/)
    - 500,000 French high school students on single server

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

![centre height:500](https://raw.githubusercontent.com/ianthomas23/pydata-london-2026-jupyterlite/main/images/kernel.svg)

---

# Python kernel design 2

![centre](https://raw.githubusercontent.com/ianthomas23/pydata-london-2026-jupyterlite/main/images/pyodide-xeus.svg)

---

# Python kernel packaging

&nbsp; | pyodide | xeus
--- | ---- | ---- |
Packaging tool | `pip`-based | `conda`-based
Languages | Python only | All languages
`noarch` platform | PyPI | Conda-forge
`emscripten-wasm32` platform | pyodide now, PyPI soon | Emscripten-forge

* Same as choice of `pip` or `conda` environments for Python on real hardware

---

# How do I choose between the Python kernels?

- Mostly you can use either
- Switch between them easily using JupyterLite UI
- `pyodide`: usually rely on pyodide distribution for curated choice of packages
- `xeus-python`: usually choose packages and versions at deployment time
- Install new packages at runtime:
    - `pyodide` use `%pip install` from PyPi
    - `xeus-python` use `mamba install` from conda-forge or Emscripten-forge
        - Older versions use `%mamba` or `!mamba` instead
- Often different versions of packages available

<!--Demo switching kernels, and different versions of scipy available on try-jupyter-->

---

![float-right w:200 Emscripten-forge logo](https://raw.githubusercontent.com/emscripten-forge/recipes/main/docs/assets/icon_none_color.svg)

# Emscripten-forge packages

- xeus-based wasm packages are built on Emscripten-forge
    - Conda-forge does not support `emscripten-wasm32` platform
    - Emscripten-forge is conda-forge for WebAssembly
- Install such packages using `mamba` or `pixi`
    - `conda` does not support `emscripten-wasm32` plaform
- Recipes at https://github.com/emscripten-forge/recipes
- Served from `prefix.dev` channel https://prefix.dev/channels/emscripten-forge-4x

---

# Key features

- Static web site
    - Easy to deploy
    - Scales well
- Browser local storage
    - Shared between the different kernels
    - Changes only visible to you
    - When you return to the same site using the same browser you see your changes
    - Files that are served in the deployment can be modified and saved locally
        - If deleted, revert to the served files

---

# Other interesting demos

- Installing packages on the fly
    - `mamba` for xeus kernels, `pip` for pyodide kernel
- Compiled languages such as C++
    - Compiler toolchain is compiled to WebAssembly
- JupyterCAD
    - https://jupytercad.github.io/JupyterCAD/lab/index.html
- JupyterGIS
    - https://jupytergis.readthedocs.io/en/latest/lite/lab/

---

# What is JupyterLite good at? :heart:

- Trying things out
    - Without installing an environment/packages
- Education
    - Students start with copy of initial files and modify them locally
- Keeping data secure in the browser
    - Health data, finance

---

# What is JupyterLite bad at? :broken_heart:

- Does not support multithreading or multiprocessing
- Large datasets
    - Data stored in the browser
- Sharing modified files
- Kernels only support a single version of programming language at a time
- Remote access limited by [Cross-Origin Resource Sharing](https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/CORS) (CORS)

---

# Useful extensions: terminal

![float-right w:250 JupyterLite terminal logo](https://raw.githubusercontent.com/jupyterlite/terminal/main/docs/_static/terminal_logo.svg)

- JupyterLite extension for terminal that runs in the browser
- Connected to the `/drive` that is shared with kernels
- Commands compiled to WebAssembly using Emscripten-forge
- Example commands: `ls`, `echo`, `uname`, `vim`, `nano`, `git`
- Demo https://jupyter.org/try-jupyter/lab/ :rocket:
- Note `git clone` of remote repo is possible but requires a CORS proxy
    - See https://git2cpp.readthedocs.io/ for more details

---

# Useful extension: Jupyterlite AI

- Try it out at https://jupyterlite.github.io/ai/lab/index.html
- Set up a provider using API key
- [Video walkthrough](https://github.com/user-attachments/assets/e33d7d84-53ca-4835-a034-b6757476c98b) showing agent creating and running notebook
- Sandboxed
    - API key stored in your browser
    - Can only access browser storage for the session
    - Safe way to try out a provider without giving it access to your operating system, hard drive, and environment variables
- Project name change

---

# Useful extension: jupyterlite-sphinx

- Embed JupyterLite into your `sphinx` project documentation
- jupyterlite-sphinx
    - https://jupyterlite-sphinx.readthedocs.io/
- ipyleaflet
    - https://ipyleaflet.readthedocs.io/
- numpy
    - https://numpy.org/
- sympy
    - https://live.sympy.org/

---

# Create a JupyterLite deployment 1

- Deployment is just a static web site
- Deploy locally or to GitHub Pages, Read the Docs, Vercel, etc
- Official docs
    - https://jupyterlite.readthedocs.io/en/stable/howto/index.html
- Example deployments
    - In many https://github.com/jupyterlite repos
    - https://github.com/jupyter/try-jupyter

---

# Create a JupyterLite deployment 2

- Questions
    - What packages (kernels, terminal, extensions) do you want?
    - What pre-installed xeus kernel packages do you want?
    - What content (notebooks, data files) do you want?
- Process
    - Build the static site using `jupyter lite build`
    - Serve it

---

# Local deployment with both Python kernels

<div class="columns">
<div>

`build-environment.yml`:
```yml
name: build-env1
channels:
  - conda-forge
dependencies:
  - jupyterlite-core
  - jupyterlite-pyodide-kernel
  - jupyterlite-xeus
```

</div>
<div>

`environment.yml`:
```yml
name: temp
channels:
  - https://prefix.dev/emscripten-forge-4x
  - conda-forge
dependencies:
  - xeus-python
  - matplotlib
```

</div>
</div>

Sequence of commands:

```bash
micromamba create -f build-environment.yml
micromamba activate build-env1
jupyter lite build
python -m http.server -d _output/
```

---

# Deployment extras 1

- Content (notebooks, data files)
    - Put in a separate directory e.g. `contents`
    - Append `--contents contents/` to `jupyter lite build`
    - `jupyter_server` must be in `build-environment.yml`

- Xeus kernels
    - Add to `environment.yml`
    - `jupyterlite-xeus` must be in `build-environment.yml`

- Non-xeus kernels such as `pyodide` and `p5`
    - Add to `build-environment.yml`

---

# Deployment extras 2

- Preinstall xeus kernel packages
    - Add to `environment.yml`

- Extensions such as `jupyterlite-ai`
    - Add to `build-environment.yml`

- Terminal extension
    - Add `jupyterlite-terminal` and `nodejs` to `build-environment.yml`
    - Add `jupyter-lite.json` file to enable terminals
    - Optional `cockle-config-in.json` for extra commands, env vars, etc

---

# More complicated local deployment

- Kernels = `pyodide`, `p5`, `xeus-cpp`, `xeus-python`
    - `p5` kernel not on `conda-forge` so install using `pip`
- Content = notebook, text file
- Extensions = terminal, catppuccin theme
- See `deploy2` directory of this talk's github repository
- Live demo :rocket:
- On GitHub Pages
    - https://ianthomas23.github.io/pydata-london-2026-jupyterlite/deploy2

---

# Deploy to github pages

- Demo repository https://github.com/jupyterlite/demo
- Docs https://jupyterlite.readthedocs.io/en/stable/quickstart/deploy.html
- Use the demo repo as a template to create your own repository
- Set up GitHub Actions and deploy
- Follow video and screenshots in the docs site

---

# Making it easier to deploy and share using notebook.link

![float-right w:250 Hero astronaut image](https://raw.githubusercontent.com/notebook-link/notebook.link//main/assets/hero-astro.svg)

https://notebook.link is a web platform built on top of JupyterLite

- Deployment is trivial, almost single-click
- Very fast to start a kernel
- Your files are stored in the cloud so that they can be used from multiple machines
- Sharing of notebooks with anyone via a link

---

# Where JupyterLite is going

- Make JupyterLite experience as close to JupyterLab as possible
- Combining with JupyterLab codebase
- Hybrid JupyterLab/JupyterLite supporting local (JupyterLite in browser) and remote (JupyterLab) kernels
- More kernel languages, including compiled languages
- Missing Emscripten-forge packages

---

# Summary/conclusions
