
<p align="center">
  <img src="https://github.com/meta-llama/llama3/blob/main/Llama3_Repo.jpeg" width="400"/>
</p>

<p align="center">
  🤗 <a href="https://huggingface.co/meta-llama">Models on Hugging Face</a> &nbsp;|&nbsp;
  <a href="https://www.llama.com/docs/overview/">Docs</a> &nbsp;|&nbsp;
  <a href="https://ai.meta.com/blog/">Blog</a> &nbsp;|&nbsp;
  <a href="https://llama.meta.com/">Website</a> &nbsp;|&nbsp;
  <a href="https://llama.meta.com/get-started/">Get Started</a> &nbsp;|&nbsp;
  <a href="https://github.com/llamastack/llama-stack">Llama Stack</a>
</p>

---

## ⚠️ Repository status: Deprecated / archived

This repository is kept as a **historical, minimal reference** for running the original Meta Llama 3 (8B / 70B) native checkpoints.

**New model releases, tooling, and end-to-end examples for the Llama 3 family (and newer) have moved to the repos below.** Please use these going forward:

- **llama-models** — model cards, licenses + acceptable use policies, utilities, and the recommended `llama-model` CLI for downloading and verifying weights  
  https://github.com/meta-llama/llama-models
- **Llama Stack** — unified APIs + CLI/SDKs and verified distributions for inference, RAG, agents, tools, safety, evals  
  https://github.com/llamastack/llama-stack
- **Llama Stack Apps** — example applications and agentic components built on top of Llama Stack  
  https://github.com/llamastack/llama-stack-apps
- **PurpleLlama** — safety tooling and evaluations (e.g., Llama Guard, safeguards, mitigations)  
  https://github.com/meta-llama/PurpleLlama
- **llama-agentic-system** — a standalone, opinionated agentic system (useful reference; many examples now live under Llama Stack Apps)  
  https://github.com/meta-llama/llama-agentic-system
- **llama-cookbook** — community + official recipes for inference, fine-tuning, RAG, and integrations  
  https://github.com/meta-llama/llama-cookbook

If you have questions, please open issues in the most relevant repo above.

---

## What’s in this repo?

A lightweight Python reference implementation for:
- loading **Llama 3** native checkpoints
- running local inference (text completion + chat completion examples)

For the most up-to-date workflows (downloads, prompt formats, tool-calling, agents, safety, multi-provider deployments), use **llama-models** + **Llama Stack**.

---

## Recommended (current) workflow

### 1) Download model weights (Meta distribution) with `llama-models`

```bash
pip install llama-models
llama-model list
llama-model download --source meta --model-id CHOSEN_MODEL_ID
````

You’ll be prompted for the signed URL from the Meta email flow (links expire; you can re-request if needed).

### 2) Build apps with unified APIs using Llama Stack

```bash
curl -LsSf https://github.com/llamastack/llama-stack/raw/main/scripts/install.sh | bash
```

Then follow the Llama Stack docs to choose a distribution (local or hosted providers) and run inference/RAG/agents consistently.

---

## Legacy: Using this repo directly (native checkpoints)

> This section is provided for backwards compatibility. For new projects, prefer the workflow above.

### Install

```bash
pip install -e .
```

### Download (legacy)

To download the model weights and tokenizer, visit the Meta Llama website and accept the license:
[https://llama.meta.com/llama-downloads/](https://llama.meta.com/llama-downloads/)

Once approved, you’ll receive a signed URL over email. Run:

```bash
chmod +x download.sh
./download.sh
```

Pre-reqs: `wget` and `md5sum`.

### Quick start (chat)

```bash
torchrun --nproc_per_node 1 example_chat_completion.py \
  --ckpt_dir Meta-Llama-3-8B-Instruct/ \
  --tokenizer_path Meta-Llama-3-8B-Instruct/tokenizer.model \
  --max_seq_len 512 --max_batch_size 6
```

Notes:

* Replace `--ckpt_dir` and `--tokenizer_path` with your local paths.
* Tune `--max_seq_len` and `--max_batch_size` to fit your hardware.

---

## Inference

Different models require different model-parallel (MP) values:

| Model | MP |
| ----: | -: |
|    8B |  1 |
|   70B |  8 |

> Models support long context, but KV-cache is preallocated based on `max_seq_len` and `max_batch_size`.

---

## Hugging Face access

Llama models are also available on Hugging Face (Transformers and “original” weights):
[https://huggingface.co/meta-llama](https://huggingface.co/meta-llama)

Example (download original weights):

```bash
pip install -U huggingface-hub
huggingface-cli download meta-llama/Meta-Llama-3-8B-Instruct \
  --include "original/*" \
  --local-dir meta-llama/Meta-Llama-3-8B-Instruct
```

Example (Transformers inference):

```python
import torch
from transformers import AutoTokenizer, AutoModelForCausalLM

model_id = "meta-llama/Meta-Llama-3-8B-Instruct"

tokenizer = AutoTokenizer.from_pretrained(model_id)
model = AutoModelForCausalLM.from_pretrained(
    model_id,
    torch_dtype=torch.bfloat16,
    device_map="auto",
)

messages = [{"role": "user", "content": "Write a haiku about gradient descent."}]
inputs = tokenizer.apply_chat_template(messages, return_tensors="pt").to(model.device)
outputs = model.generate(inputs, max_new_tokens=128)
print(tokenizer.decode(outputs[0], skip_special_tokens=True))
```

---

## Responsible use & safety

Llama models can generate harmful or misleading content in some scenarios. For system-level safety, incorporate safeguards such as **PurpleLlama / Llama Guard** and follow the official responsible-use guidance.

---

## Issues

Because this repo is deprecated, please file issues in the most relevant maintained repo:

* Models / download tooling / licenses: [https://github.com/meta-llama/llama-models/issues](https://github.com/meta-llama/llama-models/issues)
* Llama Stack APIs / CLI / distributions: [https://github.com/llamastack/llama-stack/issues](https://github.com/llamastack/llama-stack/issues)
* Safety tooling and evals: [https://github.com/meta-llama/PurpleLlama/issues](https://github.com/meta-llama/PurpleLlama/issues)
* Recipes / integrations: [https://github.com/meta-llama/llama-cookbook/issues](https://github.com/meta-llama/llama-cookbook/issues)

---

## Model card

See `MODEL_CARD.md`.

## License

See `LICENSE` and `USE_POLICY.md` (and, for the latest policies across Llama versions, refer to `llama-models`).

## Questions

For FAQs and updated documentation, see:
[https://www.llama.com/faq/](https://www.llama.com/faq/)
