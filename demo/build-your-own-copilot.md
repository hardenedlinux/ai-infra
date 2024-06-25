# Build your own Copilot

This tutorial helps you to build your own local Copilot with [CodeLlama](https://github.com/meta-llama/codellama) model.

## Prepare the environment

### Install CUDA

First, make sure you've installed the NVIDIA driver and CUDA Toolkit according to the [Prepare the CUDA environment in AWS G5 instances undert Ubuntu 24.04](https://github.com/hardenedlinux/ai-infra/blob/master/base/aws-g5-cuda-dev-environment.md) article.

### Install Ollama

See how to [Setup Ollama](https://github.com/hardenedlinux/ai-infra/blob/master/base/ollama-setup.md).

### Install models

```bash
ollama run codellama:7b-instruct
ollama run codellama:7b-code
```

You may noticed that we installed two models, `codellama:7b-instruct` is for auto-complete, and `codellama:7b-code` is for chat. We will tell you how to use them later.

### Install VSCode

```bash
sudo apt install code
```

Or you want to [download and install manually](https://code.visualstudio.com/Download).

### Install Twinny

See this picture that how to install Twinny:

<center>
<img src="img/twinny.jpg" alt="Twinny"/>
</center>

## Configure Twinny

See the picture how to configure Twinny:

<center>
<img src="img/twinny-config.jpg" alt="Twinny Config"/>
</center>


```
Auto-complete
Hostname: localhost
Port: 11434
Path: /api/generate
Model Name: codellama:7b-code
FIM Template: codellama

Chat
Hostname: localhost
Port: 11434
Path: /v1/chat/completions
Model Name: codellama:7b-instruct
```

## Test Twinny

Code complete:

<img src="https://github.com/rjmacarthy/twinny/assets/5537428/69f567c0-2700-4474-b621-6099255bc87b" alt="Twinny code-complete"/>
</center>

Chat:

<img src="https://github.com/rjmacarthy/twinny/assets/5537428/a5c5bb34-60f6-41f6-8226-c62cf4c17c1d" alt="Twinny Chat"/>
</center>
