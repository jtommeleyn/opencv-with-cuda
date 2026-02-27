<div align="center">

# 🖥️ Building OpenCV with CUDA & cuDNN for Python on Windows

**A step-by-step guide to compile OpenCV from source with GPU acceleration**

[![OS - Windows](https://img.shields.io/badge/OS-Windows-blue?logo=windows)](https://www.microsoft.com/windows)
[![CUDA](https://img.shields.io/badge/CUDA-12.9-green?logo=nvidia)](https://developer.nvidia.com/cuda-toolkit)
[![OpenCV](https://img.shields.io/badge/OpenCV-4.13.0--dev-red?logo=opencv)](https://opencv.org/)
[![Python](https://img.shields.io/badge/Python-3.11-yellow?logo=python)](https://www.python.org/)
[![License: MIT](https://img.shields.io/badge/License-MIT-brightgreen.svg)](LICENSE)

</div>

---

> [!NOTE]
> This method was tested and confirmed working in **December 2025**.
> All paths shown are for illustration — your paths may differ.

---

## 📑 Table of Contents

| # | Section |
|---|---------|
| 1 | [Prerequisites](#-prerequisites) |
| 2 | [Install Visual Studio 2022](#-install-visual-studio-2022) |
| 3 | [Install CUDA Toolkit](#-install-cuda-toolkit) |
| 4 | [Install cuDNN](#-install-cudnn) |
| 5 | [Download OpenCV + opencv_contrib](#-download-opencv--opencv_contrib) |
| 6 | [Set Up Project Structure](#-set-up-project-structure) |
| 7 | [Install CMake GUI](#-install-cmake-gui) |
| 8 | [Create Python Environment (Anaconda)](#-create-python-environment-anaconda) |
| 9 | [Configure CMake (GUI)](#%EF%B8%8F-configure-cmake-gui) |
| 10 | [Set CUDA Architectures](#-set-cuda-architectures) |
| 11 | [Disable Optional Builds](#-disable-optional-builds-faster-compilation) |
| 12 | [Configure cuDNN in CMake](#-configure-cudnn-in-cmake) |
| 13 | [Generate the CMake Project](#-generate-the-cmake-project) |
| 14 | [Build via PowerShell](#-build-via-powershell) |
| 15 | [Fix Python DLL Paths](#-fix-python-dll-paths) |
| 16 | [GPU Test Script](#-gpu-test-script) |
| 17 | [Troubleshooting](#-troubleshooting) |

---

## 📋 Prerequisites

> [!TIP]
> The versions below were used during testing. Newer or older versions may also work.

| Software | Version | Notes |
|:---------|:--------|:------|
| Visual Studio | 2022 (Community is fine) | With C++ workload |
| CUDA Toolkit | 12.9 recommended | Supports newer GPUs |
| cuDNN | 9.14 (for CUDA 12.9) | For DNN modules |
| OpenCV | 4.13.0-dev | From source |
| opencv_contrib | 4.13.0-dev | Extra CUDA modules |
| CMake GUI | 3.28+ | For configuration |
| Python | 3.11 | Works with ABI3 manylinux |
| Anaconda | 23.7.4 | For virtual environments |

---

## 1️⃣ Install Visual Studio 2022

Download **Visual Studio Community**:
👉 https://visualstudio.microsoft.com/vs/community/

During installation, check the following:
- [x] Desktop development with C++
- [x] MSVC 143/144 compiler tools
- [x] Windows 10/11 SDK

---

## 2️⃣ Install CUDA Toolkit

Download from the NVIDIA website:
👉 https://developer.nvidia.com/cuda-toolkit-archive

Install **CUDA 12.9** (recommended). **Restart your PC** afterwards.

---

## 3️⃣ Install cuDNN

Download cuDNN (requires a free NVIDIA account):
👉 https://developer.nvidia.com/cudnn

Select **cuDNN 9.14** for **CUDA 12.9**.

After installation, you should have directories like:

```
C:\Program Files\NVIDIA\CUDNN\v9.14\include\12.9
C:\Program Files\NVIDIA\CUDNN\v9.14\lib\12.9\x64
```

---

## 4️⃣ Download OpenCV + opencv_contrib

Download the ZIP files from GitHub:
- **OpenCV**: https://github.com/opencv/opencv
- **opencv_contrib**: https://github.com/opencv/opencv_contrib

Choose version **4.13.0-dev** for both.

---

## 5️⃣ Set Up Project Structure

Create the following folder structure:

```
C:\CUDA\
├── opencv-4.13.0-dev\
├── opencv_contrib-4.13.0-dev\
└── build\
```

---

## 6️⃣ Install CMake GUI

Download CMake:
👉 https://cmake.org/download/

Then launch **CMake (cmake-gui)**.

---

## 7️⃣ Create Python Environment (Anaconda)

Download Anaconda:
👉 https://www.anaconda.com/download

Create and activate a new environment:

```bash
conda create -n cuda_12.9 python=3.11
conda activate cuda_12.9
pip install numpy
```

---

## ⚙️ Configure CMake (GUI)

Open CMake GUI and set the paths:

| Field | Value |
|:------|:------|
| **Where is the source code** | `C:\CUDA\opencv-4.13.0-dev` |
| **Where to build the binaries** | `C:\CUDA\build` |

Click **Configure** → select:
- Generator: **Visual Studio 17 2022**
- Platform: **x64**

Then click **Configure**.

### Enable CUDA

Search for and enable:

| Variable | Value |
|:---------|:------|
| `WITH_CUDA` | `ON` |
| `CUDA_FAST_MATH` | `ON` |
| `ENABLE_FAST_MATH` | `ON` |

### Set Extra Modules Path

```
OPENCV_EXTRA_MODULES_PATH = C:/CUDA/opencv_contrib-4.13.0-dev/modules
```

### Configure Python Paths

Set these to match your Anaconda environment:

```
PYTHON3_EXECUTABLE         = C:/.../anaconda/envs/cuda_12.9/python.exe
PYTHON3_LIBRARY            = C:/.../anaconda/envs/cuda_12.9/libs/python311.lib
PYTHON3_INCLUDE_DIR        = C:/.../anaconda/envs/cuda_12.9/include
PYTHON3_PACKAGES_PATH      = C:/.../anaconda/envs/cuda_12.9/Lib/site-packages
PYTHON3_NUMPY_INCLUDE_DIRS = .../anaconda/envs/cuda_12.9/Lib/site-packages/numpy/_core/include
```

Also enable:

```
BUILD_opencv_python3 = ON
```

> [!IMPORTANT]
> Click **Configure** again after setting these values.

---

## 🏗️ Set CUDA Architectures

Search for `CUDA_ARCH_BIN` and set it to match your GPU:

<details>
<summary><b>📋 CUDA Compute Capability Reference Table</b></summary>

| GPU Family | Architecture | Compute Capability |
|:-----------|:-------------|:-------------------|
| GTX 10xx | Pascal | **6.1** |
| GTX 16xx | Turing | **7.5** |
| RTX 20xx | Turing | **7.5** |
| Tesla T4 | Turing | **7.5** |
| Quadro RTX 8000 | Turing | **7.5** |
| Tesla V100 | Volta | **7.0** |
| RTX 30xx | Ampere | **8.6** |
| RTX A6000 | Ampere | **8.6** |
| RTX 40xx | Ada Lovelace | **8.9** |
| RTX 5000 Ada | Ada Lovelace | **8.9** |

</details>

---

## ⏩ Disable Optional Builds (Faster Compilation)

Disable the following to speed up compilation:

| Variable | Value |
|:---------|:------|
| `BUILD_TESTS` | `OFF` |
| `BUILD_PERF_TESTS` | `OFF` |
| `BUILD_JAVA` | `OFF` |
| `BUILD_DOCS` | `OFF` |
| `BUILD_EXAMPLES` | `OFF` |
| `CMAKE_CONFIGURATION_TYPES` | `Release` |

---

## 🔧 Configure cuDNN in CMake

| Variable | Value |
|:---------|:------|
| `CUDNN_LIBRARY` | `C:/Program Files/NVIDIA/CUDNN/v9.14/lib/12.9/x64/cudnn.lib` |
| `CUDNN_INCLUDE_DIR` | `C:/Program Files/NVIDIA/CUDNN/v9.14/include/12.9` |

> [!TIP]
> Keep clicking **Configure** until there are no more red entries or errors.

---

## ✅ Generate the CMake Project

1. Click **Configure** (one final time)
2. Click **Generate**

If both complete without errors, you're ready to build!

---

## 🔨 Build via PowerShell

Open **PowerShell as Administrator** and run:

```powershell
cmake --build "C:\CUDA\build" --target INSTALL --config Release -- /m
```

> [!NOTE]
> This will compile OpenCV and automatically install the Python bindings into your Anaconda environment. The build may take **30–90 minutes** depending on your hardware.

---

## 🐍 Fix Python DLL Paths

To prevent DLL errors at runtime, add the following **before** importing `cv2`:

```python
import os

os.add_dll_directory(r"C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v12.9\bin")
os.add_dll_directory(r"C:\Program Files\NVIDIA\CUDNN\v9.14\bin\12.9")
os.add_dll_directory(r"C:\CUDA\build\install\x64\vc17\bin")

import cv2
```

---

## 🧪 GPU Test Script

Use this script to verify everything works:

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
    print("✅ CUDA is working!")
else:
    print("❌ No CUDA devices found.")
```

**Expected output:**

```
OpenCV version: 4.13.0-dev
CUDA devices: 1
GPU test shape: (2000, 2000)
✅ CUDA is working!
```

---

## ❓ Troubleshooting

| Error | Cause | Solution |
|:------|:------|:---------|
| `ImportError: cv2 not found` | Python paths incorrect in CMake | Double-check `PYTHON3_*` variables and rebuild |
| `DLL load failed` / `DLL missing` | Missing runtime DLLs | Add `os.add_dll_directory()` calls before importing `cv2` |
| `Unsupported compute capability` | Wrong GPU architecture | Set `CUDA_ARCH_BIN` to match your GPU (see [reference table](#%EF%B8%8F-set-cuda-architectures)) |
| `cuDNN: not found` | cuDNN path incorrect | Verify `CUDNN_LIBRARY` and `CUDNN_INCLUDE_DIR` in CMake |
| Build fails with `nvcc fatal` | CUDA/VS version mismatch | Ensure CUDA Toolkit supports your Visual Studio version |

---

<div align="center">

## 🎉 Done!

**You now have OpenCV with CUDA and cuDNN support ready to use with Python.**

---

*If this guide helped you, consider giving it a ⭐!*

</div>
