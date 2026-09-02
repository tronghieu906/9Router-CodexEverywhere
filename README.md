# 🚀 9Router + CodexEverywhere

Work on my machine™

A guide for routing **CodexEverywhere** models (e.g., `gpt-5.6-terra`, `gpt-5.6-sol`, `gpt-5.6-luna`) through **9Router** into the **OpenAI Codex Desktop App**.

> [!WARNING]
> **Security & Isolation Note:**
> Using third-party / unofficial API providers can carry security and privacy risks. Routing traffic through your local **9Router** acts as an isolation barrier: it protects your real OpenAI session tokens, blocks telemetry leakage, and keeps third-party keys out of your main environment.

---

## 🛠️ Step-by-Step Setup

### Step 1: Configure 9Router Provider Node

In your 9Router Web Dashboard (`http://localhost:20128`):

1. Go to **Providers** -> **Add Custom Provider** (OpenAI-compatible).
2. **Name:** `CodexEverywhere`
3. **Prefix:** `ce`
4. **Base URL:** `https://codex-easy.ai/v1`
5. **API Type / Protocol:** **`Chat`** *(Crucial: Do NOT select 'Responses')*
6. **API Key:** `<YOUR_CODEX_EVERYWHERE_API_KEY>`

---

### Step 2: Configure Codex (`config.toml`)

File location:
- **Windows:** `%USERPROFILE%\.codex\config.toml`
- **macOS / Linux:** `~/.codex/config.toml`

```toml
model_provider = "9router"
model = "ce/gpt-5.6-terra" // YOUR PREFERED MODEL
model_catalog_json = 'C:\Users\<YOUR_USERNAME>\.codex\model_catalog.json'
requires_openai_auth = false

[model_providers.9router]
name = "9Router"
base_url = "http://127.0.0.1:20128/v1"
wire_api = "responses"
requires_openai_auth = false

[model_providers.9router.http_headers]
Authorization = "Bearer <YOUR_9ROUTER_LOCAL_KEY>"

[agents]
default_subagent_model = "ce/gpt-5.6-terra"
```

---

### Step 3: Custom Model Catalog (`model_catalog.json`)

File location:
`%USERPROFILE%\.codex\model_catalog.json`

> [!IMPORTANT]
> **What is MUST vs. What is CUSTOMIZABLE:**
> Codex's internal Rust deserializer strictly requires all **11 keys** below. If any key is missing, Codex will crash on startup with `"Windows setup didn't finish • config_load"`.
> 
> * **DO NOT delete any lines.**
> * **Only modify the values marked with `[CUSTOMIZABLE]` comments below.**
> *(Note: Standard JSON does not support comments; remove `// ...` comments before saving into your `model_catalog.json`)*.

