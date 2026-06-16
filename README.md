# llama.cpp CUDA Build for Tesla P100

This repo is set up to automatically build llama.cpp with CUDA support for Tesla P100 (sm_60, Pascal architecture).

## Quick Start

### 1. Create the repo on GitHub

```bash
# If you have gh CLI installed:
cd llama-cuda-build
gh repo create llama-cuda-build --public --source=. --push

# Or manually:
# 1. Go to https://github.com/new
# 2. Create a repo named "llama-cuda-build"
# 3. Push this code:
git init
git add .
git commit -m "Add CUDA P100 build workflow"
git remote add origin https://github.com/YOUR_USERNAME/llama-cuda-build.git
git push -u origin main
```

### 2. Trigger the build

1. Go to your repo on GitHub
2. Click **Actions** tab
3. Click **"Build llama.cpp CUDA for Tesla P100"**
4. Click **"Run workflow"** → **"Run workflow"**

### 3. Wait & Download

- Build takes about 15-30 minutes
- When done, click the workflow run → scroll down to **Artifacts**
- Download `llama-cuda-sm60-p100.zip`
- Unzip all files to the same directory on your P100 machine

### 4. Download CUDA runtime DLLs

You also need the CUDA 12.2 runtime DLLs. Download from:
```
https://github.com/ggml-org/llama.cpp/releases
```
Look for `cudart-llama-bin-win-cuda-12.4-x64.zip` in the latest release assets.
Unzip the DLL files to the same directory as the exe files.

> Note: Even though it says CUDA 12.4, the cudart DLLs are backward compatible
> with CUDA 12.2 drivers as long as your driver version >= 536.25

### 5. Run

```cmd
llama-cli.exe -m your-model.gguf -p "Hello" -ngl 99 -c 4096
```

## Build Configuration

| Parameter | Value | Reason |
|-----------|-------|--------|
| CUDA_ARCHITECTURES | 60 | Tesla P100 is Pascal sm_60 |
| GGML_CUDA_FA | OFF | P100 has no Tensor Core, FlashAttention won't help |
| GGML_CUDA_FORCE_MMQ | ON | Better quantized inference on Pascal |
| GGML_CUDA_GRAPHS | OFF | Avoids potential issues on older GPUs |
| CUDA Toolkit | 12.2.2 | Matches your driver's CUDA version |

## Troubleshooting

- **"no GPU found"**: Make sure NVIDIA driver is installed on the P100 machine
- **"cuda capability not supported"**: The build may have fallen back to default archs. Re-trigger the workflow
- **"cudart64_12.dll not found"**: You need to download the cudart DLLs (step 4 above)
- **Build failed**: Check the Actions log. CUDA install may timeout - re-run the workflow
