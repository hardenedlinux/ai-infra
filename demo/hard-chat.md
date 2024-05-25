# LLM chat demo - hard-chat

In this demo, we show how to build a chatbot using the Llama Chatbot model. The Llama Chatbot model is a pre-trained model that can be fine-tuned on your own dataset.

```bash
git clone https://github.com/hardenedlinux/hard-chat-demo.git
cd hard-chat-demo
```
# Prepare environment

The test environment is Ubuntu 24.04 with NVIDIA A10G GPU.

First, make sure you've installed the NVIDIA driver and CUDA Toolkit according to the [Prepare the CUDA environment in AWS G5 instances undert Ubuntu 24.04](base/aws-g5-cuda-dev-environment.md) article.

## Install Python virtual environment

It's recommended to use virtual environments to manage the Python packages. We use **virtualenv** in this article.

```bash
sudo apt install virtualenv
virtual .local
source .local/bin/activate
```

**NOTE: You have to `source .local/bin/activate` in the same path every time you open a new terminal.**

## Build llama-cpp-python with CUDA

```bash
pip3 install gradio sentencepiece
CMAKE_ARGS="-DLLAMA_CUDA=ON" FORCE_CMAKE=1 pip3 install llama-cpp-python
```

## Install dependencies

```bash
pip3 install -r requirements.txt
```

# Run the hard-chat

## Download the model

```bash
bash scripts/download-model.sh
```

## Run

```bash

python3 run.py
```
