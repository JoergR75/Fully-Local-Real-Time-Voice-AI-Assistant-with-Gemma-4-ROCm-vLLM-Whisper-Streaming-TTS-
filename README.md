# 🤖 Fully Local Real-Time Voice AI Assistant with Gemma 4, ROCm, vLLM, Whisper Streaming, and Piper-TTS

A fully offline, GPU-accelerated AI voice assistant that streams Gemma 4 LLM responses in real time with speech input and speech synthesis on AMD ROCm hardware.

---

# 🚀 Overview

This project provides a fully local, GPU-accelerated AI voice assistant optimized for AMD ROCm platforms, including Radeon AI PRO and Ryzen AI systems.

The assistant combines:

* **vLLM** for high-performance real-time LLM inference
* **Whisper** for speech-to-text transcription
* **Piper-TTS** for fully offline voice synthesis
* **Gradio** for a browser-based interactive UI
* **Gemma 4** LoRA models optimized for vLLM

All responses are streamed token-by-token with low latency, while generated speech is automatically synthesized and played back locally after completion.

The entire pipeline runs fully offline without cloud services, external APIs, or internet connectivity after model installation.

---

# 🧠 Features

* Real-time streaming LLM responses
* Voice input via microphone using Whisper
* AI voice playback using Piper-TTS
* Fully local GPU acceleration with AMD ROCm
* Low-latency inference with vLLM
* Persistent in-session chat history
* Customizable assistant personality via system prompts
* Browser-based Gradio interface
* Optimized for Radeon AI PRO and Ryzen AI platforms

---

# 🏗 Architecture

## Pipeline Flow

```text id="1wtqqd"
Microphone
   ↓
Whisper STT
   ↓
Gemma 4 + vLLM Streaming Inference
   ↓
Piper-TTS
   ↓
Audio Playback
```

---

# 🔧 Core Components

| Component              | Purpose                                      |
| ---------------------- | -------------------------------------------- |
| vLLM                   | High-speed LLM inference and token streaming |
| Hugging Face Tokenizer | Chat template formatting                     |
| Whisper                | Speech-to-text transcription                 |
| Piper-TTS              | Fully offline text-to-speech synthesis       |
| Gradio                 | Browser-based UI and interaction layer       |
| ROCm                   | GPU acceleration on AMD hardware             |

---

# 🖥 Supported Platforms

## Operating Systems

* Ubuntu 22.04 LTS
* Ubuntu 24.04 LTS

## Supported Hardware

* AMD Radeon™ AI PRO R9700
* AMD Radeon™ AI PRO R9600
* AMD Ryzen™ AI MAX 390
* AMD Instinct™ GPUs
* RDNA3 / RDNA4 GPUs
* CDNA2 / CDNA3 / CDNA4 GPUs

## Software Stack

* ROCm 7.2+
* Python 3.10+
* PyTorch ROCm builds
* vLLM 0.20+
* Gradio
* faster-whisper
* Piper-TTS

---

# 🚀 Select Your AMD AI Platform

Choose your installation path below:

