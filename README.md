# Jetson Orin Nano Installation Guide

This document summarizes the installation and software setup process for the **NVIDIA Jetson Orin Nano 8GB** using **JetPack 7.2**. The installation is performed from a Windows PC using VMware Workstation Pro and Ubuntu Desktop 24.04.4 LTS, followed by SDK Manager configuration and post-installation setup on the Jetson Orin Nano.

---

## Prerequisites

The following software and hardware are required before starting the installation.

| Item | Description |
|---|---|
| Windows OS | Host operating system |
| VMware Workstation Pro | Used to run the Ubuntu virtual machine |
| Ubuntu Desktop 24.04.4 LTS | Linux environment used for the Jetson installation |
| NVIDIA SDK Manager | Used to flash Jetson Linux and install JetPack components |
| Jetson Orin Recovery Mode | The Jetson Orin Nano must be placed into recovery mode for flashing |
| USB 256GB+ | USB storage device used as the selected storage device |
| LAN DHCP | Network connection using DHCP |
| POWER | Power connection for the Jetson Orin Nano |
| USB CC | USB-C connection between the host computer and Jetson |

### Download Links

- VMware Workstation Pro  
  https://www.vmware.com/info/workstation-pro/evaluation

- Ubuntu Desktop 24.04.4 LTS  
  https://ubuntu.com/download/alternative-downloads

- NVIDIA SDK Manager  
  https://developer.download.nvidia.com/sdkmanager/redirects/sdkmanager-exe.html

---

# STEP 01 — Configure NVIDIA SDK Manager

### Step Description

Install and launch NVIDIA SDK Manager, then select the target Jetson hardware and the required JetPack and SDK components.

### Configuration

**SDK Manager Version**

```text
SDK Manager 2.4.1.13536 x86_64
```

**Target Hardware**

```text
Jetson Orin Nano modules
```

**JetPack**

```text
JetPack 7.2
```

**Installation Method**

```text
Direct Flash
```

**DeepStream**

```text
DeepStream 9.1
```

**Holoscan**

```text
Holoscan 4.4
```

---

# STEP 02 — Select Jetson Linux and SDK Components

### Step Description

Select the Jetson Linux image, WSL-related setup components, Jetson runtime components, CUDA and AI libraries, computer vision libraries, container support, multimedia components, and developer tools.

## Jetson Linux

The following Jetson Linux components are selected:

```text
Jetson Linux
Jetson Linux Image
WSL Setup Preparation 39.2
WSL Ubuntu Instance 39.2
WSL Kernel Image 39.2
Windows USBIPD 39.2
Drivers for Jetson 39.2
File System and OS 39.2
Flash Jetson Linux
Flash Jetson Linux 39.2
WSL Flash Post Setup 39.2
```

## Jetson Runtime Components

Additional runtime components are selected:

```text
Jetson Runtime Components
Additional Setups
Date Time Target Setup 1.0
Gstreamer 39.2
Jetson Camera SIPL 39.2
```

## CUDA and AI Runtime Components

The CUDA and AI runtime components are:

```text
CUDA Toolkit Runtime
CUDA Toolkit Runtime 13.2
CUDA X-Al Runtime
CuDNN Runtime 9.20
TensorRT Runtime 10.16
```

## Computer Vision Runtime

The computer vision runtime components are:

```text
Computer Vision Runtime
OpenCV Runtime 4.8
CuPVA Runtime 2.9.1
VPI Runtime 4.1.3
```

## NVIDIA Container Runtime

The NVIDIA container runtime is selected with Docker integration:

```text
NVIDIA Container Runtime
NVIDIA Container Runtime with Docker integration 1.19
```

## Multimedia

The Multimedia API is selected:

```text
Multimedia
Multimedia API 39.2
```

## Jetson SDK Components

The following SDK components are selected.

### CUDA Toolkit

```text
CUDA Toolkit
CUDA Toolkit 13.2
```

### CUDA-X AI

```text
CUDA-X AI
CuDNN 9.20
TensorRT 10.16
```

### Computer Vision (UNCHECK)

```text
Computer Vision
OpenCV 4.8
VPI 4.1.3
```

### Developer Tools

```text
Developer Tools
Nsight Systems 2026.3
```

### DeepStream

```text
DeepStream
DeepStream 9.1
```

---

# STEP 03 — Configure Jetson Orin Nano

### Step Description

Select the Jetson Orin Nano 8GB as the target device, configure the initial username and password, and select the USB storage device.

## Target Device

```text
Selected device: Jetson Orin Nano 8GB
```

## OEM Configuration

Select:

```text
1. OEM Configuration: Pre-Config
```

Configure the username:

```text
New Username: jetson
```

Configure the password:

