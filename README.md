# mixture_of_adapters
Dynamic Mixture of Adapters prototype for loading and switching LoRA adapters at inference time.
# MoA - Mixture of Adapters (Prototype)

**Dynamic adapter routing for specialized local agent behavior.**

A research prototype exploring **Mixture of Adapters (MoA)** — a system that uses an MLP router to dynamically select the best specialized LoRA adapter based on the input prompt. The goal is to get the benefits of multiple domain-specific adapters without manually choosing which one to use.

## Architecture

- **Base Model**: Qwen2.5-Coder 14B Instruct (4-bit)
- **Adapters**: reasoning, tool_use, safety, pentesting (each rank 64)
- **Router**: MLP (3 hidden layers) trained on last-hidden-state embeddings
- **Inference**: Hugging Face + PEFT (slow but functional)
Prompt → Embedding → MLP Router → Selected Adapter → Generation

## Current Status (May 2026)

**This is a research prototype, not a finished product.**

- Router validation accuracy: ~86%
- The router frequently confuses:
  - reasoning ↔ tool_use (many tool use examples are reasoning-heavy)
  - safety ↔ pentesting (embeddings were generated from a jailbroken model)
- Adapters were trained on limited, single-domain data. Multi-adapter combinations (e.g. reasoning + tool_use) were not included in training.
- Inference is slow (Hugging Face backend). A custom Rust GGUF inference engine with native dynamic adapter switching is planned.

## Why This Exists

Most local agents use a single static LoRA. This project explores whether we can get **specialized behavior on demand** by routing between multiple adapters at inference time. Early results show promise, but label noise in the training data and embedding misalignment are current limitations.

## Quick Start

```bash
git clone https://github.com/charlesericwilson-portfolio/mixture_of_adapters
cd mixture_of_adapters
Update the config/config.yaml with correct model paths

# Install Python backend (prototype)
pip install -r requirements.txt
python moa_server.py

# In a second terminal use the Gradio frontend
python moa_frontend.py
