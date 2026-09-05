---
name: sovereign-ai-harness
description: A ten-stage guide for building a household or small-team AI stack that runs entirely on owned hardware, from picking a model file through to a full local intelligence stack. Use when someone wants to move off cloud AI APIs onto local/offline inference, needs to choose a GGUF model and runtime for their hardware, or is planning a local AI setup stage by stage.
---

# Sovereign AI Harness

Ten stages, from a model file on a shelf to a locally-owned intelligence
stack. Stages 00 (model selection) and 01 (runtime) are documented with
working commands today; 02–09 are named and planned, filled in as each is
actually built and tested on real hardware.

## How to use this

This is a reference guide, not a single script — read the stage that
matches where the person is:

| Stage | What it answers |
|---|---|
| 00 — Model | Which GGUF model file fits the hardware available? |
| 01 — Runtime | How do you actually load and run that model locally (llama.cpp)? |
| 02–09 | Planned: interface, memory, tools, orchestration, ingestion, voice, energy, community |

Start at [stages/00-model.md](stages/00-model.md) and
[stages/01-runtime.md](stages/01-runtime.md) for working commands and
hardware-sizing tables. [configs/](configs/) has ready-to-use
`llama-server` configs for a basic setup and for higher-end hardware.

## When to use it

- Someone wants off cloud AI APIs and onto hardware they own, and doesn't
  know where to start.
- Choosing a model size/quantization against known RAM and disk limits.
- Planning a local AI setup as a sequence of stages rather than one big
  leap — each stage is independently useful.
- Connecting the other Solystopia tools together: run
  `household-capability-audit` first to see where you're exposed, size
  the build with `hearthmind-economics`, then follow this harness to
  actually build it. `piper-local-tts-demo` covers stage 07 (voice) today.

## Install

No package to install — this is documentation and config, not a CLI.
Clone or browse the repo, or fetch a single stage:

```bash
curl -O https://raw.githubusercontent.com/arthursilas-ai/sovereign-ai-harness/main/stages/00-model.md
```

Or as an agent skill: `npx skills add arthursilas-ai/sovereign-ai-harness`