```text
New Password: jetson
```

## Storage Device

Select:

```text
2. Storage Device: USB
```

---

## SDK Manager Installation Configuration

SDK Manager is now ready to install the SDK components on the Jetson Orin Nano module.

```text
SDK Manager is about to install SDK components on your Jetson Orin Nano module
Connection: USB
Selected device: Jetson Orin Nano 8GB
IP Address: 192.168.55.1
Username: jetson
Password: jetson
Install
```

### Step Description

The Jetson Orin Nano is connected to the host computer through USB. The device is accessed using the IP address `192.168.55.1`, with the configured username and password. Select **Install** to begin installing the selected JetPack and SDK components.

---

# STEP 04 — Check the Installation Log

### Step Description

After SDK Manager completes the installation, check the installation log for details and possible errors or difficulties.

The recorded SDK Manager log for reference:

```text
SDKM_logs_JetPack_7.2_Linux_for_Jetson_Orin_Nano_8GB_Direct_Flash_2026-07-27_20-13-21
```

---

# Jetson Orin Nano First Boot

After the Jetson Orin Nano has been flashed and the initial SDK installation is complete, perform the following post-installation setup.

---

# STEP 05 — Install `jtop`

### Step Description

Install `jetson_stats`, which provides the `jtop` utility for monitoring the Jetson Orin Nano system and NVIDIA hardware status.

Run:

```bash
sudo apt update
sudo pip3 install --break-system-packages git+https://github.com/rbonghi/jetson_stats.git
sudo reboot
jtop
```

After rebooting, run:

```bash
jtop
```

---

# STEP 06 — Configure 8GB Swap Space

### Step Description

Disable the currently active swap space and create a new 8GB swap file. The swap file is then configured to remain active after reboot.

Turn off the active swap space:

```bash
sudo swapoff -a
```

Create an 8GB swap file:

```bash
sudo fallocate -l 8G /swapfile
```

Set the correct permissions:

```bash
sudo chmod 600 /swapfile
```

Create the swap area:

```bash
sudo mkswap /swapfile
```

Enable the swap file:

```bash
sudo swapon /swapfile
```

Make the swap file persistent across reboots:

```bash
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
```

Reboot the Jetson:

```bash
sudo reboot
```

---

# STEP 07 — Install OpenCV Dependencies

### Step Description

Install the required build tools and development libraries before compiling OpenCV from source.

Update the package list:

```bash
sudo apt update
```

Install the required dependencies:

```bash
sudo apt install -y build-essential cmake git libgtk2.0-dev pkg-config \
                    libavcodec-dev libavformat-dev libswscale-dev \
                    python3-dev python3-numpy libtbb-dev libjpeg-dev \
                    libpng-dev libtiff-dev
```

---

# STEP 08 — Download OpenCV and OpenCV Contrib

### Step Description

Download the OpenCV and OpenCV Contrib source code from GitHub using the `5.x` branch.

Navigate to the Downloads directory:

```bash
cd Downloads/
```

Clone OpenCV:

```bash
git clone --branch 5.x https://github.com/opencv/opencv.git
```

Clone OpenCV Contrib:

```bash
git clone --branch 5.x https://github.com/opencv/opencv_contrib.git
```

Navigate to the OpenCV source directory:

```bash
cd opencv
```

Create the build directory:

```bash
mkdir build
```

Enter the build directory:

```bash
cd build
```

---

# STEP 09 — Configure OpenCV with CUDA Support

### Step Description

Configure OpenCV for a release build with CUDA, cuDNN, cuBLAS, CUDA-accelerated DNN processing, GStreamer, V4L2, and Python 3 support.

Run the following CMake configuration:

```bash
cmake -D CMAKE_BUILD_TYPE=RELEASE \
      -D CMAKE_INSTALL_PREFIX=/usr/local \
      -D OPENCV_EXTRA_MODULES_PATH=../../opencv_contrib/modules \
      -D EIGEN_INCLUDE_PATH=/usr/include/eigen3 \
      -D WITH_CUDA=ON \
      -D WITH_CUDNN=ON \
      -D WITH_CUBLAS=ON \
      -D CUDA_FAST_MATH=ON \
      -D OPENCV_DNN_CUDA=ON \
      -D ENABLE_FAST_MATH=1 \
      -D CUDA_ARCH_BIN=8.7 \
      -D WITH_GSTREAMER=ON \
      -D WITH_LIBV4L=ON \
      -D BUILD_OPENCV_PYTHON3=ON \
      -D HAVE_opencv_python3=ON \
      -D BUILD_EXAMPLES=OFF ..
```

### Step Description

The configuration specifically enables CUDA-related acceleration and sets:

```text
CUDA_ARCH_BIN=8.7
```

