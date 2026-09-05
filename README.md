# sovereign-ai-harness

Ten stages for building household AI capability — from a model file on a shelf to a locally-owned intelligence stack.

Extracted from [solystopia.tech/tools](https://solystopia.tech/tools) — Project Hearthmind's working guide to the Sovereign AI Harness.

Stages 00 and 01 are documented with working commands. Stages 02–09 are named and planned — updated as each stage is built and tested.

---

## The ten stages

| Stage | Name | Status | What it is |
|---|---|---|---|
| 00 | Model | ✅ Live | Pick a small, open-weight model you can hold in your hand |
| 01 | Runtime | ✅ Live | The runner that loads the model and lets it think |
| 02 | Interface | 🔨 Planned | How a household talks to the model |
| 03 | Memory | 🔨 Planned | Context that persists — on your machine, not a vendor server |
| 04 | Tools & actions | 🔨 Planned | Letting the model act, carefully |
| 05 | Orchestration | 🔨 Planned | Running more than one model, routing tasks between them |
| 06 | Data ingestion | 🔨 Planned | Feeding the model household-specific knowledge |
| 07 | Voice | 🔨 Planned | Speaking to and from the model |
| 08 | Energy coupling | 🔨 Planned | Scheduling workloads against on-site generation |
| 09 | Community | 🔨 Planned | Sharing capability across households |

---

## Stage 00 — Model

**The goal:** pick a model file you physically control. A GGUF from HuggingFace, saved to local disk, with a known provenance.

### Recommended starting models

These are small enough to run on modest hardware, good enough to be genuinely useful, and openly licensed.

| Model | Size | RAM required | Good for |
|---|---|---|---|
| Llama-3.2-3B-Instruct.Q4_K_M.gguf | ~2GB | 4GB | Fast, general purpose |
| Qwen2.5-7B-Instruct.Q4_K_M.gguf | ~4.5GB | 8GB | Better reasoning, still fast |
| Llama-3.1-8B-Instruct.Q4_K_M.gguf | ~5GB | 8GB | Solid baseline |
| Mistral-7B-Instruct-v0.3.Q5_K_M.gguf | ~5GB | 8GB | Instruction following |
| Phi-3.5-mini-instruct.Q5_K_M.gguf | ~2.5GB | 4GB | Very fast, lightweight tasks |

**Q4_K_M** is the recommended quantisation tier: good quality, runs on most hardware.

### Download a model

```bash
# Example: Llama 3.2 3B (fast, small)
mkdir -p models
curl -L -o models/llama-3.2-3b-instruct.Q4_K_M.gguf \
  "https://huggingface.co/bartowski/Llama-3.2-3B-Instruct-GGUF/resolve/main/Llama-3.2-3B-Instruct-Q4_K_M.gguf"

# Example: Qwen 2.5 7B (better reasoning)
curl -L -o models/qwen2.5-7b-instruct.Q4_K_M.gguf \
  "https://huggingface.co/Qwen/Qwen2.5-7B-Instruct-GGUF/resolve/main/qwen2.5-7b-instruct-q4_k_m.gguf"
```

See `stages/00-model.md` for the full guide including hardware sizing, quantisation tiers, and model selection notes.

---

## Stage 01 — Runtime

**The goal:** install llama.cpp and confirm the model runs locally.

### Install llama.cpp

```bash
# macOS (Homebrew)
brew install llama.cpp

# From source (any platform)
git clone https://github.com/ggerganov/llama.cpp
cd llama.cpp && make -j$(nproc)
```

### Run your first inference

```bash
# Interactive chat
llama-cli -m models/llama-3.2-3b-instruct.Q4_K_M.gguf \
  --chat-template llama3 \
  -ngl 99 \
  -p "You are a helpful household assistant."

# One-shot query (pipe-friendly)
echo "What is the capital of France?" | \
  llama-cli -m models/llama-3.2-3b-instruct.Q4_K_M.gguf \
  --chat-template llama3 -ngl 99 -no-cnv

# OpenAI-compatible API server (for integrations)
llama-server -m models/llama-3.2-3b-instruct.Q4_K_M.gguf \
  --host 0.0.0.0 --port 8080 -ngl 99
```

### Benchmark your hardware

```bash
llama-bench -m models/llama-3.2-3b-instruct.Q4_K_M.gguf -n 128 -ngl 99
```

Record: `t/s` (tokens per second). This is your baseline. Stage 08 will route heavy jobs to hours when solar is producing.

See `stages/01-runtime.md` for the full guide including GPU offload, context window sizing, and config templates.

---

## Stage 02–09 — Planned

These stages are named and designed, not yet fully documented. They will be filled in as each is built and tested on actual hardware — the Hearthmind experiment at [solystopia.tech/hearthmind](https://solystopia.tech/hearthmind).

| Stage | Planned content |
|---|---|
| 02 — Interface | Local chat UI (Open WebUI), terminal shortcuts, household device integration |
| 03 — Memory | SQLite conversation history, personal knowledge base, on-machine RAG |
| 04 — Tools & actions | Tool-use patterns, safe action gating, file/calendar/home automation hooks |
| 05 — Orchestration | Multi-model routing, specialised sub-agents, task queuing |
| 06 — Data ingestion | Document processing, OCR, household archive indexing |
| 07 — Voice | STT (Whisper) + TTS (Piper) — the full offline voice loop |
| 08 — Energy coupling | Compute scheduling against solar generation, power monitoring |
| 09 — Community | Shared models, federated knowledge, ward-scale AI utilities |

---

## Integration with other Solystopia tools

| Tool | Where it fits in the harness |
|---|---|
| [piper-local-tts-demo](https://github.com/arthursilas-ai/piper-local-tts-demo) | Stage 07 — Voice (TTS half) |
| [agent-preflight](https://github.com/arthursilas-ai/agent-preflight) | Stage 04 — before deploying any tool-using agent |
| [hearthmind-economics](https://github.com/arthursilas-ai/hearthmind-economics) | Before Stage 00 — sizing the hardware purchase |
| [household-capability-audit](https://github.com/arthursilas-ai/household-capability-audit) | Before Stage 00 — mapping where you're currently exposed |

---

## Hardware reference

| Device | RAM | Recommended stage | Notes |
|---|---|---|---|
| Raspberry Pi 4 | 8GB | Stage 00–01 only | Phi-3.5-mini at ~3 t/s; useful for light tasks |
| Any laptop ≥2018 | 16GB | Stage 00–04 | Good daily driver for 7B models |
| Mac Mini M2 | 16–24GB | Stage 00–06 | Excellent performance, ~30 t/s on 7B |
| NVIDIA DGX Spark | 128GB | Full harness | All stages; 70B+ models locally |

---

## The principle

> Understand a system before you abstract it.

Each stage is a real building block, not a marketing category. Two are live and working today, the rest are named honestly as planned. No stage is documented until it has been built and tested.

This is not a product. It is a working notebook.

---

## Contributing

If you've built and tested one of the planned stages, open a PR. The format is: one `stages/NN-name.md` file with working commands, hardware notes, and what surprised you.

---

## License

MIT.

---

*Part of the Solystopia open-source toolkit — [solystopia.tech](https://solystopia.tech)*