```jsonc
{
  "models": [
    {
      "slug": "ce/gpt-5.6-terra",                 // [CUSTOMIZABLE] Model ID with prefix (e.g. ce/<model-name>)
      "display_name": "GPT-5.6 Terra",            // [CUSTOMIZABLE] Clean label shown in Codex UI dropdown
      "supported_reasoning_levels": [             // [MANDATORY BOILERPLATE - DO NOT REMOVE]
        { "effort": "low", "description": "low" },
        { "effort": "medium", "description": "medium" },
        { "effort": "high", "description": "high" }
      ],
      "shell_type": "unified_exec",               // [MANDATORY] Must remain "unified_exec"
      "visibility": "list",                       // [MANDATORY] Must remain "list"
      "supported_in_api": true,                   // [MANDATORY] Must remain true
      "priority": 1,                              // [CUSTOMIZABLE] Dropdown sort order (1 = top)
      "model_messages": {                         // [MANDATORY BOILERPLATE - DO NOT REMOVE]
        "instructions_template": "You are Codex, a coding assistant."
      },
      "support_verbosity": true,                  // [MANDATORY] Must remain true
      "truncation_policy": { "mode": "tokens", "limit": 10000 }, // [MANDATORY]
      "experimental_supported_tools": []          // [MANDATORY] Must remain empty array []
    },
    {
      "slug": "ce/gpt-5.6-sol",                   // [CUSTOMIZABLE]
      "display_name": "GPT-5.6 Sol",              // [CUSTOMIZABLE]
      "supported_reasoning_levels": [
        { "effort": "low", "description": "low" },
        { "effort": "medium", "description": "medium" },
        { "effort": "high", "description": "high" }
      ],
      "shell_type": "unified_exec",
      "visibility": "list",
      "supported_in_api": true,
      "priority": 2,                              // [CUSTOMIZABLE]
      "model_messages": {
        "instructions_template": "You are Codex, a coding assistant."
      },
      "support_verbosity": true,
      "truncation_policy": { "mode": "tokens", "limit": 10000 },
      "experimental_supported_tools": []
    },
    {
      "slug": "ce/gpt-5.6-luna",                  // [CUSTOMIZABLE]
      "display_name": "GPT-5.6 Luna",             // [CUSTOMIZABLE]
      "supported_reasoning_levels": [
        { "effort": "low", "description": "low" },
        { "effort": "medium", "description": "medium" },
        { "effort": "high", "description": "high" }
      ],
      "shell_type": "unified_exec",
      "visibility": "list",
      "supported_in_api": true,
      "priority": 3,                              // [CUSTOMIZABLE]
      "model_messages": {
        "instructions_template": "You are Codex, a coding assistant."
      },
      "support_verbosity": true,
      "truncation_policy": { "mode": "tokens", "limit": 10000 },
      "experimental_supported_tools": []
    }
  ]
}
```

---

### Step 4: Lock Down Auth (`auth.json`)

File location:
`%USERPROFILE%\.codex\auth.json`

Set your 9Router local key here to prevent Codex from making direct unproxied cloud requests:

```json
{
  "OPENAI_API_KEY": "<YOUR_9ROUTER_LOCAL_KEY>"
}
```

---

## 🕵️‍♂️ The "Dual-Agent" Mystery: Why 9Router Logs Two Models per Prompt

When testing a model like **`GPT-5.6 Terra`**, you might notice both **`gpt-5.6-terra`** AND **`gpt-5.6-luna`** appearing in 9Router's Recent Requests table simultaneously. 

### Why this happens:
On every single prompt, Codex Desktop dispatches **TWO parallel requests**:
1. **Primary Turn (User Prompt):** Runs on whichever model you selected in the UI dropdown (e.g. `GPT-5.6 Terra`).
2. **Background Helper Agent:** Codex automatically triggers a background agent to generate **thread titles**, **ambient suggestions**, and **memory summaries**. This background agent uses the default model set in `config.toml` (`default_subagent_model` / `model`).

---

## ⚡ Common Errors & Fixes Reference Table

| Issue / Error | Root Cause | Fix |
| :--- | :--- | :--- |
| **`400: Bad Request (upstream_error)`** | Upstream only supports `/v1/chat/completions`, but node was set to `Responses`. | Set **API Type** to **`Chat`** in 9Router node settings. Keep `wire_api = "responses"` in Codex. |
| **App stuck on *"Windows setup didn't finish • config_load"*** | Incomplete `model_catalog.json` schema missing any of the 11 required fields. | Use the 11-field schema from Step 3. Verify with `codex doctor`. |
| **Model shows as generic `"Custom"` in UI** | Model slug is missing from `model_catalog.json`. | Add model slug with `"visibility": "list"` into `model_catalog.json`. |
| **Direct cloud requests bypassing 9Router** | Third-party key still present in `auth.json`. | Replace key in `auth.json` with your local 9Router key. |
| **Recent Requests shows unexpected model (e.g. Luna)** | Codex background subagent generated titles/suggestions using default model. | Set `default_subagent_model` in `config.toml` to match your primary model. |
