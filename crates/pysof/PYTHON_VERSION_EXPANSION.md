# Python Version Support Expansion for pysof

## Summary

Expanded Python version support from **Python 3.11 only** to **Python 3.8 - 3.13**.

## Changes Made

### 1. pyproject.toml

#### requires-python
```toml
# Before
requires-python = ">=3.11,<3.12"

# After
requires-python = ">=3.8"
```

#### Classifiers
Added classifiers for all supported Python versions:
```toml
classifiers = [
  "Programming Language :: Python :: 3",
  "Programming Language :: Python :: 3.8",   # NEW
  "Programming Language :: Python :: 3.9",   # NEW
  "Programming Language :: Python :: 3.10",  # NEW
  "Programming Language :: Python :: 3.11",
  "Programming Language :: Python :: 3.12",  # NEW
  "Programming Language :: Python :: 3.13",  # NEW
  # ... other classifiers
]
```

#### Tool Configurations
```toml
# Ruff target version
[tool.ruff]
target-version = "py38"  # Changed from "py311"

# MyPy Python version
[tool.mypy]
python_version = "3.8"  # Changed from "3.11"
```

### 2. README.md

Updated all references to Python version requirements:

#### Installation Section
```markdown
**Supported Platforms:**
- **Python**: 3.8, 3.9, 3.10, 3.11, 3.12, 3.13
```

#### Development Requirements
```markdown
- Python 3.8 or later (3.8, 3.9, 3.10, 3.11, 3.12, 3.13 supported)
```

#### Build Instructions
```bash
# Create a venv with your preferred Python version (3.8+)
uv venv --python 3.11  # or 3.8, 3.9, 3.10, 3.12, 3.13
```

### 3. PYPI_CHECKLIST.md

Updated validation checklist:
```markdown
- [ ] Python version requirement correct: `requires-python = ">=3.8"`
- [ ] Supported platforms listed correctly (Python 3.8-3.13)
```

### 4. TESTING_PYPI.md

Added Python version support to the improvements list:
- ✅ Support Python 3.8, 3.9, 3.10, 3.11, 3.12, 3.13

## Rationale

### Why Python 3.8+?

1. **PyO3 Compatibility**: PyO3 0.24 (used by pysof) supports Python 3.8 - 3.13
2. **Broader Adoption**: Supports users on older Python versions
3. **Industry Standard**: Python 3.8+ covers most production environments
4. **LTS Support**: Python 3.8 reached EOL in Oct 2024, but still widely used

### Version Support Timeline

| Python Version | Release Date | EOL Date | Status |
|----------------|--------------|----------|---------|
| 3.8 | Oct 2019 | Oct 2024 | EOL (still widely used) |
| 3.9 | Oct 2020 | Oct 2025 | Active |
| 3.10 | Oct 2021 | Oct 2026 | Active |
| 3.11 | Oct 2022 | Oct 2027 | Active |
| 3.12 | Oct 2023 | Oct 2028 | Active |
| 3.13 | Oct 2024 | Oct 2029 | Active |

## Testing Requirements

Before releasing with expanded Python support, wheels should be built and tested for:

### Minimum Testing
- [ ] Python 3.8 (oldest supported)
- [ ] Python 3.11 (previously only supported version)
- [ ] Python 3.13 (newest supported)

### Comprehensive Testing (Recommended)
- [ ] Python 3.8
- [ ] Python 3.9
- [ ] Python 3.10
- [ ] Python 3.11
- [ ] Python 3.12
- [ ] Python 3.13

### Test Matrix

For each Python version, test on:
- [ ] Linux x86_64 (glibc)
- [ ] Windows x86_64
- [ ] macOS AArch64

### What to Test

For each Python version:

1. **Installation**
   ```bash
   pip install pysof
   ```

2. **Import**
   ```python
   import pysof
   print(pysof.__version__)
   ```

3. **Basic Functionality**
   ```python
   import pysof
   
   view = {
       "resourceType": "ViewDefinition",
       "id": "test",
       "name": "Test",
       "status": "active",
       "resource": "Patient",
       "select": [{"column": [{"name": "id", "path": "id"}]}]
   }
   
   bundle = {
       "resourceType": "Bundle",
       "type": "collection",
       "entry": [{
           "resource": {"resourceType": "Patient", "id": "test-123"}
       }]
   }
   
   result = pysof.run_view_definition(view, bundle, "json")
   assert len(result) > 0
   print("✅ Basic functionality works")
   ```

