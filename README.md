# SpringSummit2026_Ollama

Hands-on local AI demo project using Ollama for text and image-assisted operations workflows.

This project is designed for citizen developers and DevOps engineers who want to:
- Build a custom local model persona with a `Modelfile`.
- Run repeatable chat workflows against the local Ollama API.
- Interpret pasted logs into structured operational insights.
- Analyze images and request machine-readable output with latency-aware settings.

## Project Goals

- Keep all inference local for privacy and offline-friendly demos.
- Show practical API usage patterns with clear error handling.
- Demonstrate reliability and performance tuning for real operational tasks.

## Repository Structure

```text
SpringSummit26/
	Modelfile
	README.md
	bgp_training_data[86].jsonl
	llamaDemoPic.webp
	Notebooks/
		Demo_Spring2026.ipynb
		Modelfile
```

## Prerequisites

Install and verify the following:

1. Python 3.12+
2. Jupyter support in VS Code (or Jupyter Lab)
3. Ollama installed and running locally
4. Base model available in Ollama: `llama3.2-vision:latest`

Recommended checks:

```bash
python --version
ollama --version
ollama list
```

If needed, pull the base model:

```bash
ollama pull llama3.2-vision:latest
```

## Quick Start

1. Open `Notebooks/Demo_Spring2026.ipynb`.
2. Run cells in order from top to bottom.
3. Watch for output at each step before proceeding.

The notebook flow covers:
- Creating a model definition (`Modelfile`).
- Building a custom local model tag (`netops-llama3.2-vision`).
- Verifying runtime and Ollama availability.
- Sending text chat requests to `/api/chat`.
- Interpreting pasted log snippets into concise JSON-style findings.
- Sending image + text prompts to the same model with optional latency profiles.

## Core Concepts Used in the Notebook

- `HOST`: Base URL for Ollama API, usually `http://localhost:11434`.
- `MODEL_NAME`: Local model tag used for requests.
- `normalize_host()`: Prevents URL issues caused by trailing slashes.
- `messages`: Role-based conversation payload sent to Ollama chat API.
- `options`: Runtime generation controls (for example `num_predict`, `temperature`).
- `option_profile`: Named preset to balance response quality and latency.

## Latency Profiles for Image Inference

The notebook defines `OPTION_PROFILES` in the image helper cell:

- `fast`: Lower latency, shorter output.
- `balanced`: Default compromise between speed and quality.
- `quality`: Richer output with higher latency.

Example usage:

```python
chat_with_image(
		"./llamaDemoPic.webp",
		"Return JSON only with fields: gpu_number, temperature_c, power_w, bus_id.",
		option_profile="fast",
		timeout_seconds=420,
		retries=1,
)
```

## Troubleshooting

### 1) HTTP 405 from `/api/chat`

Likely cause: malformed URL due to trailing slash handling.

Fix:
- Ensure host is normalized before endpoint append:
	- `http://localhost:11434` (no trailing slash)
	- use `host.rstrip("/")` in helper methods.

### 2) Timeout on image calls

Likely cause: model inference takes longer for multimodal requests.

Fixes:
- Increase `timeout_seconds`.
- Use `option_profile="fast"`.
- Reduce `num_predict` via `options={...}` override.
- Keep `retries` enabled for transient failures.

### 3) Model not found

Likely cause: custom model was not created yet.

Fix:
- Re-run build cells.
- Confirm with `ollama list`.

### 4) Ollama unreachable

Fix:
- Start Ollama.
- Re-run the notebook health-check cell.

## Suggested Run Order

1. Create and write `Modelfile`.
2. Build local model with `ollama create`.
3. Check Python and platform.
4. Confirm Ollama service is up.
5. List local models.
6. Run text chat example.
7. Run log interpretation example.
8. Run image analysis example.

## Audience Notes

For citizen developers:
- Use the notebook as a guided workflow and run cells sequentially.
- Start with `fast` profile for responsive results.

For DevOps engineers:
- Reuse helper functions for local runbooks and diagnostics.
- Prefer structured outputs and profile-based tuning for repeatability.

## Next Improvements (Optional)

- Add strict JSON schema validation for model responses.
- Export notebook helpers into a reusable Python module.
- Add automated smoke tests for Ollama endpoint availability and model tagging.
