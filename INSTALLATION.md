# AI Forge Installation Guide

## ⚠️ Note: Pre-Release Version

AI Forge is currently in **pre-release** and must be installed from GitHub source. PyPI publishing is planned for a future release.

## Quick Install

```bash
# Clone the repository
git clone https://github.com/mohdhasnain-pixel/ai-forge.git
cd ai-forge

# Create virtual environment
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install with training support
pip install -e ".[train]"

# Verify installation
ai-forge --version
ai-forge env check
```

## Installation Variants

### 1. Minimal Installation (CLI Only)
```bash
pip install -e .
```

### 2. With Training Support (Recommended)
```bash
pip install -e ".[train]"
```

### 3. Full Installation (All Features)
```bash
pip install -e ".[all]"
```

### 4. Development Installation
For contributors and developers:
```bash
pip install -e ".[dev]"
pre-commit install
```

### 5. Specific Features
```bash
# Data engineering tools
pip install -e ".[data]"

# Serving and deployment
pip install -e ".[serve]"

# Evaluation framework
pip install -e ".[eval]"

# Multiple features
pip install -e ".[train,serve,data,eval]"
```

## Platform-Specific Setup

### Linux/macOS

```bash
git clone https://github.com/mohdhasnain-pixel/ai-forge.git
cd ai-forge
python3 -m venv venv
source venv/bin/activate
pip install -e ".[train]"
```

### Windows

```bash
git clone https://github.com/mohdhasnain-pixel/ai-forge.git
cd ai-forge
python -m venv venv
venv\Scripts\activate
pip install -e ".[train]"
```

### macOS with Homebrew

```bash
# Install Python if not already installed
brew install python@3.10

# Clone and setup
git clone https://github.com/mohdhasnain-pixel/ai-forge.git
cd ai-forge
python3.10 -m venv venv
source venv/bin/activate
pip install -e ".[train]"
```

## GPU Setup

### NVIDIA GPUs

```bash
# Verify CUDA is installed
nvidia-smi

# Install AI Forge with GPU support
git clone https://github.com/mohdhasnain-pixel/ai-forge.git
cd ai-forge
python -m venv venv
source venv/bin/activate
pip install -e ".[train]"

# Verify GPU detection
python -c "import torch; print(torch.cuda.is_available())"
```

### AMD GPUs (ROCm)

```bash
# Install ROCm (https://rocmdocs.amd.com/en/latest/deploy/linux/index.html)

# Clone and setup
git clone https://github.com/mohdhasnain-pixel/ai-forge.git
cd ai-forge
python -m venv venv
source venv/bin/activate

# Install with ROCm PyTorch
pip install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/rocm5.7
pip install -e ".[train]"
```

### Apple Silicon (M1/M2/M3)

```bash
# Clone and setup
git clone https://github.com/mohdhasnain-pixel/ai-forge.git
cd ai-forge
python -m venv venv
source venv/bin/activate

# Install with MLX backend
pip install -e ".[all]"

# Initialize with MLX
ai-forge init --backend mlx --model <mlx-model>
```

## Docker Installation

### Build Docker Image

```bash
git clone https://github.com/mohdhasnain-pixel/ai-forge.git
cd ai-forge

# Build with CUDA 12.1
docker build -t ai-forge:latest .

# Run container
docker run --gpus all -it ai-forge:latest bash
```

### Use Pre-built Dockerfile

```bash
# The Dockerfile includes CUDA 12.1 and all training dependencies
# See Dockerfile for details
docker build -t my-ai-forge:latest .
```

## Verification

### Test Installation

```bash
# Check version
ai-forge --version

# Verify environment
ai-forge env check

# View help
ai-forge --help

# Test data tools
ai-forge data --help

# Test training
ai-forge train --help
```

### Quick Test

```bash
# Create sample data
cat > test.jsonl << 'EOF'
{"messages": [{"role": "system", "content": "You are helpful"}, {"role": "user", "content": "Hello"}, {"role": "assistant", "content": "Hi!"}]}
EOF

# Test training
ai-forge train --model gpt2 --data test.jsonl --epochs 1 --batch-size 1
```

## Troubleshooting

### Git Clone Issues

```bash
# If you get "command not found: git"
# Install git:
# Ubuntu/Debian: sudo apt-get install git
# macOS: brew install git
# Windows: Download from https://git-scm.com/download/win
```

### Python Version Issues

```bash
# Check Python version
python --version  # Should be 3.10+

# If version is wrong, try:
python3 --version

# Use specific version if available
python3.10 -m venv venv
```

### Virtual Environment Issues

```bash
# If activation fails on Windows:
# Use: venv\Scripts\activate.bat (for Command Prompt)
# Or: venv\Scripts\Activate.ps1 (for PowerShell)

# If on Linux/macOS:
source venv/bin/activate
```

### CUDA/GPU Issues

```bash
# Check NVIDIA GPU
nvidia-smi

# Verify PyTorch sees GPU
python -c "import torch; print(f'GPU Available: {torch.cuda.is_available()}')"

# If GPU not detected, reinstall PyTorch for your CUDA version
# See: https://pytorch.org/get-started/locally/
```

### Dependency Conflicts

```bash
# If you get dependency errors, try:
pip install --upgrade pip
pip install --upgrade setuptools wheel

# Then reinstall
pip install -e ".[train]"

# If still failing, use fresh virtual environment
rm -rf venv
python -m venv venv
source venv/bin/activate
pip install -e ".[train]"
```

## Update Installation

To update to the latest version:

```bash
cd ai-forge
git pull origin main
pip install -e ".[train]"  # Re-install in case dependencies changed
```

## Next Steps

After installation, see:
- [README.md](README.md) - Project overview
- [SETUP.md](SETUP.md) - Detailed setup guide
- [PROJECT_LIFECYCLE.md](PROJECT_LIFECYCLE.md) - Complete workflow
- [docs/training.md](docs/training.md) - Training guide

## Support

- **Issues**: https://github.com/mohdhasnain-pixel/ai-forge/issues
- **Discussions**: https://github.com/mohdhasnain-pixel/ai-forge/discussions
- **Documentation**: [docs/](docs/)

---

**Status**: Pre-release (source installation only)  
**Latest Version**: See `git log --oneline` or [Releases](https://github.com/mohdhasnain-pixel/ai-forge/releases)
