# SpacemiT AI SDK

English | [简体中文](README_zh.md)

SpacemiT AI SDK brings together AI components adapted for SpacemiT K-series processors. This repository organizes the components as **Git submodules**, so they can be fetched as needed, developed independently, and integrated into applications.

## 1. Overview

This repository includes the following components as submodules:

- **Computer vision (`vision/`)**: detection, classification, segmentation, tracking, face recognition, pose estimation, and more, with unified C++/Python interfaces and extensive examples
- **Speech**:
  - **`vad/`**: voice activity detection (VAD)
  - **`asr/`**: automatic speech recognition (ASR)
  - **`tts/`**: text-to-speech synthesis (TTS)
- **Natural language (`llm/`)**: integration with OpenAI-compatible services such as `llama-server`, with examples such as `llm_chat` for quick evaluation and integration
- **Multimodal (`vlm/`)**: vision-language model capabilities

> Note: See the `examples/` directory and README in each component's repository for component-specific examples.

The component directories are located at the root of this repository:

- **`vision/`**: computer vision SDK and `examples/`
- **`asr/`**: speech recognition SDK with a unified API and examples
- **`tts/`**: speech synthesis SDK with a unified API and examples
- **`vad/`**: voice activity detection SDK with a unified API and examples
- **`llm/`**: large language model SDK with OpenAI-compatible service integration and examples
- **`vlm/`**: vision-language model SDK with C++/Python HTTP interfaces and Gateway integration

Public headers and API entry points are listed below. Refer to each component's README for authoritative details:

- **Vision**: `vision/include/vision_service.h`
- **ASR**: `asr/include/asr_service.h`
- **TTS**: `tts/include/tts_service.h`
- **VAD**: `vad/include/vad_service.h`
- **LLM**: `llm/include/llm_service.h`
- **VLM**: `vlm/include/vlm/vlm_service.h` (C++ API), `vlm/src/python/vlm.py` (Python HTTP API)

## 2. Building

### 2.1 Get the Source Code

This repository uses Git submodules to manage component source code. A recursive clone is recommended:

```bash
git clone --recurse-submodules https://github.com/spacemit-com/ai-sdk.git
# Or use SSH:
#git clone --recurse-submodules git@github.com:spacemit-com/ai-sdk.git
```

If you have already cloned the main repository without its submodules:

```bash
cd ai-sdk
git submodule update --init --recursive
```

### 2.2 Build All Components

The top-level repository brings together the component sources as submodules:

```bash
# Set up the build environment
source build/envsetup.sh

# Build all components
# The first build may take longer because component dependencies are installed automatically.
m
```

To **build a single component**, enter its directory and run `mm`. You must first run `source build/envsetup.sh`:

```bash
source build/envsetup.sh

cd asr && mm
# Or:
cd vision && mm
cd tts && mm
cd vad && mm
cd llm && mm
```

Build artifacts are typically installed to `output/staging`; refer to the SDK build configuration for the exact location.

## 3. Running Examples

> Note: Models and assets downloaded by the examples are cached under the user's home directory. Common defaults are `~/.cache/models/` and `~/.cache/assets/`; see each component's README for details.

### 3.1 Vision

**Step 1: Download models**

```bash
# Download all Vision example models to ~/.cache/models/vision/
bash vision/scripts/download_all_models.sh
```

**Step 2: Download assets (images and videos)**

```bash
# Download example images and videos to ~/.cache/assets/
bash vision/scripts/download_assets.sh
```

Default directories:

- Models: `~/.cache/models/vision/`
- Assets: `~/.cache/assets/`

**Step 3: Run examples**

The following commands run common examples from `vision/examples/`. The executable names depend on what is installed on your `PATH`. See the README in each example directory for additional options.

```bash
# Face recognition (similarity)
arcface vision/examples/arcface/config/arcface.yaml

# Face detection
yolov5-face vision/examples/yolov5-face/config/yolov5-face.yaml

# Gesture detection
yolov5_gesture vision/examples/yolov5_gesture/config/yolov5_gesture.yaml

# Object detection
yolov8 vision/examples/yolov8/config/yolov8.yaml
yolov11 vision/examples/yolov11/config/yolov11.yaml

# Pose estimation
yolov8_pose vision/examples/yolov8_pose/config/yolov8_pose.yaml

# Instance segmentation
yolov8_seg vision/examples/yolov8_seg/config/yolov8_seg.yaml

# Image classification / emotion recognition
resnet vision/examples/resnet/config/resnet50.yaml
emotion vision/examples/emotion/config/emotion.yaml

# Multi-object tracking (video/camera)
# These two examples display tracking results in real time and require a connected display.
# They will fail if no display is connected.
bytetrack vision/examples/bytetrack/config/bytetrack.yaml
ocsort vision/examples/ocsort/config/ocsort.yaml
```

### 3.2 ASR

**Step 1: Download sample audio**

```bash
mkdir -p ~/.cache/models/assets/audio
cd ~/.cache/models/assets/audio
wget https://archive.spacemit.com/spacemit-ai/model_zoo/assets/audio/001_zh_daily_weather.wav
```

