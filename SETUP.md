# Setup & Installation Guide

Complete setup instructions for AI Forge development and usage.

## Table of Contents

1. [System Requirements](#system-requirements)
2. [Installation](#installation)
3. [Verification](#verification)
4. [Docker Setup](#docker-setup)
5. [Troubleshooting](#troubleshooting)

## System Requirements

### Minimum Requirements

- **OS**: Linux (Ubuntu 20.04+), macOS (12.0+), or Windows 10+
- **Python**: 3.10, 3.11, or 3.12
- **RAM**: 16GB minimum (32GB+ recommended for large models)
- **Disk**: 50GB+ free space

### For GPU Training (Recommended)

- **NVIDIA**: CUDA 12.1+ (RTX 3060 or better)
- **AMD**: ROCm 5.7+ (supported but less tested)
- **Apple Silicon**: M1/M2+ (via MLX backend)

### Check Your System

```bash
# Check Python version
python --version  # Should be 3.10+

# Check available RAM
# Linux/macOS:
free -h
# Windows:
wmic OS get TotalVisibleMemorySize,FreePhysicalMemory

# Check disk space
df -h /  # Linux/macOS
dir C:\  # Windows

# Check GPU (if available)
# NVIDIA:
nvidia-smi
# AMD:
rocm-smi
```

## Installation

### Installation from GitHub

```bash
# Clone the repository
git clone https://github.com/mohdhasnain-pixel/ai-forge.git
cd ai-forge

# Create virtual environment
python -m venv env
source env/bin/activate  # Windows: env\Scripts\activate

# Basic installation (CLI only)
pip install -e .

# With training support (recommended)
pip install -e ".[train]"

# Full installation (all features)
pip install -e ".[all]"

# With specific extras
pip install -e ".[train,serve,data,eval]"
```

### Using Conda

```bash
# Create conda environment
conda create -n ai-forge python=3.10

# Activate environment
conda activate ai-forge

# Clone and install
git clone https://github.com/mohdhasnain-pixel/ai-forge.git
cd ai-forge
pip install -e ".[train]"
```

## Verification

### Test Installation

```bash
# Check installation
ai-forge --version

# Verify environment
ai-forge env check

# View help
ai-forge --help
```

### Quick Test Training

```bash
# Create test data
cat > test_data.jsonl << 'EOF'
{"messages": [{"role": "system", "content": "You are helpful"}, {"role": "user", "content": "Hello"}, {"role": "assistant", "content": "Hi!"}]}
{"messages": [{"role": "system", "content": "You are helpful"}, {"role": "user", "content": "What is 2+2?"}, {"role": "assistant", "content": "4"}]}
EOF

# Initialize training config
ai-forge init --task sft --model gpt2

# Run a quick training test (adjust batch size and epochs for your hardware)
ai-forge train --epochs 1 --batch-size 1 --data test_data.jsonl --model gpt2

# Check outputs
ls -la outputs/
```

## Docker Setup

### Build Docker Image

```bash
# Build with GPU support
docker build -t ai-forge:latest .

# Build with specific PyTorch version
docker build --build-arg PYTORCH_VERSION=2.0.1 -t ai-forge:latest .
```

### Run Training in Docker

```bash
# Run with GPU access
docker run --gpus all \
  -v $(pwd)/data:/workspace/data \
  -v $(pwd)/outputs:/workspace/outputs \
  ai-forge:latest \
  train --data /workspace/data/train.jsonl

# Run with custom command
docker run --gpus all \
  -v $(pwd)/data:/workspace/data \
  ai-forge:latest \
  eval --model <model-path>
```

### Docker Compose (Optional)

Create `docker-compose.yml`:

```yaml
version: '3.8'

services:
  ai-forge:
    build: .
    image: ai-forge:latest
    container_name: ai-forge-train
    runtime: nvidia
    environment:
      - NVIDIA_VISIBLE_DEVICES=all
    volumes:
      - ./data:/workspace/data
      - ./outputs:/workspace/outputs
      - ./models:/workspace/models
    working_dir: /workspace
    command: train --data /workspace/data/train.jsonl
```

Run with: `docker-compose up`

## GPU Setup

### NVIDIA CUDA

```bash
# Check CUDA installation
nvidia-smi

# If not installed, download from:
# https://developer.nvidia.com/cuda-12-1-0-download-archive

# Verify CUDA works with PyTorch
python -c "import torch; print(torch.cuda.is_available())"
```

### AMD ROCm

```bash
# Check ROCm installation
rocm-smi

# Install ROCm PyTorch
pip install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/rocm5.7
```

### Apple Silicon (MLX)

```bash
# Install MLX backend support
pip install -e ".[all]"  # Includes MLX support

# Initialize with MLX backend
ai-forge init --task sft --backend mlx --model <mlx-model>
```

## Environment Variables

```bash
# Suppress HuggingFace tokenizer warnings
export TOKENIZERS_PARALLELISM=false

# Disable CUDA if needed (CPU-only mode)
export CUDA_VISIBLE_DEVICES=""

# Set number of workers for data loading
export NUM_WORKERS=4

# Set HuggingFace cache directory
export HF_HOME=/path/to/cache

# Enable debug logging
export DEBUG=1
```

## Virtual Environment Setup

### Using venv

```bash
# Create environment
python -m venv env

# Activate environment
# Linux/macOS:
source env/bin/activate
# Windows:
env\Scripts\activate

# Install dependencies
pip install --upgrade pip
pip install -e ".[train]"

# Deactivate when done
deactivate
```

### Using Poetry (Optional)

```bash
# Install Poetry
pip install poetry

# Create environment
poetry install

# Activate environment
poetry shell

# Run commands within environment
poetry run ai-forge --help
```

## Development Setup

### Install Development Tools

```bash
# Clone repository
git clone https://github.com/mohdhasnain-pixel/ai-forge.git
cd ai-forge

# Create virtual environment
python -m venv env
source env/bin/activate

# Install development dependencies
pip install -e ".[dev]"

# Install pre-commit hooks
pre-commit install

# Run tests to verify setup
pytest -m unit
```

### IDE Setup

#### VS Code

1. Install Python extension
2. Select interpreter: `Python: Select Interpreter`
3. Choose `./env/bin/python`
4. Install Pylance extension
5. Create `.vscode/settings.json`:

```json
{
  "python.linting.enabled": true,
  "python.linting.ruffEnabled": true,
  "python.formatting.provider": "none",
  "[python]": {
    "editor.formatOnSave": true,
    "editor.defaultFormatter": "charliermarsh.ruff"
  }
}
```

#### PyCharm

1. Go to Settings → Project → Python Interpreter
2. Click gear icon → Add
3. Select existing environment
4. Choose `./env/bin/python`
5. Enable ruff plugin: Settings → Plugins → Search "Ruff"

## Troubleshooting

### Python Version Issues

```bash
# Check Python version
python --version

# If wrong version, try python3:
python3 --version
pip3 install ...

# Update pip
python -m pip install --upgrade pip
```

### GPU Not Detected

```bash
# Check NVIDIA GPU
nvidia-smi

# If not found, reinstall CUDA:
# 1. Download from https://developer.nvidia.com/cuda-downloads
# 2. Follow installation guide
# 3. Add to PATH (Windows) or update ~/.bashrc (Linux)

# Test PyTorch GPU support
python -c "import torch; print(torch.cuda.is_available())"
```

### Memory Issues

```bash
# Reduce batch size
ai-forge train --batch-size 4

# Enable gradient checkpointing
ai-forge train --gradient-checkpointing

# Use 8-bit optimization
pip install bitsandbytes
ai-forge train --use-8bit

# Switch to CPU-only (slow but works)
export CUDA_VISIBLE_DEVICES=""
```

### Installation Problems

```bash
# Clear pip cache
pip cache purge

# Reinstall in clean environment
python -m venv env_clean
source env_clean/bin/activate
pip install -e ".[train]"

# If still failing, check Python version and compatibility
python --version
pip list | grep torch
```

### Permission Errors (Linux/macOS)

```bash
# If you see permission denied:
# Option 1: Use sudo (not recommended)
sudo git clone https://github.com/mohdhasnain-pixel/ai-forge.git
cd ai-forge
pip install -e .

# Option 2: Install to user directory (recommended)
pip install --user ai-forge

# Option 3: Use virtual environment (best)
python -m venv env
source env/bin/activate
git clone https://github.com/mohdhasnain-pixel/ai-forge.git
cd ai-forge
pip install -e .
```

## Next Steps

After installation, check out:

1. **Quick Start**: See [README.md](README.md) Quick Start section
2. **Training Guide**: See [docs/training.md](docs/training.md)
3. **Examples**: See examples in [README.md](README.md) Examples section
4. **Full Docs**: See [docs/README.md](docs/README.md)

## Getting Help

- **Documentation**: https://github.com/mohdhasnain-pixel/ai-forge/tree/main/docs
- **Issues**: https://github.com/mohdhasnain-pixel/ai-forge/issues
- **Discussions**: https://github.com/mohdhasnain-pixel/ai-forge/discussions
- **Discord**: Join our community Discord

---

**Installation complete!** 🎉 Run `ai-forge --help` to get started.
