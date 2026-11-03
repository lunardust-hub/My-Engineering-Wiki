📘 Workflow Summary: Deploying and Optimizing Ollama on Windows

Phase 1: Initialization & The "Pitfall" (踩坑)

Goal: Install Ollama and run a local LLM (qwen3.5:9b).

· The Action: Ran ollama run qwen3.5:9b in different terminals (Admin vs. Standard).
· The Problem: The model started downloading repeatedly, and the C drive was filling up rapidly.
· The Root Cause:
  1. By default, Ollama stores models in C:\Users\<Username>\.ollama\models.
  2. Environment Variables (环境变量) were not synchronized between Administrator and Standard user terminals, causing the system to treat them as two different environments.

Phase 2: Migration & Troubleshooting (排错与迁移)

Goal: Stop the redundant downloads and move models to drive D.

· Step 1: Kill Background Processes (终止后台进程)
  Get-Process ollama* | Stop-Process -Force 
  (This prevents file-locking issues and stops the incomplete download.)
· Step 2: Set User-level Environment Variables (设置用户级环境变量)
  [Environment]::SetEnvironmentVariable("OLLAMA_MODELS", "D:\OllamaModels", "User")
  (Setting it at the "User" level ensures both Admin and Standard terminals read the same path.)
· Step 3: Clean up C Drive (清理C盘)
  Remove-Item -Path "C:\Users\asus\.ollama\models\*" -Recurse -Force
· Step 4: Restart & Verify (重启并验证)
  Close all terminals, open a new one, and run echo $env:OLLAMA_MODELS to confirm it outputs D:\OllamaModels. Re-run the ollama run command to download the model directly to drive D.

Phase 3: Performance Optimization (性能优化)

Goal: Maximize performance for the RTX 5060 (8GB VRAM).

· Flash Attention: set OLLAMA_FLASH_ATTENTION=1 (Improves processing speed and saves VRAM.)
· KV Cache Quantization: set OLLAMA_KV_CACHE_TYPE=q8_0 (Compresses the memory used for context, allowing longer conversations without running out of VRAM.)
· Note: These must be set before starting ollama serve.

Phase 4: Understanding the "Weird Files" (理解底层原理)

When you looked into D:\OllamaModels, you saw files starting with sha256-.

· What are they? They are Content-Addressable Storage (内容寻址存储).
· How it works: Ollama splits large models into chunks. The filename is the SHA256 hash (digital fingerprint) of the content. This prevents data corruption and allows deduplication (if two models share the same component, it only downloads once).

---

💡 Key Engineering Takeaways (核心工程经验总结)

1. Never Hardcode Paths (永远不要硬编码路径): Always use Environment Variables or configuration files for paths. This makes your system flexible and portable.
2. Understand the Root Cause (刨根问底): The repeated download wasn't just a "glitch"; it was a mismatch in how the OS handles user permissions and environment scopes.
3. Document Your Troubleshooting (记录排错过程): We used GitHub Codespaces to create a Markdown file. In the future, whenever you encounter a similar issue, you just search your own "Personal Knowledge Base" instead of asking AI again.
4. Hardware Constraints Drive Architecture (硬件限制决定架构): With 8GB VRAM, you can't just run any model. You have to use Quantization (量化, like Q4 or q8_0 for KV cache) and limit the Context Window (上下文窗口) to prevent OOM (Out of Memory, 显存溢出) errors.
