# AI Forge

> **Fine-tune and post-train LLMs in one command. No SSH, no config hell.**

[![Python 3.10+](https://img.shields.io/badge/python-3.10%2B-blue)](https://www.python.org/downloads/)
[![License: Apache 2.0](https://img.shields.io/badge/License-Apache%202.0-green.svg)](LICENSE)
[![Tests](https://img.shields.io/badge/tests-passing-brightgreen)]()

AI Forge is an end-to-end LLM training platform that simplifies fine-tuning, post-training, and evaluation of large language models. Built for researchers and practitioners who want production-grade training without infrastructure overhead.

## ✨ Key Features

- **One-Command Training** - Start training with `ai-forge train` (no config hell)
- **Multiple Training Methods** - SFT, DPO, GRPO, PPO, KTO, ORPO, and more
- **Efficiency First** - LoRA, DoRA, QLoRA, and other parameter-efficient techniques built-in
- **Quantization & Optimization** - QAT, FP8, KV-cache, and gradient checkpointing
- **Data Engineering** - Built-in data tools, synthetic generation, and quality scorecards
- **Evaluation Framework** - Comprehensive eval gates and benchmarking
- **Production Ready** - OpenAI-compatible serving, export, and deployment autopilot
- **Compliance & Governance** - HIPAA, SOC2, EU-AI-Act templates and audit logs
- **Multi-Backend** - GPU (NVIDIA/AMD), MLX (Apple Silicon), Modal (serverless cloud)

## 🚀 Quick Start

### Installation

```bash
# Clone the repository
git clone https://github.com/mohdhasnain-pixel/ai-forge.git
cd ai-forge

# Create virtual environment
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install in development mode
pip install -e .

# With training support (recommended for fine-tuning)
pip install -e ".[train]"

# Full stack (all optional features)
pip install -e ".[all]"
```

### Your First Training Job

```bash
# 1. Prepare your data
ai-forge data format --input data.jsonl --output data_formatted.jsonl

# 2. Create a training config
ai-forge init --task sft --model meta-llama/Llama-2-7b

# 3. Start training
ai-forge train

# 4. Evaluate your model
ai-forge eval

# 5. Deploy
ai-forge serve --model ./outputs/model
```

## 📖 Documentation

| Resource | Description |
|----------|-------------|
| **[Getting Started](docs/README.md)** | 5-minute introduction and feature overview |
| **[Training Guide](docs/training.md)** | SFT, DPO, GRPO, PPO, KTO, ORPO, and other methods |
| **[Data Engineering](docs/data.md)** | Data formats, mixing, synthetic generation, quality scoring |
| **[Evaluation & Probes](docs/evaluation.md)** | Eval gates, benchmarking, X-ray probes, drift detection |
| **[PEFT & Efficiency](docs/peft-and-efficiency.md)** | LoRA, DoRA, QLoRA, long context, auto-tuning |
| **[Performance & Quantization](docs/performance-and-quantization.md)** | QAT, FP8, KV-cache, multi-GPU, DeepSpeed, FSDP |
| **[Serving & Export](docs/serving-and-export.md)** | OpenAI-compatible API, batch inference, deployment |
| **[Adapters & Registry](docs/adapters-and-governance.md)** | Adapter lifecycle, model registry, supply-chain controls |
| **[Backends & Ops](docs/backends-and-ops.md)** | MLX, Unsloth, Modal cloud, HF Hub, experiment tracking |
| **[Compliance](docs/compliance.md)** | HIPAA, SOC2, EU-AI-Act, audit logs, model cards |
| **[Command Reference](docs/commands.md)** | Complete CLI command reference |
| **[Models & Hardware](docs/models.md)** | Recommended models, VRAM guide, extras matrix |

## 🛠️ Common Commands

After installation, use these commands:

```bash
# Data operations
ai-forge data format      # Convert data to training format
ai-forge data demo        # Generate demo dataset
ai-forge data mix         # Mix multiple datasets

# Training
ai-forge train           # Start training
ai-forge train --help    # See all training options

# Evaluation
ai-forge eval            # Run evaluation
ai-forge eval --help     # See all eval options

# Serving
ai-forge serve           # Start OpenAI-compatible server
ai-forge serve --help    # See all serving options

# Utilities
ai-forge env check       # Check environment and dependencies
ai-forge --version       # Show version info
ai-forge --help          # Full command list
```

## 📊 Project Lifecycle

### 1. **Setup Phase**
```bash
# Check system setup
python -m pip install --upgrade pip
pip install "ai-forge[train]"
ai-forge env check

# Initialize project
ai-forge init --task sft --model <model-name>
```

### 2. **Data Preparation Phase**
```bash
# Explore and validate data
ai-forge data inspect data.jsonl

# Format data for training
ai-forge data format --input data.jsonl --output formatted.jsonl

# Mix and balance datasets
ai-forge data mix dataset1.jsonl dataset2.jsonl --output mixed.jsonl

# Quality check
ai-forge data quality check formatted.jsonl
```

### 3. **Configuration & Planning Phase**
```bash
# Review generated config
cat training_config.yaml

# Plan training (dry-run without actual training)
ai-forge train --plan

# Validate config
ai-forge validate config training_config.yaml
```

### 4. **Training Phase**
```bash
# Start training
ai-forge train

# Monitor training
# (Logs and metrics available in ./outputs/logs)

# Resume interrupted training
ai-forge train --resume ./outputs/checkpoint
```

### 5. **Evaluation Phase**
```bash
# Run evaluation on holdout set
ai-forge eval

# Benchmark against baselines
ai-forge eval --benchmark

# Generate eval report
ai-forge eval --format html > eval_report.html
```

### 6. **Optimization Phase**
```bash
# Quantize model
ai-forge quant --method fp8 --model ./outputs/model

# Export for inference
ai-forge export --format gguf --model ./outputs/model

# Test inference
ai-forge generate --model ./outputs/model --prompt "Hello"
```

### 7. **Serving & Deployment Phase**
```bash
# Start OpenAI-compatible server
ai-forge serve --model ./outputs/model --port 8000

# In another terminal, test the API
curl http://localhost:8000/v1/completions \
  -H "Content-Type: application/json" \
  -d '{"model": "model", "prompt": "Hello", "max_tokens": 100}'

# Deploy to cloud
ai-forge deploy --model ./outputs/model --cloud modal
```

## 🔧 Installation & Setup

### System Requirements

- **Python**: 3.10, 3.11, or 3.12
- **RAM**: Minimum 16GB (32GB+ recommended for large models)
- **GPU** (optional): NVIDIA GPU with CUDA 12.1+ or AMD GPU with ROCm
- **Disk**: 50GB+ free space for models

### Environment Setup

```bash
# Create virtual environment
python -m venv env
source env/bin/activate  # On Windows: env\Scripts\activate

# Install development dependencies
pip install "ai-forge[dev]"

# Run pre-commit hooks
pre-commit install
```

### Docker Setup

```bash
# Build Docker image with GPU support
docker build -t ai-forge:latest .

# Run training in container
docker run --gpus all -v $(pwd)/data:/workspace/data \
  -v $(pwd)/outputs:/workspace/outputs ai-forge:latest \
  train --data /workspace/data/train.jsonl
```

## 🧪 Testing

```bash
# Run all tests
pytest

# Run unit tests only (fast)
pytest -m unit

# Run integration tests
pytest -m integration

# Run with coverage report
pytest --cov=ai_forge_cli --cov-report=html

# Run specific test file
pytest tests/test_training.py -v
```

## 📋 Examples

### Example 1: Fine-tune Llama-2 on Custom Data

```bash
# 1. Prepare data in JSONL format
cat > data.jsonl << 'EOF'
{"messages": [{"role": "system", "content": "You are helpful"}, {"role": "user", "content": "Hello"}, {"role": "assistant", "content": "Hi there!"}]}
EOF

# 2. Start training
ai-forge train \
  --model meta-llama/Llama-2-7b \
  --data data.jsonl \
  --task sft \
  --learning-rate 2e-4 \
  --epochs 3

# 3. Monitor progress in outputs/
ls -la outputs/
tail -f outputs/logs/training.log
```

### Example 2: DPO Training with Preference Data

```bash
# Prepare preference data
cat > preferences.jsonl << 'EOF'
{"prompt": "What is 2+2?", "chosen": "The answer is 4", "rejected": "The answer is 5"}
EOF

# Train with DPO
ai-forge train \
  --model meta-llama/Llama-2-7b-chat \
  --data preferences.jsonl \
  --task dpo \
  --learning-rate 5e-5
```

### Example 3: Serve Model with OpenAI-Compatible API

```bash
# Start server
ai-forge serve \
  --model ./outputs/model \
  --port 8000 \
  --openai-format

# Test with Python client
python -c "
import requests
response = requests.post('http://localhost:8000/v1/completions', json={
    'model': 'model',
    'prompt': 'Once upon a time',
    'max_tokens': 100
})
print(response.json())
"
```

## 🤝 Contributing

We welcome contributions! Please see [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

### Development Workflow

```bash
# 1. Fork the repo and clone your fork
git clone https://github.com/YOUR_USERNAME/ai-forge.git
cd ai-forge

# 2. Create a feature branch
git checkout -b feature/your-feature-name

# 3. Install development dependencies
pip install -e ".[dev]"
pre-commit install

# 4. Make changes and test
pytest tests/

# 5. Push and create a pull request
git push origin feature/your-feature-name
```

### Code Quality

```bash
# Format code
ruff format src/ tests/

# Lint code
ruff check src/ tests/

# Type checking
mypy src/

# Run tests
pytest --cov=ai_forge_cli
```

## 📈 Performance & Benchmarks

See [BENCHMARKS.md](BENCHMARKS.md) for detailed performance numbers on various hardware setups.

## 🔐 Security & Compliance

- **HIPAA Ready**: Templates in `docs/compliance.md`
- **SOC2 Compliant**: Audit logs and access controls
- **EU-AI-Act**: Compliance templates included
- **Supply Chain**: Model attestation and provenance tracking

## ❓ FAQ

**Q: Do I need a GPU?**
A: GPU is recommended for training. CPU-only training is supported but slow.

**Q: Can I use this on Apple Silicon (M1/M2)?**
A: Yes! Use the MLX backend: `ai-forge init --backend mlx`

**Q: What models are supported?**
A: See [docs/models.md](docs/models.md) for the full list. Llama, Mistral, Phi, QwQ, and many more.

**Q: How do I monitor training progress?**
A: Check `outputs/logs/training.log` or use `ai-forge monitor` for live metrics.

**Q: Can I train without configuring anything?**
A: Yes! `ai-forge train` starts training with sensible defaults.

**Q: How do I deploy a fine-tuned model?**
A: See [docs/serving-and-export.md](docs/serving-and-export.md) for deployment options.

## 📞 Support

- **Documentation**: Check [docs/](docs/) for comprehensive guides
- **Issues**: Report bugs on [GitHub Issues](https://github.com/mohdhasnain-pixel/ai-forge/issues)
- **Discussions**: Ask questions on [GitHub Discussions](https://github.com/mohdhasnain-pixel/ai-forge/discussions)
- **Discord**: Join our community [Discord server](https://discord.gg/8RgVbFA6Zq)

## 📜 License

This project is licensed under the **Apache License 2.0** - see [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

Built on top of industry-leading libraries:
- [Transformers](https://huggingface.co/transformers/) - HuggingFace
- [PEFT](https://github.com/huggingface/peft) - Parameter-Efficient Fine-Tuning
- [TRL](https://github.com/huggingface/trl) - Transformer Reinforcement Learning
- [Accelerate](https://huggingface.co/accelerate/) - Training acceleration
- [PyTorch](https://pytorch.org/) - Deep learning framework

---

**Made with ❤️ for the AI community**

[GitHub](https://github.com/mohdhasnain-pixel/ai-forge) • [Issues](https://github.com/mohdhasnain-pixel/ai-forge/issues) • [Discussions](https://github.com/mohdhasnain-pixel/ai-forge/discussions)
