# Contributing to AI Forge

We love your input! We want to make contributing to AI Forge as easy and transparent as possible, whether it's:

- Reporting a bug
- Discussing the current state of the code
- Submitting a fix
- Proposing new features
- Becoming a maintainer

## Development Process

We use GitHub to host code, to track issues and feature requests, as well as accept pull requests.

### Getting Started

1. **Fork the repository** on GitHub
2. **Clone your fork** locally:
   ```bash
   git clone https://github.com/YOUR_USERNAME/ai-forge.git
   cd ai-forge
   ```

3. **Create a virtual environment**:
   ```bash
   python -m venv env
   source env/bin/activate  # On Windows: env\Scripts\activate
   ```

4. **Install development dependencies**:
   ```bash
   pip install -e ".[dev]"
   pre-commit install
   ```

5. **Create a feature branch**:
   ```bash
   git checkout -b feature/your-feature-name
   ```

### Development Workflow

#### Running Tests

```bash
# Run all tests
pytest

# Run specific test file
pytest tests/test_training.py -v

# Run tests with coverage
pytest --cov=soup_cli --cov-report=html

# Run only unit tests (fast)
pytest -m unit

# Run integration tests
pytest -m integration
```

#### Code Quality

```bash
# Format code with ruff
ruff format src/ tests/

# Lint code with ruff
ruff check src/ tests/ --fix

# Type checking with mypy
mypy src/

# Run pre-commit hooks on all files
pre-commit run --all-files
```

#### Before Committing

```bash
# Make sure all tests pass
pytest --cov=soup_cli --cov-fail-under=77

# Run code quality checks
ruff format src/ tests/
ruff check src/ tests/
mypy src/

# Commit your changes
git commit -am "Brief description of changes"
```

## Pull Request Process

1. **Update the README.md** with details of changes to the interface if applicable
2. **Update documentation** in the `docs/` directory if adding new features
3. **Add tests** for any new functionality
4. **Ensure all tests pass** before submitting:
   ```bash
   pytest --cov=soup_cli --cov-fail-under=77
   ```
5. **Update the CHANGELOG.md** (if present) with notes on your changes
6. **Push to your fork** and open a Pull Request

### Pull Request Requirements

- [ ] Tests pass locally (`pytest`)
- [ ] Code is formatted (`ruff format`)
- [ ] Linting passes (`ruff check`)
- [ ] Type checking passes (`mypy src/`)
- [ ] All new public methods have docstrings
- [ ] Changes are documented in README or docs/

### Commit Message Guidelines

Use clear, descriptive commit messages:

```
type(scope): subject

body

footer
```

**Types:**
- `feat:` A new feature
- `fix:` A bug fix
- `docs:` Documentation changes
- `style:` Code style changes (formatting, missing semicolons, etc.)
- `refactor:` Code refactoring without feature changes
- `perf:` Performance improvements
- `test:` Adding or updating tests
- `chore:` Build process, dependencies, etc.

**Example:**
```
feat(training): add support for DPO training method

- Implement DPO trainer wrapper
- Add DPO-specific configuration options
- Add comprehensive tests for DPO training

Closes #123
```

## Reporting Bugs

### Before Submitting a Bug Report

- Check the issue tracker to see if the problem has already been reported
- Search closed issues - the problem might already be fixed
- Check the documentation and existing discussions

### How to Submit a Good Bug Report

Bugs are tracked as GitHub issues. Create an issue and provide the following information:

1. **Use a clear, descriptive title**
2. **Describe the exact steps to reproduce** the problem
3. **Provide specific examples** to demonstrate the steps
4. **Describe the behavior you observed** after following the steps
5. **Explain which behavior you expected** to see instead and why
6. **Include screenshots or GIFs** if possible
7. **Include your environment**:
   ```bash
   python --version
   pip show soup-cli
   # For GPU issues:
   nvidia-smi
   ```

### Example Bug Report

