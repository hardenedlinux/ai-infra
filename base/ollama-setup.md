# Setup ollama

Ollama is a tool for running Llama models.

## Install ollama

```bash
curl -fsSL https://ollama.com/install.sh | sh
```

## Change models storage location

The default location of the models is `/usr/share/ollama/.ollama/models/`. You can change it to your own location.
For example, if you want to change the models storage location to `~/Project/models/`, you can run the following command:

```bash
sudo mount --bind ~/Project/models/ /usr/share/ollama/.ollama/models/

```

If you do it in first time, you may need to change ownership of the new location:

```bash
sudo chown -R ollama:ollama ~/Project/models/
```

## Test ollama

You can test ollama by running the following command:

```bash
ollama run llama3
```

Test the ollama server by running the following command:

```bash
curl -X POST http://localhost:11434/api/generate -d '{
  "model": "llama3",
  "prompt":"hello"
 }'
```

## Stop ollama server

```bash
sudo systemctl stop ollama
```
