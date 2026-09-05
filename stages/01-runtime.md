# Stage 01 — Runtime

The runner that loads the model and lets it think — llama.cpp: no account, no hosted dashboard, no hidden subscription.

## Install llama.cpp

### macOS (Homebrew — easiest)
```bash
brew install llama.cpp
```

### Linux / from source
```bash
git clone https://github.com/ggerganov/llama.cpp
cd llama.cpp

# CPU only
make -j$(nproc)

# NVIDIA GPU (CUDA)
make -j$(nproc) GGML_CUDA=1

# Apple Silicon (Metal)
make -j$(nproc) GGML_METAL=1
```

### Windows
Use [LM Studio](https://lmstudio.ai) as the runtime — it bundles llama.cpp with a GUI.
Or use WSL2 and the Linux build instructions.

## First run — interactive chat

```bash
llama-cli \
  -m models/llama-3.2-3b-instruct.Q4_K_M.gguf \
  --chat-template llama3 \
  -ngl 99 \
  -c 4096 \
  -p "You are a helpful household assistant."
```

Flags:
- `-ngl 99` — offload all layers to GPU (set to 0 for CPU-only)
- `-c 4096` — context window in tokens
- `--chat-template llama3` — use the model's instruction format

## One-shot inference (pipe-friendly)

```bash
echo "Summarise this in one sentence: the sky is blue because of Rayleigh scattering." | \
  llama-cli -m models/llama-3.2-3b-instruct.Q4_K_M.gguf \
  --chat-template llama3 -ngl 99 -no-cnv 2>/dev/null
```

`-no-cnv` disables the multi-turn format for single-turn use.

## API server (OpenAI-compatible)

Exposes the model as a local HTTP API — useful for integrations, Home Assistant, or any tool that speaks OpenAI's protocol.

```bash
llama-server \
  -m models/llama-3.2-3b-instruct.Q4_K_M.gguf \
  --host 0.0.0.0 --port 8080 \
  -ngl 99 \
  -c 4096
```

Test it:
```bash
curl http://localhost:8080/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "local",
    "messages": [{"role": "user", "content": "What time is it good to run heavy workloads?"}]
  }'
```

## Benchmark your hardware

Record this. You'll use it in Stage 08 to plan energy-coupled scheduling.

```bash
llama-bench \
  -m models/llama-3.2-3b-instruct.Q4_K_M.gguf \
  -n 128 -ngl 99
```

What to record:
- `pp` — prompt processing speed (tokens/sec, input)
- `tg` — text generation speed (tokens/sec, output)
- Power draw during the benchmark (use a smart plug)

Example results for reference:
| Hardware | Model | tg (t/s) | Power |
|---|---|---|---|
| Raspberry Pi 4 8GB | Phi-3.5-mini Q4_K_M | ~3 | ~8W |
| MacBook Air M2 | Llama-3.2-3B Q4_K_M | ~45 | ~15W |
| Mac Mini M4 | Qwen2.5-7B Q4_K_M | ~55 | ~25W |
| DGX Spark | Llama-3.1-70B Q4_K_M | ~35 | ~170W |

## Configs

See `configs/` for ready-to-use llama-server config files for common setups.

## What to do with a working runtime

Once you get a response, you have Stage 01 complete. The model is running on your hardware. No request left the building.

Document:
- Which model, which quantisation
- Your t/s at generation
- Power draw under load (estimated or measured)
- Context window you set

This is your baseline. Everything else builds on it.

---

*Next: [Stage 02 — Interface](02-interface.md) (planned)*
