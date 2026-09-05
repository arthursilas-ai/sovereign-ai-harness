# Stage 00 — Model

Pick a small, open-weight model you can hold in your hand: a GGUF file with a known provenance, not a hosted API key.

## What GGUF means

GGUF is the model format used by llama.cpp. The model is a single file — you download it, you own it. No account, no API key, no cloud dependency. The file contains the model weights in a quantised format: compressed to fit consumer hardware without destroying capability.

## Quantisation tiers

| Tier | Quality loss | Size | Use when |
|---|---|---|---|
| Q8_0 | ~0% | Large | You have plenty of RAM, want full quality |
| Q5_K_M | ~1% | Medium-large | Best quality-per-GB on most hardware |
| Q4_K_M | ~2–3% | Medium | **Recommended default** — excellent quality, broad hardware support |
| Q3_K_M | ~5–8% | Small | RAM-constrained (e.g. 4GB Raspberry Pi) |
| Q2_K | ~15%+ | Tiny | Only if nothing else fits |

The suffix `_K_M` denotes the K-quants method: better than older methods at the same bit depth. Prefer `_K_M` variants.

## Hardware sizing

| Available RAM | Model tier | Examples |
|---|---|---|
| 4GB | 3B Q4_K_M or smaller | Phi-3.5-mini, Llama-3.2-1B |
| 8GB | 7–8B Q4_K_M | Llama-3.1-8B, Qwen2.5-7B, Mistral-7B |
| 16GB | 13B Q4_K_M | Llama-2-13B, Mixtral (some layers) |
| 24GB | 20–27B Q4_K_M | Mistral-Small, Gemma-2-27B |
| 64GB+ | 70B Q4_K_M | Llama-3.1-70B, Qwen2.5-72B |
| 128GB (DGX Spark) | 70–130B Q8_0 | Full quality on very large models |

Leave 20% RAM headroom for the OS, context, and KV cache.

## Where to find models

- [HuggingFace GGUF search](https://huggingface.co/models?library=gguf)
- [Bartowski's collection](https://huggingface.co/bartowski) — well-tested, reliable quantisations
- [LM Studio model catalogue](https://lmstudio.ai/models) — curated, community-rated

## Download commands

```bash
mkdir -p models

# Llama 3.2 3B — fast, small, good for automation
curl -L -o models/llama-3.2-3b-instruct.Q4_K_M.gguf \
  "https://huggingface.co/bartowski/Llama-3.2-3B-Instruct-GGUF/resolve/main/Llama-3.2-3B-Instruct-Q4_K_M.gguf"

# Qwen 2.5 7B — better reasoning, still fast
curl -L -o models/qwen2.5-7b-instruct.Q4_K_M.gguf \
  "https://huggingface.co/Qwen/Qwen2.5-7B-Instruct-GGUF/resolve/main/qwen2.5-7b-instruct-q4_k_m.gguf"

# Llama 3.1 8B — solid general baseline
curl -L -o models/llama-3.1-8b-instruct.Q4_K_M.gguf \
  "https://huggingface.co/bartowski/Meta-Llama-3.1-8B-Instruct-GGUF/resolve/main/Meta-Llama-3.1-8B-Instruct-Q4_K_M.gguf"
```

## Verify the download

```bash
# Check file size is roughly expected
ls -lh models/

# Quick sanity check with llama.cpp
llama-cli -m models/llama-3.2-3b-instruct.Q4_K_M.gguf \
  -n 32 -p "The capital of France is" --log-disable 2>/dev/null
```

If it outputs something coherent, you have a working model.

## Provenance

Know where your model came from. Check:
- The HuggingFace model card for licensing (Meta's Llama licence vs Apache 2.0 vs MIT)
- Whether it's a base model, an instruction-tuned variant, or a fine-tune
- The quantisation author — bartowski, TheBloke, and mlx-community are well-known and reliable

You're not just running someone else's model. You're choosing what intelligence you're bringing into your home.

---

*Next: [Stage 01 — Runtime](01-runtime.md)*