| Platform | Description | Installation |
|---|---|---|
| 🟥 **AMD Radeon™ AI PRO R9700** | Dedicated workstation GPU with high VRAM capacity | [Go to Installation](#-radeon-ai-pro-r9700-installation) |
| 🟦 **AMD Ryzen™ AI MAX 390** | Unified memory AI APU platform optimized for efficiency | [Go to Installation](#-ryzen-ai-max-390-installation) |

---

# 🟥 Radeon™ AI PRO R9700 Installation

## Recommended Environment
- Ubuntu 22.04 / 24.04
- ROCm 7.2+
- AMD Radeon AI PRO R9700/R9600D
- vLLM >= 0.20

### 1️⃣ **System preperation**
Install the latest **RDNA4** architecture docker vLLM container from open.ai
```bash
docker pull vllm/vllm-openai-rocm:v0.20.1
```

### 2️⃣ **Start the vLLM container**
```bash
sudo docker run -it \
    -p 7860:7860 \
    --device=/dev/kfd \
    --device=/dev/dri \
    --security-opt seccomp=unconfined \
    --group-add video \
    --entrypoint /bin/bash \
    vllm/vllm-openai-rocm:v0.20.1
```

| Flag / Option | Purpose |
|---------------|---------|
| `-p 7860:7860` | Exposes port 7860 (commonly used for web UIs or API endpoints) |
| `--device=/dev/kfd` | Grants access to the ROCm kernel driver (required for compute) |
| `--device=/dev/dri` | Passes the physical GPU device into the container (all visable GPU's) |
| `--security-opt seccomp=unconfined` | Required to avoid ROCm-related syscall restrictions |
| `--entrypoint /bin/bash` | Overrides the default startup command of the container and launches an interactive Bash shell instead |
| `--group-add video` | Ensures proper GPU access permissions inside the container |

rocm/vllm-openai:...
Uses the ROCm 7.2 vLLM development image with:
Ubuntu 22.04
Python 3.12
PyTorch 2.10
vLLM 0.20.1

Notes
Adjust /dev/dri/cardX and /dev/dri/renderDX if your GPU uses different device IDs (ls /dev/dri/ to verify).
Ensure Docker is installed and the ROCm driver is properly configured on the host system.
For production use, consider adding volume mounts for model storage and persistent data.


### 3️⃣ **Update and install** the container environment
```bash
sudo apt update
sudo apt install nano -y
sudo apt install ffmpeg -y
python3 -m pip install --upgrade pip wheel
python3 -m pip install gradio
python3 -m pip install faster-whisper
python3 -m pip install huggingface_hub[cli]
cd /app
wget https://github.com/rhasspy/piper/releases/latest/download/piper_linux_x86_64.tar.gz
tar -xzf piper_linux_x86_64.tar.gz
cd /app/piper
chmod +x piper
wget https://huggingface.co/rhasspy/piper-voices/resolve/main/en/en_US/lessac/medium/en_US-lessac-medium.onnx
wget https://huggingface.co/rhasspy/piper-voices/resolve/main/en/en_US/lessac/medium/en_US-lessac-medium.onnx.json
wget --content-disposition \
"https://huggingface.co/rhasspy/piper-voices/resolve/v1.0.0/en/en_US/amy/medium/en_US-amy-medium.onnx?download=true"
wget --content-disposition \
"https://huggingface.co/rhasspy/piper-voices/resolve/v1.0.0/en/en_US/amy/medium/en_US-amy-medium.onnx.json?download=true"
wget https://huggingface.co/rhasspy/piper-voices/resolve/main/en/en_US/libritts/high/en_US-libritts-high.onnx -O en_US-libritts-high.onnx
wget https://huggingface.co/rhasspy/piper-voices/resolve/main/en/en_US/libritts/high/en_US-libritts-high.onnx.json -O en_US-libritts-high.onnx.json
cd ..
```

### 4️⃣ **Download** the model - **Phu-Hien/gemma_4_dkkd_lora_vllm**
```bash
MODEL_DIR=/app/models/gemma_4_dkkd_lora_vllm
mkdir -p $MODEL_DIR
hf download Phu-Hien/gemma_4_dkkd_lora_vllm --local-dir $MODEL_DIR
```
This may take 10–15 minutes, depending on your internet connection.

### 5️⃣ **Download** the Chat Agent script
```bash
wget https://raw.githubusercontent.com/JoergR75/Fully-Local-Real-Time-Voice-AI-Assistant-with-Gemma-4-ROCm-vLLM-Whisper-Streaming-TTS-/refs/heads/main/chat_agent_stream_piper_vllm_radeon_ai_pro_gemma4.py
```

### 6️⃣ **Run** the Chat Agent
```bash
python3 chat_agent_stream_piper_vllm_radeon_ai_pro_gemma4.py
```
Starting the Gemma 4 model, Piper-TTS, and Whisper for the first time will download their weights. 

<img width="2186" height="2695" alt="image" src="https://github.com/user-attachments/assets/a2cf43e6-83db-4390-b63a-df9464215dd0" />

### 7️⃣ Launch the Gradio web Agent from another device connected to same network
First, SSH into the web server and forward port **7860**:
```echo
ssh -L 7860:0.0.0.0:7860 ai1@pc1
```
or use the the server IP address
```echo
ssh -L 7860:0.0.0.0:7860 ai1@192.168.178.xxx
```
Now you can open **http://localhost:7860** in your local browser to access the Gradio Web Agent.

<img width="1793" height="2072" alt="image" src="https://github.com/user-attachments/assets/b1efcc92-b5bb-4077-8ad1-ebb4a0faa033" />

---

# 🟦 Ryzen™ AI MAX 390 Installation

## Recommended Environment

* Ubuntu 22.04 / 24.04
* Ryzen AI MAX 390
* ROCm 7.2+
* Shared memory optimized setup



