🚀 Windows Local AI Coding Agent Deployment Guide (Ollama + DSH + Qwen)

🎯 Objective

To run a local 9B LLM on a Windows machine with an RTX 5060 (8GB VRAM) and deploy a functional AI Agent using the DSH (DeepSeek Harness) framework. The Agent will be capable of writing files and executing terminal commands autonomously.

---

Phase 1: Ollama Model Migration & VRAM Optimization

1. Resolve C-Drive Space Crisis (Unify Model Path)
By default, models download to the C drive, and paths can be inconsistent between Admin and Standard terminals. Set a User-level environment variable:

```powershell
[Environment]::SetEnvironmentVariable("OLLAMA_MODELS", "D:\OllamaModels", "User")
```

⚠️ Action Required: Close ALL terminal windows after this, reopen a new one, and verify by running echo $env:OLLAMA_MODELS to ensure it points to drive D.

2. Enable Performance Optimization (Essential for 8GB VRAM)
Before starting the Ollama service, set environment variables to enable Flash Attention and quantize the KV cache:

```powershell
$env:OLLAMA_FLASH_ATTENTION="1"
$env:OLLAMA_KV_CACHE_TYPE="q8_0"
ollama serve
```

💡 Why: This significantly reduces VRAM usage, leaving more headroom for the Agent's long context.

3. Download & Verify Model

```powershell
ollama run qwen3.5:9b
```

Type hello in the terminal. If it responds, the model is successfully deployed. Type /bye to exit.

---

Phase 2: Resolving pnpm Dependency Hell (DSH Prerequisites)

DSH's plugin system heavily relies on pnpm. Recent versions of pnpm have strict security policies that block unapproved build scripts, which must be manually allowed.

1. Install pnpm globally

```powershell
npm install -g pnpm
```

2. Fix the ERR_PNPM_IGNORED_BUILDS Error
Navigate to the DSH profile directory and force-write the whitelist to allow @google/genai and protobufjs:

```powershell
# Navigate to DSH dependency directory
cd "$env:USERPROFILE\.dsh\profiles\web"

# Write whitelist configuration
Set-Content -Path "pnpm-workspace.yaml" -Value "allowBuilds:`n  '@google/genai': true`n  'protobufjs': true"

# Clean conflicting dependencies and reinstall
Remove-Item -Recurse -Force node_modules, pnpm-lock.yaml -ErrorAction SilentlyContinue
pnpm install --config.confirm-modules-purge=false
```

---

Phase 3: DSH Framework Installation & Plugin Mounting

1. Install DSH globally and start

```powershell
npm install -g @deepseek-ai/dsh
```

2. Install the Ollama integration plugin
Run the following in your terminal (using --force to avoid version conflicts):

```powershell
dsh plugin --profile web add --force https://github.com/NOirBRight/dsh-llm-ollama/releases/download/v0.6.28/dsh-llm-ollama-0.6.28.tgz
```

3. Start the Web Service

```powershell
dsh web
```

⚠️ Warning: DO NOT close this terminal window; it hosts the Agent's backend engine. Access the UI at http://127.0.0.1:3080.

---

Phase 4: Core Configuration Modification to Connect Qwen

Configuring directly through the Web UI may trigger an "API Key missing" error. The most stable approach is to modify the underlying configuration file.

1. Open the config file
Click Open Config File in the top right of the DSH web UI (or open C:\Users\asus\.dsh\profiles\web\cordis.patch.yml in VS Code).

2. Overwrite with the core configuration
Clear any redundant settings and paste the following code:

```yaml
- id: ui-settings-general
  name: "@deepseek-ai/dsh-client-ui-settings-general"
  config:
    welcomeNoticeVersion: 2026-09-28.1

- id: llm-pi-ai
  config:
    providers:
      local-ollama:
        api: openai-completions
        baseURL: http://127.0.0.1:11434/v1
        apiKey: ollama # Dummy key to bypass strict validation for local services
        models:
          - id: qwen3.5:9b

- id: agent-default-model
  config:
    provider: local-ollama
    model: qwen3.5:9b
```

💡 After saving the file, DSH will hot-reload automatically. Refresh the web page. If qwen3.5:9b appears in the bottom-right model selector, the connection is successful.

---

Phase 5: Agent Sandbox Practice & Pitfall Avoidance

1. Create a Safe Sandbox
NEVER let a newly configured Agent operate on important project code. Create a blank folder in your terminal:

```powershell
mkdir D:\agent-lab
```

2. Issue the Prompt
Switch your workspace to D:\agent-lab in DSH, select qwen3.5:9b, and send:

Create a new file test_agent.py in the current workspace. Write a Python script to print the squares of numbers from 1 to 10, then execute it using the terminal.

3. Handling "Context Amnesia"

· Symptom: After writing 200+ lines of code, the 9B model exhausts its context window (limited by 8GB VRAM), leading to hallucinations or forgetting previous instructions.
· Engineering Fallback: Don't struggle with the Agent's memory. Since the file has already been created via tool calls, simply execute python test_agent.py in your local terminal to verify the actual runtime output.

---

📝 Core Engineering Takeaways

1. Environment Variables are the First Hurdle: When dealing with multiple terminals, always use User-level environment variables to prevent environment fragmentation between Admin and Standard users.
2. Dependency Conflicts are the Norm: When facing pnpm's ERR_PNPM_IGNORED_BUILDS, the most robust solution is to clean node_modules + modify pnpm-workspace.yaml whitelist + run pnpm install.
3. Boundaries are Crucial for Small Models: On 8GB VRAM, the context window must be strictly controlled (e.g., limit to 8192) to prevent OOM (Out of Memory) crashes. Accept that it will "forget" during long tasks and use step-by-step instructions instead of long, complex prompts.
4. Security First: Agents have real permissions to write files and execute terminal commands. Always test new prompts or models in a sandbox directory like agent-lab.