# Train your own Llama3 with unsloth

<center>
<img src="https://raw.githubusercontent.com/unslothai/unsloth/main/images/unsloth%20logo%20white%20text.png" alt="unsloth"/>
</center>

## Introduction

Unsloth is a lightweight library for faster LLM fine-tuning which is fully compatible with the Hugging Face ecosystem (Hub, transformers, PEFT, TRL).

Before diving into Unsloth, it may be helpful to read our [QLoRA blog post](https://huggingface.co/blog/4bit-transformers-bitsandbytes), or be familiar with LLM fine-tuning using the Huggingface PEFT library.

In this tutorial, we train a new model with `unsloth/llama-3-8b-bnb-4bit`.

## Prerequisites

The test environment is Ubuntu 24.04 with NVIDIA A10G GPU.

First, make sure you've installed the NVIDIA driver and CUDA Toolkit according to the [Prepare the CUDA environment in AWS G5 instances undert Ubuntu 24.04](https://github.com/hardenedlinux/ai-infra/blob/master/base/aws-g5-cuda-dev-environment.md) article.

### Install conda

We strongly recommended to use conda to manage your Python environment. It's the easiest way to avoid wasting time on dependency conflicts.

```bash
wget -c https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
bash Miniconda3-latest-Linux-x86_64.sh

source ~/.bashrc

conda create --name unsloth_env \
    python=3.10 \
    pytorch-cuda=<11.8/12.1> \
    pytorch cudatoolkit xformers -c pytorch -c nvidia -c xformers \
    -y
conda activate unsloth_env
```

### Install unsloth

```bash
pip install "unsloth[colab-new] @ git+https://github.com/unslothai/unsloth.git"

pip install --no-deps "trl<0.9.0" peft accelerate bitsandbytes
```

## Prepare the dataset

We use ["yahma/alpaca-cleaned"](https://huggingface.co/datasets/yahma/alpaca-cleaned) as the dataset. You can replace it with your own dataset.

**The framework will download the dataset automatically when you specified it. So you don't have to download manually. Make sure your dateset namespace is correct. **

## Train a language model with unsloth

Save the following code to `train.py`:

```python
import torch

from trl import SFTTrainer
from transformers import TrainingArguments
from datasets import load_dataset

from unsloth import FastLanguageModel

max_seq_length = 2048 # Supports RoPE Scaling interally, so choose any!

# Load Llama model
model, tokenizer = FastLanguageModel.from_pretrained(
    model_name = "unsloth/llama-3-8b-bnb-4bit", # modify to your prefered model
    max_seq_length = max_seq_length,
    dtype = None,
    load_in_4bit = True,
)

# Do model patching and add fast LoRA weights
model = FastLanguageModel.get_peft_model(
    model,
    r = 16,
    target_modules = ["q_proj", "k_proj", "v_proj", "o_proj",
                      "gate_proj", "up_proj", "down_proj",],
    lora_alpha = 16,
    lora_dropout = 0, # Supports any, but = 0 is optimized
    bias = "none",    # Supports any, but = "none" is optimized
    use_gradient_checkpointing = True,
    random_state = 3407,
    max_seq_length = max_seq_length,
)

# Get dataset
alpaca_prompt = """Below is an instruction that describes a task, paired with an input that provides further context. Write a response that appropriately completes the request.

### Instruction:
{}

### Input:
{}

### Response:
{}"""

EOS_TOKEN = tokenizer.eos_token # Must add EOS_TOKEN
def formatting_prompts_func(examples):
    instructions = examples["instruction"]
    inputs       = examples["input"]
    outputs      = examples["output"]
    texts = []
    for instruction, input, output in zip(instructions, inputs, outputs):
        # Must add EOS_TOKEN, otherwise your generation will go on forever!
        text = alpaca_prompt.format(instruction, input, output) + EOS_TOKEN
        texts.append(text)
    return { "text" : texts, }
pass

dataset = load_dataset("yahma/alpaca-cleaned", split = "train")
dataset = dataset.map(formatting_prompts_func, batched = True,)

trainer = SFTTrainer(
    model = model,
    train_dataset = dataset,
    dataset_text_field = "text",
    max_seq_length = max_seq_length,
    tokenizer = tokenizer,
    args = TrainingArguments(
      per_device_train_batch_size = 2,
      gradient_accumulation_steps = 4,
      warmup_steps = 10,
      max_steps = 60,
      fp16 = not torch.cuda.is_bf16_supported(),
      bf16 = torch.cuda.is_bf16_supported(),
      logging_steps = 1,
      output_dir = "outputs",
      optim = "adamw_8bit",
      seed = 3407,
  ),
)
trainer.train()
```

## Train with unsloth

```bash
python train.py
```

Wait for the training to finish. The model will be saved in the `outputs` directory.
