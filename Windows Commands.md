# System & Network Diagnostics

### 1. Checking Listening Ports (Windows PowerShell)
Useful for checking which ports are being used by background services (e.g., Ollama running on port 11434).
```bash
# Check all listening ports and filter for specific port numbers
netstat -ano | findstr LISTENING
```
2. Checking Local Model Attributes (Ollama)
To quickly verify the technical specs, parameters, and tags of a specific AI model.
### Syntax: ollama show <model_name>
```bash
ollama show qwen3.5:9b
```
