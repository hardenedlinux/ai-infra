# Build llama-cpp with CUDA

Make sure you have prepared the environment according to the [Prepare the CUDA environment in AWS G5 instances undert Ubuntu 24.04](base/Prepare the CUDA environment in AWS G5 instances undert Ubuntu 24.04.md) article.

```bash
git clone https://github.com/ggerganov/llama.cpp.git
cd llama.cpp
make LLAMA_CUDA=1 -j5
```
