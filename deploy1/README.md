# Simple deployment with both Python kernels

Assumes have `micromamba` installed.

```bash
micromamba create -f build-environment.yml
micromamba activate build-env1
jupyter lite build
```

Outputs to `_output` directory.

To serve locally:

```bash
python -m http.server -d _output/
```

and open browser at http://localhost:8000