4. **All Output Formats**
   ```python
   for format in ["csv", "json", "ndjson", "parquet"]:
       result = pysof.run_view_definition(view, bundle, format)
       assert len(result) > 0
       print(f"✅ {format} format works")
   ```

## CI/CD Considerations

### GitHub Actions Matrix

Consider updating `.github/workflows/ci.yml` to build wheels for multiple Python versions:

```yaml
strategy:
  matrix:
    python-version: ['3.8', '3.9', '3.10', '3.11', '3.12', '3.13']
    os: [ubuntu-latest, windows-latest, macos-latest]
```

### maturin Build

maturin automatically builds wheels for all available Python versions when using:

```bash
# Builds for all Python versions found on the system
maturin build --release --out dist

# Or explicitly specify versions
maturin build --release --out dist --interpreter python3.8 python3.9 python3.10 python3.11 python3.12 python3.13
```

## Compatibility Notes

### Language Features

Since we're targeting Python 3.8+, avoid using features from Python 3.9+:

**Avoid (Python 3.9+):**
- `dict` union operator (`|`)
- Type hint generics without `from __future__ import annotations`
- `str.removeprefix()` / `str.removesuffix()`

**Safe for Python 3.8+:**
- Type hints with `typing` module
- f-strings
- Walrus operator (`:=`)
- Positional-only parameters (`/`)

### PyO3 Notes

PyO3 0.24 handles Python version compatibility automatically. The Rust code doesn't need changes - it will work across all supported Python versions.

### Dependencies

All current dependencies support Python 3.8+:
- `pyo3 >= 0.24.1` - supports Python 3.8-3.13 ✅
- `helios-sof` - Rust library, no Python dependencies ✅
- `helios-fhir` - Rust library, no Python dependencies ✅

## Breaking Changes

**None**. This change is fully backwards compatible:
- Python 3.11 users can continue using pysof without any changes
- Users on Python 3.8-3.10 and 3.12-3.13 can now use pysof
- No API changes
- No behavior changes

## Migration Guide

### For Package Users

**No action required**. If you're already using pysof with Python 3.11, nothing changes.

**New capability**: You can now use pysof with Python 3.8, 3.9, 3.10, 3.12, or 3.13:

```bash
# With Python 3.8
python3.8 -m pip install pysof

# With Python 3.12
python3.12 -m pip install pysof
```

### For Package Maintainers

1. **Update pyproject.toml** ✅ (Done)
2. **Update README** ✅ (Done)
3. **Update checklists** ✅ (Done)
4. **Build wheels for all versions**
5. **Test on multiple Python versions**
6. **Update CI/CD** (if applicable)
7. **Release to PyPI**

## Verification Checklist

Before releasing:

- [ ] `pyproject.toml` has `requires-python = ">=3.8"`
- [ ] All Python version classifiers added (3.8-3.13)
- [ ] README updated with version support
- [ ] Tool configurations updated (ruff, mypy)
- [ ] Checklists updated (PYPI_CHECKLIST.md, TESTING_PYPI.md)
- [ ] Wheels built for all Python versions
- [ ] Installation tested on at least Python 3.8, 3.11, 3.13
- [ ] Basic functionality tested on multiple versions
- [ ] PyPI package metadata will show correct Python versions

## Future Considerations

### Python 3.14+

When Python 3.14 is released:
1. Verify PyO3 supports Python 3.14
2. Add `"Programming Language :: Python :: 3.14"` classifier
3. Test with Python 3.14
4. Update documentation

### Dropping Python 3.8

When Python 3.8 usage drops significantly:
1. Update `requires-python = ">=3.9"`
2. Remove Python 3.8 classifier
3. Update documentation
4. Consider using Python 3.9+ features

## References

- [PyO3 User Guide - Python Version Support](https://pyo3.rs/latest/python-version-support.html)
- [Python Release Cycle](https://devguide.python.org/versions/)
- [PEP 440 - Version Identification](https://peps.python.org/pep-0440/)
- [Python Version Status](https://endoflife.date/python)

---

**Date**: 2025-11-02  
**Author**: Doug Rojas  
**Status**: Ready for testing and release