Additional samples are available in the [audio assets directory](https://archive.spacemit.com/spacemit-ai/model_zoo/assets/audio).

**Step 2: Run examples**

```bash
# Transcribe a WAV file
asr_file_demo ~/.cache/models/assets/audio/001_zh_daily_weather.wav

# Streaming recognition (requires an external audio input device)
asr_stream_demo -l # List audio device IDs
asr_stream_demo -i 0 -t 5 # -i specifies the input audio device ID
```

### 3.3 TTS

**Run examples**

```bash
# Synthesize speech with default parameters
tts_file_demo

# Specify the text and backend (Chinese text for the Chinese backend)
tts_file_demo -p "你好世界" -l matcha:zh

# Streaming synthesis (requires an external audio output device)
tts_stream_demo -l # List audio device IDs
tts_stream_demo -o 0 --output 48000 --channels 2 -e matcha:zh-en # -o specifies the output audio device ID
```

### 3.4 VAD

**Run the example**

```bash
# Test with built-in simulated audio
vad_simple_demo
```

### 3.5 LLM

**Step 1: Download a model (GGUF example)**

```bash
mkdir -p ~/.cache/models/llm
cd ~/.cache/models/llm
wget https://archive.spacemit.com/spacemit-ai/model_zoo/llm/qwen2.5-0.5b-instruct-q4_0.gguf
```

To try other models, browse the [LLM model directory](https://archive.spacemit.com/spacemit-ai/model_zoo/llm).

**Step 2: Start an OpenAI-compatible service and run the example**

```bash
# Start the server on port 8080
llama-server -m ~/.cache/models/llm/qwen2.5-0.5b-instruct-q4_0.gguf -t 8 --port 8080 &

# Run the SDK example
llm_chat "Hello" "http://localhost:8080/v1" "qwen2.5-0.5b" "You are a helpful assistant." 256
```

For a cloud or remote OpenAI-compatible service, replace the second argument with the service's `api_base` and provide the API key through an environment variable:

```bash
export OPENAI_API_KEY=your_api_key
llm_chat "Hello" "https://api.deepseek.com" "deepseek-chat" "You are a helpful assistant." 256
```

### 3.6 VLM

**Step 1: Install dependencies and download a model**

```bash
sudo apt-get update
sudo apt-get install -y build-essential cmake curl libyaml-cpp-dev nlohmann-json3-dev
sudo apt install llama.cpp-tools-spacemit

cd vlm
bash scripts/download_model.sh fastvlm-mm-0.5b-q4_1
```

Available models (validated with `--vision-backend smt` and ONNX vision models):

| Model ID | Parameters | Disk Size | Inference Speed |
|----------|------------|-----------|-----------------|
| `fastvlm-mm-0.5b-q4_1` | 0.5B | ~766M | ~58 tok/s |
| `Qwen3.5-0.8B` | 0.8B | ~932M | ~40 tok/s |
| `Qwen3.5-2B` | 2B | ~2.6G | ~26 tok/s |
| `Qwen3.5-4B` | 4B | ~3.9G | ~13 tok/s |
| `qwen30ba3b-mm-q4_1` | 30B (MoE) | ~17.6G | ~18 tok/s |

Models are downloaded to `~/.cache/models/vlm/<model_name>/` by default. Set `VLM_CACHE_DIR=/data/models` to use a different directory.

**Step 2: Build the VLM SDK**

```bash
cd vlm
mkdir -p build && cd build
cmake .. -DCMAKE_BUILD_TYPE=Release
cmake --build . -j$(nproc)
ctest --output-on-failure
```

**Step 3: Run the C++ example**

```bash
cd vlm/build
./fastvlm_demo \
  --config ../examples/fastvlm/config/fastvlm.yaml \
  --image /path/to/image.jpg \
  --prompt "Describe this image in one sentence"
```

**Step 4: Run the Python example**

The Python interface is a pure HTTP client that communicates with `llama-server` through an OpenAI-compatible API. It does not require a C ABI dependency.

```bash
cd vlm
PYTHONPATH=$PWD/src/python:$PYTHONPATH \
  python3 examples/fastvlm/python/vlm_demo.py \
  --config examples/fastvlm/config/fastvlm.yaml \
  --image /path/to/image.jpg \
  --prompt "Describe this image"
```

**Step 5: Use the Gateway**

The Gateway includes a VLM domain that supports both local `llama-server` instances and remote proxy mode. Its endpoints are under `/v1/vlm/*`, including an OpenAI-compatible `/v1/chat/completions` route.

Start the Gateway:

```bash
cd gateway
PYTHONPATH=src python3 -m uvicorn spacemit_ai_gateway.app.main:app \
  --host 0.0.0.0 --port 18790
```

Register and load a model, using `local_path` to specify its directory:

```bash
# Register the model
curl -X POST http://127.0.0.1:18790/v1/vlm/models/register \
  -H 'Content-Type: application/json' \
  -d '{"model":"fastvlm-mm-0.5b-q4_1","source_type":"local_path","local_path":"~/.cache/models/vlm/fastvlm-mm-0.5b-q4_1"}'

# Load the model
curl -X POST http://127.0.0.1:18790/v1/vlm/models/load \
  -H 'Content-Type: application/json' \
  -d '{"model":"fastvlm-mm-0.5b-q4_1"}'
```

Run text inference:

```bash
curl -X POST http://127.0.0.1:18790/v1/vlm/chat/completions \
  -H 'Content-Type: application/json' \
  -d '{"model":"fastvlm-mm-0.5b-q4_1","messages":[{"role":"user","content":"What is 2+2?"}],"max_tokens":50}'
```

Run image understanding:

```bash
curl -X POST http://127.0.0.1:18790/v1/vlm/chat/completions \
  -H 'Content-Type: application/json' \
  -d '{"model":"fastvlm-mm-0.5b-q4_1","messages":[{"role":"user","content":"Describe this image","image_path":"/path/to/image.jpg"}]}'
```

Unload the model:

```bash
curl -X POST http://127.0.0.1:18790/v1/vlm/models/unload \
  -H 'Content-Type: application/json' \
  -d '{"model":"fastvlm-mm-0.5b-q4_1"}'
```

## 4. Documentation

For more information, see the [SpacemiT AI SDK documentation (Chinese)](https://www.spacemit.com/community/document/info?lang=zh&nodepath=ai/application_tools/ai-sdk.md).