```
Title: Training fails with OOM error on 8GB GPU

Steps to reproduce:
1. Install soup-cli[train]
2. Download Llama-2-7b
3. Create test data with 10,000 examples
4. Run: ai-forge train --model meta-llama/Llama-2-7b --batch-size 32

Expected behavior:
Training should start and monitor memory usage

Actual behavior:
CUDA out of memory error after 2 iterations

Environment:
- Python 3.10.11
- torch 2.0.1
- CUDA 12.1
- GPU: NVIDIA RTX 3060 (12GB VRAM)
```

## Feature Requests

### Before Submitting a Feature Request

- Check the documentation to see if the feature already exists
- Check the issue tracker to see if the feature has already been requested

### How to Submit a Good Feature Request

1. **Use a clear, descriptive title**
2. **Describe the exact use case** for the feature
3. **Describe the expected behavior**
4. **List some examples** where the feature would be useful
5. **Explain why this would be useful** to most AI Forge users

### Example Feature Request

```
Title: Add support for LoRA merging

Use case:
After fine-tuning with LoRA, users want to merge the adapter weights
into the base model for inference without requiring separate files.

Expected behavior:
ai-forge merge --adapter ./outputs/adapter --base meta-llama/Llama-2-7b

This would:
- Load the base model
- Load the LoRA weights
- Merge them together
- Save as a standard model

Why this is useful:
- Simplifies inference setup
- Reduces storage requirements
- Enables direct integration with inference frameworks
```

## Architecture Overview

```
src/soup_cli/
├── cli.py              # Main CLI entry point
├── commands/           # Command implementations
│   ├── train.py       # Training commands
│   ├── eval.py        # Evaluation commands
│   ├── data.py        # Data processing
│   └── serve.py       # Model serving
├── core/              # Core functionality
│   ├── trainers/      # Training algorithms
│   ├── data/          # Data loading and processing
│   ├── models/        # Model definitions
│   └── eval/          # Evaluation logic
└── utils/             # Utilities
```

## Code Style Guidelines

### Python Style

We use [Ruff](https://github.com/astral-sh/ruff) for code formatting and linting.

- Maximum line length: 100 characters
- Target Python version: 3.10+
- Use type hints where possible

### Docstrings

Use concise docstrings:

```python
def train_model(config: TrainingConfig) -> Model:
    """Train a language model using the provided configuration.
    
    Args:
        config: Training configuration object
        
    Returns:
        Trained model instance
    """
```

### Comments

- Add comments only for WHY, not WHAT
- Keep comments concise
- Update comments when changing code

## Testing Guidelines

### Test Organization

```
tests/
├── unit/              # Fast, isolated tests
├── integration/       # Tests requiring real I/O
├── conftest.py        # Shared test fixtures
└── test_*.py          # Test files
```

### Writing Tests

```python
import pytest
from soup_cli.core.trainers import SFTTrainer

@pytest.mark.unit
def test_sft_trainer_initialization():
    """Test SFTTrainer initialization with valid config."""
    trainer = SFTTrainer(config=...)
    assert trainer is not None

@pytest.mark.integration
def test_training_end_to_end(tmp_path):
    """Test complete training pipeline."""
    # Setup
    # Execute
    # Assert
```

### Test Coverage

- New features should have test coverage
- Aim for >77% overall coverage (our CI threshold)
- Focus on logic, not just line coverage

## Documentation Guidelines

### README Updates

Update the main [README.md](README.md) if you:
- Add new commands
- Change command behavior
- Add new features

### Adding New Guides

Create new documentation files in `docs/` for:
- New training methods
- New data formats
- New deployment options
- New advanced features

Follow the style of existing docs:
- Start with a brief overview
- Include example commands
- Add links to related documentation
- Include troubleshooting section if applicable

## Community

### Code of Conduct

This project adheres to the Contributor Covenant. By participating, you are expected to uphold this code. Please report unacceptable behavior to the project maintainers.

### Questions?

- Open an issue with `[question]` tag
- Start a discussion on GitHub Discussions
- Join our [Discord community](https://discord.gg/8RgVbFA6Zq)

## License

By contributing to AI Forge, you agree that your contributions will be licensed under its Apache License 2.0.

## Recognition

Contributors will be recognized in:
- The main README (top contributors)
- Release notes (per-release contributors)
- GitHub contributors page

---

Thank you for contributing to AI Forge! 🙏
