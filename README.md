# brainy trainer

An interactive ML training studio for students. Train image, text, and classifier models through a browser UI — no code required.

## What it does

- **Brain** — train an image generator, text generator, or image classifier on your own examples
- **Behavior** — assemble a bot: pick inputs (camera, mic, button), connect a brain, add outputs (speaker, screen, LED), write rules
- **Deploy** — save your bot to the Library, or share it

## Stack

- Backend: Flask (Python 3.11), PyTorch, HuggingFace Transformers, sentence-transformers
- Frontend: single-page HTML/CSS/JS (no framework)
- Local LLM inference: Ollama (for Smart Prompt text mode)

## Setup

```bash
pip install flask torch torchvision transformers sentence-transformers pillow
python3 app.py
```

Then open http://localhost:5008

## Classroom Raspberry Pi deploy (no SSH per laptop)

Brainy sends a **`POST`** to **`{deploy_url}/deploy`**. Pick one Pi receiver below.

### A) Multipart ZIP + header token (typical classroom `deploy_api.py`)

Your Pi expects:

- **`POST /deploy`**
- Header **`X-Deploy-Token`**: shared secret (same hex string as `DEPLOY_TOKEN` in `deploy_api.py`)
- multipart form field **`file`** with the ZIP

This is Brainy’s **default** Classroom mode.

In Deploy → Download for Pi → Classroom mode:

- Deploy URL: **`http://<pi-ip>:5000`** (no trailing slash; `/deploy` is appended automatically)
- Token: **exact copy** of your Pi `DEPLOY_TOKEN`

### B) Legacy JSON receiver (`software/pi/classroom_deploy_service.py`)

Uses JSON + base64 ZIP in the POST body instead of multipart.

In Classroom mode, check **“Legacy JSON receiver…”** and use URL/port for that service (default **5010**).

### Pi paths (receiver B only)

Deployments land under `CLASSROOM_DEPLOY_ROOT`, with a **`latest`** pointer — see `software/pi/classroom_deploy_service.py`.

## Project structure

```
app.py                    # Flask server + all API routes
software/
  ai/
    classifier_trainer.py # Image/text/audio classifier (MobileNet / DistilBERT)
    image_trainer.py      # Variational autoencoder
    text_trainer.py       # Character-level LSTM
    finetune_trainer.py   # DistilGPT-2 fine-tuning
    smart_prompt_trainer.py # RAG pipeline via Ollama/LLaVA
  ui/
    templates/
      trainer.html        # Entire frontend (single file)
data/                     # Saved models and bots (gitignored)
```
