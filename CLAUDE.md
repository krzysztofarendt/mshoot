# CLAUDE.md — MShoot Development Guide

## Project Overview

MShoot is a Python package for Model Predictive Control (MPC) using the multiple shooting method. It supports physical models (via FMU/Functional Mock-up Interface) and data-driven models (via scikit-learn). Version 0.0.2dev, BSD 3-Clause licensed.

## Repository Structure

```
mshoot/                    # Main package
├── mshoot.py              # Core MShoot optimization class (multiple shooting solver)
├── mpc.py                 # MPCEmulation - receding horizon MPC loop
├── optimize.py            # NLP solver wrapper (scipy SLSQP)
├── __init__.py            # Exports: MShoot, SimModel, SimFMU, SimScikit, MPCEmulation
└── interfaces/
    ├── fmi.py             # SimFMU - FMU model interface (requires pyfmi)
    └── scikit.py          # SimScikit - scikit-learn model interface
test/                      # unittest-based test suite
├── test_mshoot.py         # Tests for MShoot optimization
├── test_fmi.py            # Tests for FMU interface
└── test_mpc.py            # Tests for MPC emulation
examples/                  # 28 example scripts demonstrating usage
resources/                 # Test data: FMU files, Modelica models, building data
docs-sphinx/               # Sphinx documentation source (RST)
docs/                      # Built HTML documentation
```

## Commands

### Run tests
```bash
python run_test.py
```
This runs the full unittest suite (TestMShoot, TestFMI, TestMPC). Test logs are written to `test.log`.

### Install package
```bash
pip install -e .
```

### Build documentation
```bash
cd docs-sphinx && make html
```

## Dependencies

numpy, pandas, scipy, matplotlib, scikit-learn, pyfmi

## Architecture & Key Patterns

- **SimModel** (abstract base class): All model interfaces must implement `simulate(udf, x0, **kwargs) -> (ydf, xdf)` where inputs/outputs are pandas DataFrames indexed by time in seconds.
- **MShoot**: Single-period optimization using multiple shooting with SLSQP solver.
- **MPCEmulation**: Receding horizon MPC loop wrapping MShoot. Uses two models: a control model (for optimization) and an emulation model (virtual plant).
- **SimFMU / SimScikit**: Concrete SimModel implementations for FMU and scikit-learn models.

## Code Conventions

- **No type hints** — the codebase predates modern typing conventions; do not add them.
- **Docstrings**: Sphinx-compatible format with `:param:` and `:return:` tags.
- **Data structures**: pandas DataFrames with equidistant time index (seconds) for all model I/O; numpy arrays for internal optimization.
- **Naming**: snake_case for functions/variables, PascalCase for classes.
- **Logging**: Use Python `logging` module (not print) for diagnostic output.
- **Testing**: unittest framework. Add new tests to the appropriate test file and register them in `run_test.py`'s `suite()` function.
- **New model interfaces**: Subclass `SimModel` and implement the `simulate()` method.

## Important Notes

- No CI/CD pipeline — tests are run manually.
- No linter or formatter configured — match existing code style.
- FMU tests require `pyfmi` and FMU files in `resources/fmus/`.
- Example scripts follow naming convention: `{mshoot|mpc}_{interface}_{variant}.py`.
