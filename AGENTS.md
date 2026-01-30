# Agent Guidelines for pydicom-batch

## Build/Install/Run Commands

### Environment Setup
```bash
# Create conda environment
conda env create --file environment.yml
conda activate pydicom-batch

# Run extraction
python -m pydicombatch <config_file.yml>

# Or using conda run
conda run --no-capture-output --name pydicom-batch python -u -m pydicombatch <config_file.yml>
```

### Docker Setup
```bash
# Build Docker container
./bin/build-docker-container.sh

# Run extraction with Docker
./bin/run-docker-extraction.sh <config_file.yml>
```

### Testing
No test suite is currently configured. When adding tests:
- Use pytest (add to environment.yml if needed)
- Run single test: `pytest tests/test_module.py::test_function`
- Run all tests: `pytest`

### Linting/Formatting
No linting tools are configured. Recommended tools to add:
- `black` for code formatting
- `ruff` for fast linting
- `mypy` for type checking

## Code Style Guidelines

### Import Organization
Standard library imports first, then third-party imports, then local imports:
```python
import os
import sys
import time

from pydicom import dcmread
from pydicom.dataset import Dataset
from pynetdicom import AE

from pydicombatch.common import pydicombatch
from pydicombatch.scp import SCP
```

### Formatting and Spacing
- Use 4 spaces for indentation (no tabs)
- Use snake_case for variables and functions
- Use PascalCase for classes (SCP, SCU)
- Maximum line length: 100-120 characters
- Add blank line before functions and classes

### Type Hints
Type hints are not currently used. Consider adding them for better code clarity:
```python
def process_request(request: dict) -> None:
    pass
```

### Naming Conventions
- Classes: PascalCase (SCP, SCU)
- Functions: snake_case (create_ae, send_find)
- Variables: snake_case (config_file, temp_dir)
- Constants: UPPER_SNAKE_CASE (for module-level constants)
- Private methods: prefix with underscore (_internal_method)

### Error Handling
- Use try/except blocks for expected errors
- Catch specific exceptions when possible
- Provide informative error messages
- Use KeyboardInterrupt for graceful shutdown:
```python
try:
    process_request_batch(config)
except KeyboardInterrupt:
    print('\nExtraction stopped.')
    sys.exit(0)
```

### String Formatting
Prefer f-strings over .format() or % formatting:
```python
# Preferred
filename = os.path.join(path, f'{uuid}.dcm')
# Also acceptable
filename = os.path.join(path, '{}.dcm'.format(uuid))
```

### File I/O
- Use absolute paths from configuration (not relative paths)
- Use `os.path.join` for cross-platform path handling
- Use `with open()` context managers for file operations
- Use `os.makedirs(path, exist_ok=True)` for directory creation

### Concurrency
- Use threading module for concurrent operations
- Thread pool for parallel requests: `concurrent.futures.ThreadPoolExecutor`
- Queue for thread-safe communication: `queue.Queue`
- Set threads configuration via config['request']['threads']

### Configuration
- YAML files for configuration (using pyyaml with FullLoader)
- Dictionary access pattern: `config['section']['key']`
- CSV files for batch operations
- Check file existence before access: `os.path.exists()`

### Logging and Output
- Use print() for user-facing messages
- Use tqdm for progress bars on long operations
- Include descriptive messages for progress tracking

### DICOM-Specific Patterns
- Use pydicom for dataset manipulation
- Use pynetdicom for DICOM protocol operations
- Handle DICOM tags with ElementPath for flexible element access
- Support multiple transfer syntaxes
- Implement proper association handling with timeouts

### Documentation
- Use docstrings for classes and important methods
- Include parameter descriptions in docstrings
- Document return values
- Add inline comments for complex logic

### Dependencies
Main dependencies (managed via conda):
- pydicom (DICOM file handling)
- pynetdicom (DICOM protocol)
- pyyaml (configuration)
- tqdm (progress bars)
- pytz (timezone handling)
- inquirer (interactive prompts)
