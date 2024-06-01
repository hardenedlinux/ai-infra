# Prepare the CUDA environment in AWS G5 instances undert Ubuntu 24.04

AWS G5 instances are powered by NVIDIA A10G GPUs. There's no Nvidia driver and CUDA in default.
The following steps show how to prepare the CUDA environment in AWS G5 instances under Ubuntu 24.04.

## Install the NVIDIA driver
```bash
sudo apt update
sudo apt install -y ubuntu-drivers-common
sudo ubuntu-drivers install nvidia
sudo reboot
```

After the reboot, check if the driver works.
```bash
nvidia-smi
```

## Install the CUDA Toolkit
```bash
sudo apt nvidia-cuda-dev
```

## Install the cuDNN
NVIDIA cuDNN, short for CUDA Deep Neural Network library, is a highly optimized library for deep learning applications. It accelerates the training and inference of deep neural networks on NVIDIA GPUs.

```bash
sudo apt install nvidia-cudnn
```

## Install development tools
```bash
sudo apt install build-essential cmake git unzip
```

Now, you can start to develop your CUDA applications on AWS G5 instances.
