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

* AMD Radeon AI PRO R9700
* AMD Radeon AI PRO R9600
* AMD Ryzen AI MAX+ 390
* AMD Instinct GPUs
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

# ⚡ Highlights

* 100% local execution
* No cloud dependencies
* Real-time token streaming
* Fully GPU accelerated
* Optimized for AMD ROCm AI workloads
* Designed for demos, AI agents, local copilots, and enterprise AI showcases


# 🚀 Select Your AMD AI Platform

Choose your installation path below:

| Platform | Description | Installation |
|---|---|---|
| 🟥 **Radeon AI PRO R9700** | Dedicated workstation GPU with high VRAM capacity | [Go to Installation](#-radeon-ai-pro-r9700-installation) |
| 🟦 **Ryzen AI MAX+ 390** | Unified memory AI APU platform optimized for efficiency | [Go to Installation](#-ryzen-ai-max-390-installation) |

---

# 🟥 Radeon AI PRO R9700 Installation

## Recommended Environment
- Ubuntu 24.04
- ROCm 6.4.2+
- Radeon AI PRO R9700
- vLLM >= 0.20

### Install ROCm
```bash
sudo apt update
sudo apt install rocm
````

### Verify GPU Detection

```bash
rocminfo
rocm-smi
```

### Create Python Environment

```bash
python3 -m venv gemma4
source gemma4/bin/activate
```

### Install PyTorch + vLLM

```bash
pip install torch torchvision torchaudio
pip install vllm>=0.20
```

### Launch Application

```bash
python app.py
```

---

# 🟦 Ryzen AI MAX+ 390 Installation

## Recommended Environment

* Ubuntu 24.04
* Ryzen AI MAX+ 390
* ROCm 6.4.2+
* Shared memory optimized setup

### Configure UMA Memory

Set in BIOS:

* UMA Frame Buffer Size → Highest available
* Enable iGPU shared memory optimization

### Install ROCm

```bash
sudo apt update
sudo apt install rocm
```

### Create Python Environment

```bash
python3 -m venv gemma4_apu
source gemma4_apu/bin/activate
```

### Install PyTorch + vLLM

```bash
pip install torch torchvision torchaudio
pip install vllm>=0.20
```

### Launch Application

```bash
python app.py
```

```






## ⚙️ Model Configuration
```python
MODEL_ID = "Phu-Hien/gemma_4_dkkd_lora_vllm"

SamplingParams(
    max_tokens=300,
        temperature=0.1,
        top_p=0.9,
        top_k=50,
)
```

- Mostly deterministic output
- Some small variety
- Output will not exceed 300 tokens

## 🖥 Hardware & Platform

Tested on:

- AMD Radeon™ AI PRO R9700/9600D 
- AMD Ryzen™ AI MAX 390 w/ Radeon 8050S (Strix Halo)
- ROCm 7.2.1
- Ubuntu 22.04 / 24.04
- PyTorch 2.10
- vLLM 0.20.1

Designed specifically for AMD GPU acceleration.

## 🎭 Personality System

- Ruby is configured via a structured system prompt:
- Sharp wit
- Dry humor
- Short and confident replies
- Helpful first, funny second
- Occasional references to running locally and speed
- No long explanations unless requested

## 🚀 Installation

### 1️⃣ **System preperation**
Install the latest **RDNA4** architecture docker vLLM container for Ubuntu 24.04
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
| `--device=/dev/dri` | Passes the physical GPU device into the container |
| `--security-opt seccomp=unconfined` | Required to avoid ROCm-related syscall restrictions |
| `--entrypoint /bin/bash` |  |
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

### 4️⃣ **Download** the model - here the Qwen3-4B-Instruct-2507
```bash
mkdir /app/models/qwen3-4b-instruct-2507
hf download Qwen/Qwen3-4B-Instruct-2507 --local-dir /app/models/qwen3-4b-instruct-2507
```
or the 8B Llama3.3-Instruct-Thinking-Heretic-Uncensored-Claude-4.5-Opus-High-Reasoning model
```bash
mkdir /app/models/llama3-3-8b-heretic
hf download DavidAU/Llama3.3-8B-Instruct-Thinking-Heretic-Uncensored-Claude-4.5-Opus-High-Reasoning --local-dir /app/models/llama3-3-8b-heretic
```

### 5️⃣ **Download** the Chat Agent script
for **Radeon AI PRO R9700/9600D**
```bash
wget https://raw.githubusercontent.com/JoergR75/Fully-Local-Real-Time-Voice-AI-Assistant-with-Gemma-4-ROCm-vLLM-Whisper-Streaming-TTS-/refs/heads/main/chat_agent_stream_piper_vllm_radeon_ai_pro_gemma4.py
```
for **Ryzen AI MAX 390**
```bash
wget https://raw.githubusercontent.com/JoergR75/Fully-Local-Real-Time-Voice-AI-Assistant-with-Gemma-4-ROCm-vLLM-Whisper-Streaming-TTS-/refs/heads/main/chat_agent_stream_piper_vllm_ryzen_ai_gemma4.py
```

### 6️⃣ **Run** the Chat Agent
For **Radeon AI PRO**
```bash
python3 chat_agent_stream_piper_vllm_radeon_ai_pro_gemma4.py
```
Starting the Gemma 4 model, Piper-TTS, and Whisper for the first time will download their weights. This may take 10–15 minutes, depending on your internet connection. 

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

<img width="1986" height="2316" alt="image" src="https://github.com/user-attachments/assets/101f2e80-6e95-489c-b610-833568006ed2" />

