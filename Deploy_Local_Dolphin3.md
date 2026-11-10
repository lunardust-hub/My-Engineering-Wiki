Local LLM Deployment for AI Red Teaming (Dolphin3-Cyber-8B)

**🎯 Objective**
Deploy a local, uncensored LLM for AI Red Teaming and CTF payload generation to bypass the safety sandbox of cloud APIs. 

**💻 Hardware Constraints**
- GPU: NVIDIA RTX 5060 Laptop (8GB VRAM)
- RAM: 16GB
- **Model Choice:** `Dolphin3-Cyber-8B` (`Q4_K_M` version, ~4.9GB). This fits perfectly in 8GB VRAM without OOM (Out of Memory).

---

## Phase 1: Network Pitfalls & Proxy Issues (踩坑与网络环境)

*   **The Action:** Tried to pull the model using Ollama CLI via Hugging Face (`ollama run hf.co/...`).
*   **The Problem:** Got `dial tcp i/o timeout` and `TLS internal error` due to the unstable connection to HF in mainland China. 
*   **Attempted Fix:** Tried the domestic mirror (`hf-mirror.com`) and configured Clash proxy (`HTTP_PROXY=http://127.0.0.1:7897`).
*   **The Root Cause:** Ollama has a strict security mechanism. When downloading, HF redirects to a CDN. Ollama resolves this to an IPv6 address (`2001::...`) and flags it as "non-public / redirect target not allowed", refusing to follow it. **Even the `--insecure` flag cannot bypass this.**

## Phase 2: The Ultimate Fix - Manual GGUF Import (手动导入大法)

*   **Step 1: Manual Download (手动下载)**
    Go to `hf-mirror.com` or Hugging Face, manually download the `Q4_K_M.gguf` file (~4.9GB) via IDM or browser. Place it in a clean path (e.g., `D:\AI_Models\dolphin.gguf`).

*   **Step 2: Create a Modelfile (编写说明书)**
    In the same folder, create a file named `Modelfile` (no extension) and write:
    ```text
    FROM ./dolphin.gguf
    ```

*   **Step 3: Import & Run (导入并运行)**
    Open CMD in this folder:
    ```bash
    # Register the model locally
    ollama create dolphin3-cyber -f Modelfile

    # Run it
    ollama run dolphin3-cyber
    ```

## Phase 3: Performance Optimization for 8GB VRAM (显存优化)

Since 8GB VRAM is the bottleneck, need to limit the Context Window to prevent OOM crashes.
- Use `ollama run dolphin3-cyber`
- Use `/set parameter num_ctx 4096` to limit the context length. 

---

## 🚀 Core Engineering Takeaways (核心工程总结)

1. **Never trust the CLI downloader in restricted networks.** When facing IPv6 redirect blocks or proxy issues, manually downloading the `.gguf` file and using `Modelfile` is the most bulletproof method.
2. **Hardware Constraints Drive Architecture:** With 8GB VRAM, stick to 8B parameter models at `Q4_K_M` quantization. 27B models will freeze the system due to RAM/VRAM swapping.
3. **Alternative Tooling:** For beginners or when CLI fails, **LM Studio** (GUI) is a highly recommended fallback. It handles network redirects and model searching seamlessly.
