# Guide: Installing OpenCV + CUDA + cuDNN for Python (Windows)

This comprehensive guide explains step-by-step how to build **OpenCV with CUDA support**, including **cuDNN**, on Windows using **CMake GUI**, **Visual Studio 2022**, and **Python 3.11 (Anaconda)**.

> **Note:** This method was tested and confirmed working in December 2025. All paths shown in this guide are for illustration purposes. Your paths may differ — pay close attention during installation!

---

## Table of Contents
1. [Prerequisites](#prerequisites)
2. [Installing Visual Studio 2022](#installing-visual-studio-2022)
3. [Installing CUDA Toolkit](#installing-cuda-toolkit)
4. [Installing cuDNN](#installing-cudnn)
5. [Downloading OpenCV + opencv_contrib](#downloading-opencv--opencv_contrib)
6. [Setting Up the Project Structure](#setting-up-the-project-structure)
7. [Installing CMake GUI](#installing-cmake-gui)
8. [Creating a Python Environment (Anaconda)](#creating-a-python-environment-anaconda)
9. [Configuring CMake (GUI)](#configuring-cmake-gui)
10. [Setting CUDA Architectures](#setting-cuda-architectures)
11. [Disabling Optional Builds (Faster Compilation)](#disabling-optional-builds-faster-compilation)
12. [Configuring cuDNN in CMake](#configuring-cudnn-in-cmake)
13. [Generating the CMake Project](#generating-the-cmake-project)
14. [Building via PowerShell](#building-via-powershell)
15. [Making OpenCV Work with Python (DLL Paths)](#making-opencv-work-with-python-dll-paths)
16. [Python GPU Test Script](#python-gpu-test-script)
17. [Common Errors](#common-errors)

---

## Prerequisites

> **Note:** The versions listed below were used during testing. Other (newer/older) versions may also work.

| Software | Version | Notes |
|----------|---------|-------|
| Visual Studio | 2022 (Community is fine) | With C++ workload |
| CUDA Toolkit | 12.9 recommended | Supports newer GPUs |
| cuDNN | 9.14 (CUDA 12.9) | For DNN modules |
| OpenCV | 4.13.0-dev | From source |
| opencv_contrib | 4.13.0-dev | Extra CUDA modules |
| CMake GUI | 3.28+ | For configuration |
| Python | 3.11 | Works with ABI3 manylinux |
| Anaconda | 23.7.4 | For virtual environment |

---

## Installing Visual Studio 2022

Download Visual Studio Community:

➡️ https://visualstudio.microsoft.com/vs/community/

During installation, **check the following**:
- ✔ Desktop development with C++
- ✔ MSVC 143/144 compiler tools
- ✔ Windows 10/11 SDK

---

## Installing CUDA Toolkit

Download from the NVIDIA website:

➡️ https://developer.nvidia.com/cuda-toolkit-archive

Install CUDA **12.9** (recommended). Restart your PC afterwards.

---

## Installing cuDNN

Download cuDNN (requires an NVIDIA account):

➡️ https://developer.nvidia.com/cudnn

Select:
- **cuDNN 9.14** (CUDA 12.9)

After installation, you will have directories like:
```
C:\Program Files\NVIDIA\CUDNN\v9.14\include\12.9
C:\Program Files\NVIDIA\CUDNN\v9.14\lib\12.9\x64
```

---

## Downloading OpenCV + opencv_contrib

Download the ZIP files from GitHub:
- OpenCV: https://github.com/opencv/opencv
- Contrib: https://github.com/opencv/opencv_contrib

Choose version: **4.13.0-dev**.

---

## Setting Up the Project Structure

Create a folder:
```
C:\CUDA
```

Place the following inside:
```
C:\CUDA\opencv-4.13.0-dev
C:\CUDA\opencv_contrib-4.13.0-dev
C:\CUDA\build
```

---

## Installing CMake GUI

Download CMake GUI:

➡️ https://cmake.org/download/

Then launch **CMake (GUI)**.

---

## Creating a Python Environment (Anaconda)

Install Anaconda:

➡️ https://www.anaconda.com/download

Create a new environment:
```bash
conda create -n cuda_12.9 python=3.11
conda activate cuda_12.9
pip install numpy
```

---

## Configuring CMake (GUI)

Open CMake GUI:

**Where is the source code:**
```
C:\CUDA\opencv-4.13.0-dev
```

**Where to build the binaries:**
```
C:\CUDA\build
```

Click **Configure** → select:
- Generator: **Visual Studio 17 2022**
- Platform: **x64**

Click **Configure**.

---

## Important CMake Settings

### Enable CUDA

Search for and enable:
- `WITH_CUDA = ON`
- `CUDA_FAST_MATH = ON`
- `ENABLE_FAST_MATH = ON`

### Extra Modules

```
OPENCV_EXTRA_MODULES_PATH = C:/CUDA/opencv_contrib-4.13.0-dev/modules
```

### Configure Python

Copy these parameters from your Anaconda environment:
```
PYTHON3_EXECUTABLE      = C:/.../anaconda/envs/cuda_12.9/python.exe
PYTHON3_LIBRARY         = C:/.../anaconda/envs/cuda_12.9/libs/python311.lib
PYTHON3_INCLUDE_DIR     = C:/.../anaconda/envs/cuda_12.9/include
PYTHON3_PACKAGES_PATH   = C:/.../anaconda/envs/cuda_12.9/Lib/site-packages
PYTHON3_NUMPY_INCLUDE_DIRS = .../anaconda/envs/cuda_12.9/Lib/site-packages/numpy/_core/include
```

Also enable:
```
BUILD_opencv_python3 = ON
```

Click **Configure** again.

---

## Setting CUDA Architectures

Search for:
```
CUDA_ARCH_BIN
```

For the tested GPU:
```
7.5
```

Other examples:
- RTX 30xx → **8.6**
- RTX 40xx → **8.9**
- GTX 10xx (Pascal) → **6.1**
- GTX 16xx (Turing) → **7.5**
- RTX 20xx (Turing) → **7.5**
- RTX A6000 (Ampere) → **8.6**
- RTX 5000 Ada → **8.9**
- Tesla V100 → **7.0**
- Tesla T4 → **7.5**
- Quadro RTX 8000 → **7.5**

---

## Disabling Optional Builds (Faster Compilation)

Disable the following:
- `BUILD_TESTS`
- `BUILD_PERF_TESTS`
- `BUILD_JAVA`
- `BUILD_DOCS`
- `BUILD_EXAMPLES`

Set only **Release** in:
```
CMAKE_CONFIGURATION_TYPES = Release
```

---

## Configuring cuDNN in CMake

Search for:
```
CUDNN_LIBRARY
```

Set to:
```
C:/Program Files/NVIDIA/CUDNN/v9.14/lib/12.9/x64/cudnn.lib
```

Search for:
```
CUDNN_INCLUDE_DIR
```

Set to:
```
C:/Program Files/NVIDIA/CUDNN/v9.14/include/12.9
```

Click **Configure** again until there are no more errors.

---

## Generating the CMake Project

Click:
- **Configure**
- **Generate**

If both complete without errors → you're ready to build.

---

## Building via PowerShell

Open **PowerShell as Administrator**.

Run:
```powershell
cmake --build "C:\CUDA\build" --target INSTALL --config Release -- /m
```

This will automatically install OpenCV into your Anaconda environment.

---

## Making OpenCV Work with Python (DLL Paths)

To prevent DLL errors, add the following at the top of your script:

```python
import os

os.add_dll_directory(r"C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v12.9\bin")
os.add_dll_directory(r"C:\Program Files\NVIDIA\CUDNN\v9.14\bin\12.9")
os.add_dll_directory(r"C:\CUDA\build\install\x64\vc17\bin")

import cv2
```

---

## Python GPU Test Script

```python
import os
import numpy as np

os.add_dll_directory(r"C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v12.9\bin")
os.add_dll_directory(r"C:\Program Files\NVIDIA\CUDNN\v9.14\bin\12.9")
os.add_dll_directory(r"C:\CUDA\build\install\x64\vc17\bin")

import cv2

print("OpenCV version:", cv2.__version__)

num_devices = cv2.cuda.getCudaEnabledDeviceCount()
print("CUDA devices:", num_devices)

if num_devices > 0:
    cv2.cuda.setDevice(0)
    arr = np.random.rand(2000, 2000).astype(np.float32)
    gpu_mat = cv2.cuda_GpuMat()
    gpu_mat.upload(arr)
    gpu_result = cv2.cuda.multiply(gpu_mat, gpu_mat)
    result = gpu_result.download()
    print("GPU test shape:", result.shape)
```

---

## Common Errors

### *ImportError: cv2 not found*
→ Python paths were set incorrectly in CMake.

### *DLL missing*
→ Don't forget to use `os.add_dll_directory()`.

### *Unsupported compute capability*
→ `CUDA_ARCH_BIN` is set to the wrong value.

### *cuDNN: not found*
→ `CUDNN_LIBRARY` or `CUDNN_INCLUDE_DIR` is set incorrectly.

---

## Done!

You now have OpenCV with CUDA and cuDNN support installed and ready to use with Python. 🎉
