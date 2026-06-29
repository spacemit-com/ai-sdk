# SpacemiT AI SDK

SpacemiT AI SDK 是面向进迭时空 K 系列芯片适配的 AI 能力集合仓库。本仓库以 **Git Submodule** 的方式组织各组件代码，便于按需拉取、独立演进与集成。

## 1. 项目简介

本仓库包含（以子仓库形式）：

- **计算机视觉（`vision/`）**：检测/分类/分割/跟踪/人脸/姿态等，提供 C++/Python 统一接口与丰富示例
- **语音**：
  - **`vad/`**：语音活动检测（VAD）
  - **`asr/`**：语音识别（ASR）
  - **`tts/`**：语音合成（TTS）
- **自然语言（`llm/`）**：对接 OpenAI 兼容服务（如 `llama-server`），提供 `llm_chat` 等示例便于快速体验与集成
- **多模态（`vlm/`）**：视觉语言模型相关能力

> 说明：各组件示例请直接参考对应子仓库目录中的 `examples/` 与 README。

AI SDK 关键目录速览（本仓库根目录即组件目录）：

- **`vision/`**：计算机视觉 SDK + `examples/`
- **`asr/`**：语音识别 SDK（统一 API + 示例）
- **`tts/`**：语音合成 SDK（统一 API + 示例）
- **`vad/`**：语音活动检测 SDK（统一 API + 示例）
- **`llm/`**：大模型 SDK（OpenAI 兼容服务对接 + 示例）
- **`vlm/`**：视觉语言模型 SDK（C++/Python HTTP 接口 + Gateway 适配）

各组件对外头文件（C++ API 入口，最终以组件 README 为准）：

- **Vision**：`vision/include/vision_service.h`
- **ASR**：`asr/include/asr_service.h`
- **TTS**：`tts/include/tts_service.h`
- **VAD**：`vad/include/vad_service.h`
- **LLM**：`llm/include/llm_service.h`
- **VLM**：`vlm/include/vlm/vlm_service.h`（C++ API），`vlm/src/python/vlm.py`（Python HTTP API）

## 2. 构建编译

### 2.1 获取代码

本仓库使用 Git Submodule 管理各组件源码，推荐递归拉取：

```bash
git clone --recurse-submodules https://github.com/spacemit-com/ai-sdk.git
# 或 SSH
#git clone --recurse-submodules git@github.com:spacemit-com/ai-sdk.git
```

如果你已经 clone 了主仓库但没拉子仓库：

```bash
cd ai-sdk
git submodule update --init --recursive
```

### 2.2 一键编译

本仓库以 submodule 方式聚合各组件源码；

```bash
# source环境变量
source build/envsetup.sh

# 一键编译应用
# 第一次编译时间会长一点，编译会自动安装各组件依赖，请耐心等待
m
```

如果你只想在 SDK 中**单独编译某个组件**，可以进入对应目录执行 `mm`（同样需要先 `source build/envsetup.sh`）：

```bash
source build/envsetup.sh

cd asr && mm
# 或
cd vision && mm
cd tts && mm
cd vad && mm
cd llm && mm
```
构建产物通常会安装到 `output/staging`（以 SDK 工程为准）。


## 3. 示例运行

> 说明：示例下载的模型/资源会缓存到用户目录（以组件 README 为准），常见默认目录为 `~/.cache/models/` 与 `~/.cache/assets/`。

### 3.1 Vision

**步骤 1：下载模型**

```bash
# 下载所有 vision 示例模型（会放到 ~/.cache/models/vision/ 下面）
bash vision/scripts/download_all_models.sh
```

**步骤 2：下载资源文件（图片/视频）**

```bash
# 下载示例用图片/视频资源（会放到 ~/.cache/assets/ 下面）
bash vision/scripts/download_assets.sh
```

默认目录：

- 模型：`~/.cache/models/vision/`
- 资源：`~/.cache/assets/`

**步骤 3：运行示例**

以下为 `vision/examples/` 下常见示例的运行命令（以实际安装到 PATH 的可执行文件/脚本为准；更多参数请参考对应示例目录 README）：

