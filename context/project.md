
# Project Setup and Whisper.cpp GPU Integration

## Project Initialization

- The project was created using: `npx create-video@latest` (select the TikTok template), and follow by running `npm install`.
- Initial setup requires running `node sub.mjs`, which installs `whisper.cpp` and downloads the `medium.en` model.
- Note: The default `medium.en` model and prebuilt binaries only support CPU, resulting in slow performance.

## Building whisper.cpp for NVIDIA GPU (RTX 5090)

To enable GPU acceleration, follow these steps:

1. **Install Prerequisites:**
   - [Git](https://git-scm.com/download/win) (choose the Git Bash option during installation)
   - [CMake](https://cmake.org/download/)
   - [CUDA Toolkit](https://developer.nvidia.com/cuda-downloads)
   - [Visual Studio 2022 Build Tools](https://visualstudio.microsoft.com/visual-cpp-build-tools) (select the "Desktop development with C++" workload)
   - [FFmpeg](https://ffmpeg.org/) (`choco install ffmpeg -y`)
2. **Restart your computer** after installing the above tools.
3. **Clone the whisper.cpp repository** using Git Bash:
   ```bash
   git clone https://github.com/ggml-org/whisper.cpp.git
   ```
4. **Download the desired Whisper model** (see [Whisper Models](https://github.com/ggml-org/whisper.cpp/blob/master/models/README.md)):
   ```bash
   ./models/download-ggml-model.sh <model_name>
   ```
5. **Open** `x64 Native Tools Command Prompt for VS 2022` **as administrator**.
6. **Navigate to the `whisper.cpp` directory**.
7. **Build the GPU-enabled binary** (reference: [NVIDIA GPU Support](https://github.com/ggml-org/whisper.cpp?tab=readme-ov-file#nvidia-gpu-support)):
   ```
   cmake -B build -DGGML_CUDA=1 -DCMAKE_CUDA_ARCHITECTURES="86"
   cmake --build build -j --config Release
   ```
   - The build process takes about 10 minutes and produces executables in `build/bin`.
8. **Executable Note:**
   - As of writing, `main.exe` is deprecated. If you encounter an error, use `whisper-cli.exe` instead.
   - For compatibility, copy `whisper-cli.exe` and rename it to `main.exe` in the `build/bin` directory.
9. **Integrate with Remotion:**
   - Remove the prebuilt whisper binaries installed by Remotion.
   - Create a symbolic link so Remotion uses your custom build:
     ```
     mklink /D "C:\{path-to-remotion}\whisper.cpp" "C:\{path-to-whisper}\whisper.cpp\build\bin"
     ```
10. **Transcribe videos:**
    - Run `node sub.mjs` to transcribe video files in the `public` directory.

## Using Custom or Quantized Models

To use quantized models not predefined in `whisper-config.mjs`:

1. **Download the desired model** (e.g., `large-v3-turbo-q8_0`) using the script:
   ```bash
   ./models/download-ggml-model.sh large-v3-turbo-q8_0
   ```
2. **Copy the downloaded model** from `models/` to `build/bin/`.
3. **Update `whisper-config.mjs`:**
   - Set `WHISPER_MODEL` to the base model name (e.g., `large-v3-turbo`), omitting the quantization suffix.
4. **Edit `sub.mjs`:**
   - Comment out the `downloadWhisperModel` line, since the model is already present.
5. **Transcribe videos:**
   - Run `node sub.mjs` to transcribe video files in the `public` directory using the quantized model.