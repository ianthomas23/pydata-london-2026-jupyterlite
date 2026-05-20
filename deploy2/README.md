# More complicated deployment with content, terminal, jupyterlite-ai, etc

Assumes have `micromamba` installed.

```bash
micromamba create -f build-environment.yml
micromamba activate build-env2
jupyter lite build --contents contents
```

Outputs to `_output` directory.

To serve locally:

```bash
python -m http.server -d _output/
```

and open browser at http://localhost:8000