```bash
# 人脸识别（相似度）
arcface vision/examples/arcface/config/arcface.yaml

# 人脸检测
yolov5-face vision/examples/yolov5-face/config/yolov5-face.yaml

# 手势检测
yolov5_gesture vision/examples/yolov5_gesture/config/yolov5_gesture.yaml

# 目标检测
yolov8 vision/examples/yolov8/config/yolov8.yaml
yolov11 vision/examples/yolov11/config/yolov11.yaml

# 姿态估计
yolov8_pose vision/examples/yolov8_pose/config/yolov8_pose.yaml

# 实例分割
yolov8_seg vision/examples/yolov8_seg/config/yolov8_seg.yaml

# 图像分类 / 情绪识别
resnet vision/examples/resnet/config/resnet50.yaml
emotion vision/examples/emotion/config/emotion.yaml

# 多目标跟踪（视频/摄像头）
# 以下两个示例是实时画面显示目标跟踪，需要接上屏幕，否则会报错
bytetrack vision/examples/bytetrack/config/bytetrack.yaml
ocsort vision/examples/ocsort/config/ocsort.yaml
```

### 3.2 ASR

**步骤 1：下载音频资源（示例音频）**

```bash
mkdir -p ~/.cache/models/assets/audio
cd ~/.cache/models/assets/audio
wget https://archive.spacemit.com/spacemit-ai/model_zoo/assets/audio/001_zh_daily_weather.wav
```

