# Anomalies Package - GitHub Copilot Instructions

**CRITICAL**: Always reference these instructions first and fallback to search or bash commands only when you encounter unexpected information that does not match the info here.

## Overview
The anomalies package is a Python library that implements the anomaly-free solution from [arXiv:1905.13729](https://arxiv.org/abs/1905.13729). It generates numpy arrays of N integers satisfying Diophantine equations for anomaly cancellation in theoretical physics.

## Working Effectively

### Bootstrap and Install Dependencies
Always run these commands in sequence to set up the development environment:

```bash
python -m pip install --upgrade pip
pip install flake8 pytest
pip install numpy
```
- **Timing**: Dependency installation takes 3-6 seconds. NEVER CANCEL.
- **Note**: numpy is required for the package to function. Install it before testing functionality.
- **Network Issues**: If `pip install --upgrade pip` fails with timeout, skip it and proceed with other installations.

### Install the Package
Choose one of these methods:

**Method 1: Development Mode (Recommended for development)**
```bash
python setup.py develop --user
```
- **Timing**: <1 second. NEVER CANCEL.
- **Use**: For active development when you need changes to be immediately available.

**Method 2: Standard Installation**
```bash
pip install .
```
- **Timing**: ~1 second. NEVER CANCEL.
- **Use**: For testing the package as an end user would install it.

### Build and Test
Always run these validation steps after making changes:

**Linting (Required before CI)**
```bash
# Strict syntax checking
flake8 . --count --ignore=C901 --select=E9,F63,F7,F82 --show-source --statistics
# Full linting with warnings
flake8 . --count --ignore=C901 --exit-zero --max-complexity=10 --max-line-length=127 --statistics
```
- **Timing**: <1 second each. NEVER CANCEL.
- **CRITICAL**: Always run both commands before committing or the CI will fail.

**Testing**
```bash
pytest -v
```
- **Timing**: <1 second. NEVER CANCEL.
- **Expected**: 1 test should pass (`TestColav_anomaly::test__working`).

### Build Distributable Package
```bash
python setup.py sdist bdist_wheel
```
- **Timing**: <1 second. NEVER CANCEL.
- **Output**: Creates `dist/anomalies-X.X.X.tar.gz` and `dist/anomalies-X.X.X-py3-none-any.whl`

## Validation Scenarios

### CRITICAL: Always Test Package Functionality
After making any changes to the anomalies package, always run this validation:

```python
from anomalies import anomaly

# Test basic functionality
result = anomaly.free([-1,1],[4,-2])
print('Result:', list(result))  # Should be [3, 3, 3, -12, -12, 15] (may show np.int64 wrapper)
print('GCD:', anomaly.free.gcd)  # Should be 3
print('Simplified:', list(anomaly.free.simplified))  # Should be [1, 1, 1, -4, -4, 5] (may show np.int64 wrapper)

# Verify mathematical properties
print('Sum check:', result.sum())  # Should be 0
print('Cube sum check:', (result**3).sum())  # Should be 0
```

### Interactive CLI Testing
Test the command-line interface:
```bash
python -m anomalies.anomaly
# Enter: [-1,1]
# Enter: [4,-2]
# Expected output: [ 1  1  1 -4 -4  5]
```

### Additional Test Cases
```python
# Test different parameter sets
result2 = anomaly.free([2,-3],[1,5])
print('Sum check:', result2.sum())  # Should be 0
print('Cube sum check:', (result2**3).sum())  # Should be 0
```

## Repository Structure

### Key Files and Directories
```
/home/runner/work/anomalies/anomalies/
├── anomalies/              # Main package directory
│   ├── __init__.py        # Package initialization
│   └── anomaly.py         # Core algorithm implementation
├── tests/                  # Test directory  
│   └── test_anomalies.py  # Unit tests
├── .github/workflows/      # CI/CD configuration
│   ├── python-package.yml # Main CI workflow
│   └── python-publish.yml # PyPI publishing workflow
├── setup.py               # Package configuration and dependencies
├── README.md              # Documentation and usage examples
└── LICENSE                # BSD license
```

### Important Implementation Details
- **Core function**: `anomaly.free(l, k)` - returns numpy array solving Diophantine equations
- **Dependencies**: numpy with version constraints (1.16.5+ for Python 3.8+)  
- **Attributes**: After calling `anomaly.free()`, access `.gcd` and `.simplified` attributes
- **Input constraints**: Lists `l` and `k` must have compatible dimensions per mathematical requirements

## Common Issues and Solutions

### Dependency Problems
- **Error**: `ModuleNotFoundError: No module named 'numpy'`
- **Solution**: Run `pip install numpy` before testing functionality

### Build Warnings
- **Warning**: "setup.py install is deprecated"  
- **Status**: Expected deprecation warnings, build still works correctly
- **Action**: Continue with current setup.py approach as it's functional

### Test Environment
- **Python Version**: Tested on Python 3.12.3
- **OS**: Linux (Ubuntu latest in CI)
- **Package Manager**: pip (latest version)

## CI Pipeline Details
The GitHub Actions workflow (`.github/workflows/python-package.yml`) runs:
1. Matrix testing on Python 3.7, 3.8, 3.9
2. Dependency installation via pip
3. flake8 linting (both strict and full checks)
4. pytest test execution
5. Package development installation

**NEVER CANCEL**: All CI steps complete in under 30 seconds total.

## Timing Expectations
- **Total setup time**: 10-15 seconds for full environment bootstrap
- **Linting**: <1 second per flake8 command
- **Testing**: <1 second for pytest
- **Package installation**: <1 second for both methods
- **Building**: <1 second for sdist/bdist_wheel
- **Functionality validation**: <1 second

## Development Workflow
1. Make code changes
2. Run linting: `flake8 . --count --ignore=C901 --select=E9,F63,F7,F82 --show-source --statistics`
3. Run full linting: `flake8 . --count --ignore=C901 --exit-zero --max-complexity=10 --max-line-length=127 --statistics`
4. Run tests: `pytest -v`
5. Validate functionality with test cases shown above
6. Build package (if needed): `python setup.py sdist bdist_wheel`

Always ensure all steps pass before committing changes.