The build also enables GStreamer, V4L2, and Python 3 support while disabling the OpenCV examples.

---

# STEP 10 — Compile and Install OpenCV

### Step Description

Compile OpenCV using all available CPU cores, install it to the system, and update the dynamic linker configuration.

Compile OpenCV:

```bash
make -j$(nproc)
```

Install OpenCV:

```bash
sudo make install
```

Update the dynamic linker cache:

```bash
sudo ldconfig
```

---

# STEP 11 — Verify OpenCV and CUDA

### Step Description

Check that Python can import OpenCV and verify the number of CUDA-enabled devices detected by OpenCV.

Run:

```bash
python3 -c "import cv2; print('OpenCV Version:', cv2.__version__); print('CUDA Devices:', cv2.cuda.getCudaEnabledDeviceCount())"
```

Recorded result:

```text
OpenCV Version: 5.1.0-dev
CUDA Devices: 1
```

This confirms that the installed OpenCV version is `5.1.0-dev` and that OpenCV detects one CUDA-enabled device.

Check that Python can import GStreamer with OpenCV.

Run:

```bash
python3 -c "import cv2; print(cv2.getBuildInformation())""
```

Recorded result:

```text
Video I/O:
    FFMPEG:                      YES
      avcodec:                   YES (60.31.102)
      avformat:                  YES (60.16.100)
      avutil:                    YES (58.29.100)
      swscale:                   YES (7.5.100)
      avdevice:                  NO
    GStreamer:                   YES (1.24.2)
    v4l/v4l2:                    YES (linux/videodev2.h)
    Orbbec:                      YES

```

This confirms that the installed GStreamer version is `1.24.2`.

---

# STEP 12 — Install PyTorch and Torchvision

### Step Description

Install Python virtual environment support, create a Python virtual environment, activate it, and install PyTorch and Torchvision using the CUDA 13.2 wheel index.

Install Python virtual environment support:

```bash
sudo apt install python3.12-venv
```

Create a virtual environment:

```bash
python3 -m venv --system-site-packages .venv
```

`"--system-site-packages" with System Packages for OpenCV with CUDA or GStreamer...`

Activate the virtual environment:

```bash
source .venv/bin/activate
```

Install PyTorch and Torchvision:

```bash
pip3 install torch torchvision --index-url https://download.pytorch.org/whl/cu132
```

Verify the installation:

```bash
python3 -c "import torch, torchvision; print(f'torch: {torch.__version__} (CUDA: {torch.cuda.is_available()})'); print(f'torchvision: {torchvision.__version__}')"
```

Recorded result:

```text
torch: 2.13.0+cu132 (CUDA: True)
torchvision: 0.28.0+cu132
```

---

# STEP 13 — Install Ultralytics YOLO

### Step Description

Install the latest Ultralytics YOLO package directly from the `main` branch and run the Ultralytics system check.

Install Ultralytics:

```bash
pip install git+https://github.com/ultralytics/ultralytics.git@main
```

Run the Ultralytics check:

```bash
python3 -c "import ultralytics; ultralytics.checks()"
```

---

# STEP 14 — Final Ultralytics System Check

### Step Description

The final Ultralytics check reports the installed software versions and available Jetson Orin Nano resources.

```text
Ultralytics 8.4.108 🚀
Python-3.12.3
torch-2.13.0+cu132
CUDA:0 (Orin, 7546MiB)
```

The reported system resources are:

```text
6 CPUs
7.4 GB RAM
56.8/226.9 GB disk
```

The setup output concludes with:

```text
Setup complete ✅
```

---

# Installation Summary

The installation process consists of the following major stages:

1. Prepare a Windows host with VMware Workstation Pro and Ubuntu Desktop 24.04.4 LTS.
2. Prepare the Jetson Orin Nano in recovery mode with USB storage and network connectivity.
3. Install and configure NVIDIA SDK Manager 2.4.1.13536.
4. Select Jetson Orin Nano modules and JetPack 7.2.
5. Configure Jetson Linux and the required JetPack SDK components.
6. Configure the Jetson Orin Nano 8GB with the `jetson` username and password.
7. Flash the Jetson Linux system and install the selected SDK components.
8. Check the SDK Manager installation log.
9. Complete the first-boot setup.
10. Install `jtop` using `jetson_stats`.
11. Configure an 8GB persistent swap file.
12. Build and install OpenCV 5.1.0-dev with CUDA support.
13. Verify that OpenCV detects one CUDA-enabled device.
14. Create a Python virtual environment.
15. Install PyTorch 2.13.0+cu132 and Torchvision 0.28.0+cu132.
16. Install Ultralytics YOLO from the `main` branch.
17. Run the Ultralytics system check.