更多音频资源可在 [音频资源目录](https://archive.spacemit.com/spacemit-ai/model_zoo/assets/audio) 按需下载。

**步骤 2：运行示例**

```bash
# 识别 wav 文件（示例）
asr_file_demo ~/.cache/models/assets/audio/001_zh_daily_weather.wav

# 流式识别(示例，需要外接音频输入设备)
asr_stream_demo -l # 查看音频设备号
asr_stream_demo -i 0 -t 5 # 其中 -i 为输入音频设备号
```

### 3.3 TTS

**运行示例**

```bash
# 简单合成（默认参数）
tts_file_demo

# 指定文本与后端（示例）
tts_file_demo -p "你好世界" -l matcha:zh

# 流式合成（示例，需要外接音频输出设备）
tts_stream_demo -l # 查看音频设备号
tts_stream_demo -o 0 --output 48000 --channels 2 -e matcha:zh-en # -o 为音频输出设备号
```

### 3.4 VAD

**运行示例**

```bash
# 内置模拟音频测试
vad_simple_demo
```

### 3.5 LLM

**步骤 1：下载模型（GGUF 示例）**

```bash
mkdir -p ~/.cache/models/llm
cd ~/.cache/models/llm
wget https://archive.spacemit.com/spacemit-ai/model_zoo/llm/qwen2.5-0.5b-instruct-q4_0.gguf
```

如需体验更多模型，可查看 [LLM 模型目录](https://archive.spacemit.com/spacemit-ai/model_zoo/llm) 按需下载。

**步骤 2：启动 OpenAI 兼容服务并调用示例**

```bash
# 启动服务（8080）
llama-server -m ~/.cache/models/llm/qwen2.5-0.5b-instruct-q4_0.gguf -t 8 --port 8080 &

# 调用 SDK 示例程序
llm_chat "你好" "http://localhost:8080/v1" "qwen2.5-0.5b" "You are a helpful assistant." 256
```

若使用云端/远端 OpenAI 兼容服务，只需将第 2 个参数替换为云端的 `api_base`，并通过环境变量传入 API Key，例如：

```bash
export OPENAI_API_KEY=你的云端key
llm_chat "你好" "https://api.deepseek.com" "deepseek-chat" "You are a helpful assistant." 256
```

### 3.6 VLM

**步骤 1：安装依赖并下载模型**

```bash
sudo apt-get update
sudo apt-get install -y build-essential cmake curl libyaml-cpp-dev nlohmann-json3-dev
sudo apt install llama.cpp-tools-spacemit

cd vlm
bash scripts/download_model.sh fastvlm-mm-0.5b-q4_1
```

可用模型列表（均通过 `--vision-backend smt` + ONNX vision 验证）：

| 模型 ID | 参数量 | 磁盘大小 | 推理速度 |
|---------|-------|---------|---------|
| `fastvlm-mm-0.5b-q4_1` | 0.5B | ~766M | ~58 tok/s |
| `Qwen3.5-0.8B` | 0.8B | ~932M | ~40 tok/s |
| `Qwen3.5-2B` | 2B | ~2.6G | ~26 tok/s |
| `Qwen3.5-4B` | 4B | ~3.9G | ~13 tok/s |
| `qwen30ba3b-mm-q4_1` | 30B (MoE) | ~17.6G | ~18 tok/s |

模型默认下载到 `~/.cache/models/vlm/<model_name>/`，也可以通过 `VLM_CACHE_DIR=/data/models` 指定目录。

**步骤 2：编译 VLM SDK**

```bash
cd vlm
mkdir -p build && cd build
cmake .. -DCMAKE_BUILD_TYPE=Release
cmake --build . -j$(nproc)
ctest --output-on-failure
```

**步骤 3：运行 C++ 示例**

```bash
cd vlm/build
./fastvlm_demo \
  --config ../examples/fastvlm/config/fastvlm.yaml \
  --image /path/to/image.jpg \
  --prompt "用一句话描述这张图片"
```

**步骤 4：运行 Python 示例**

Python 接口为纯 HTTP 客户端，通过 OpenAI 兼容 API 与 llama-server 通信，无需 C ABI 依赖：

```bash
cd vlm
PYTHONPATH=$PWD/src/python:$PYTHONPATH \
  python3 examples/fastvlm/python/vlm_demo.py \
  --config examples/fastvlm/config/fastvlm.yaml \
  --image /path/to/image.jpg \
  --prompt "Describe this image"
```

**步骤 5：通过 Gateway 调用**

Gateway 已接入 VLM domain，支持本地 llama-server 模式和远程代理模式。接口路径为 `/v1/vlm/*`，提供 OpenAI 兼容的 `/v1/chat/completions` 路由。

启动 Gateway：

```bash
cd gateway
PYTHONPATH=src python3 -m uvicorn spacemit_ai_gateway.app.main:app \
  --host 0.0.0.0 --port 18790
```

注册并加载模型（使用 `local_path` 指向模型目录）：

```bash
# 注册模型
curl -X POST http://127.0.0.1:18790/v1/vlm/models/register \
  -H 'Content-Type: application/json' \
  -d '{"model":"fastvlm-mm-0.5b-q4_1","source_type":"local_path","local_path":"~/.cache/models/vlm/fastvlm-mm-0.5b-q4_1"}'

# 加载模型
curl -X POST http://127.0.0.1:18790/v1/vlm/models/load \
  -H 'Content-Type: application/json' \
  -d '{"model":"fastvlm-mm-0.5b-q4_1"}'
```

推理调用（文本）：

```bash
curl -X POST http://127.0.0.1:18790/v1/vlm/chat/completions \
  -H 'Content-Type: application/json' \
  -d '{"model":"fastvlm-mm-0.5b-q4_1","messages":[{"role":"user","content":"What is 2+2?"}],"max_tokens":50}'
```

推理调用（图像理解）：

```bash
curl -X POST http://127.0.0.1:18790/v1/vlm/chat/completions \
  -H 'Content-Type: application/json' \
  -d '{"model":"fastvlm-mm-0.5b-q4_1","messages":[{"role":"user","content":"Describe this image","image_path":"/path/to/image.jpg"}]}'
```

卸载模型：

```bash
curl -X POST http://127.0.0.1:18790/v1/vlm/models/unload \
  -H 'Content-Type: application/json' \
  -d '{"model":"fastvlm-mm-0.5b-q4_1"}'
```


## 4. 文档

更多使用说明请参看 [SpacemiT AI SDK 文档](https://www.spacemit.com/community/document/info?lang=zh&nodepath=ai/application_tools/ai-sdk.md)。
