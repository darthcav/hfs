# pysof

Python wrapper for the Helios SOF (SQL on FHIR) toolkit.

This is an initial scaffolding for the Python package. In a future iteration we will expose the Rust `sof` crate APIs to Python (likely via PyO3 + maturin). For now, this package provides a project layout and tooling configured for Python 3.11 with uv.

## Requirements
- Python 3.11
- uv (package and environment manager)

## Quickstart

```bash
# From repo root
cd crates/pysof

# Ensure Python 3.11 is available and create a venv
uv venv --python 3.11

# Install the project (no dependencies yet)
uv sync

# Sanity check the package import
uv run python -c "import pysof; print(pysof.__version__)"
```

## Project layout

```
crates/pysof/
├─ pyproject.toml          # PEP 621 metadata, Python >=3.11, uv-compatible
├─ README.md
└─ src/
   └─ pysof/
      └─ __init__.py       # package root
```

## Roadmap / TODO

- v0 (MVP: packaging and docs)
  - [x] Package skeleton with src layout
  - [x] `pyproject.toml` targeting Python 3.11
  - [x] README with quickstart, layout, roadmap
  - [ ] Define Python public API surface in `pysof/__init__.py`
  - [ ] Add `ruff` (lint) and `mypy` (types) configs
  - [ ] Add `uv` scripts for fmt, lint, test
  - [ ] Add minimal tests: import/package metadata/version
  - [ ] Add CI workflow for Python 3.11: lint + tests

- v1 (Rust bindings and wheels)
  - [ ] Introduce PyO3 bindings to call Rust `sof` library
  - [ ] Switch build backend to `maturin` (PEP 621) for extension builds
  - [ ] Expose `run_view_definition(view: dict, bundle: dict, format: str) -> bytes`
  - [ ] Map content types: `csv`, `csv_with_header`, `json`, `ndjson`
  - [ ] Map errors to Python exceptions (`SofError` hierarchy)
  - [ ] Add wheel builds for Windows, macOS (x86_64, arm64), Linux (manylinux/musllinux)
  - [ ] Add integration tests mirroring Rust crate examples
  - [ ] Provide examples: in-memory, file-based, stdin/stdout-like workflows

- Future
  - [ ] Enable Parquet support when available in `sof`
  - [ ] Optional async client wrappers for `sof-server` HTTP endpoints
  - [ ] Prebuilt wheels for additional Python versions (3.12+) as demand dictates

## License
This package inherits the license from the repository root.
