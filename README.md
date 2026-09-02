# 🚀 9Router + CodexEverywhere Integration Guide

A concise, step-by-step guide for routing **CodexEverywhere** models (e.g., `gpt-5.6-terra`, `gpt-5.6-sol`, `gpt-5.6-luna`) through **9Router** into the **OpenAI Codex Desktop App**.

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
model = "ce/gpt-5.6-terra"
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
default_subagent_model = "ce/gpt-5.6-luna"
```

---

### Step 3: Custom Model Catalog (`model_catalog.json`)

File location:
`%USERPROFILE%\.codex\model_catalog.json`

> [!IMPORTANT]
> **Full Plugin & Node REPL Support:**
> This configuration includes all capability flags (`node_repl_disabled: false`, `include_plugin_usage_instructions: true`, and `include_skills_usage_instructions: true`) ensuring built-in features like Chrome browser control and tool execution function without `unsupported call: mcp__node_repl` errors.

```json
{
  "models": [
    {
      "slug": "ce/gpt-5.6-sol",
      "display_name": "GPT-5.6 Sol",
      "description": "High performance flagship model via 9Router",
      "default_reasoning_level": "high",
      "supported_reasoning_levels": [
        { "effort": "low", "description": "Fast responses with lighter reasoning" },
        { "effort": "medium", "description": "Balanced speed and reasoning depth" },
        { "effort": "high", "description": "Greater reasoning depth for complex problems" },
        { "effort": "xhigh", "description": "Extra high reasoning depth" }
      ],
      "shell_type": "unified_exec",
      "visibility": "list",
      "supported_in_api": true,
      "priority": 1,
      "additional_speed_tiers": [],
      "service_tiers": [],
      "availability_nux": null,
      "upgrade": null,
      "include_skills_usage_instructions": true,
      "include_plugin_usage_instructions": true,
      "include_apps_usage_instructions": true,
      "default_reasoning_summary": "none",
      "support_verbosity": true,
      "default_verbosity": "low",
      "apply_patch_tool_type": "freeform",
      "web_search_tool_type": "text_and_image",
      "truncation_policy": { "mode": "tokens", "limit": 10000 },
      "supports_image_detail_original": true,
      "context_window": 272000,
      "max_context_window": 872000,
      "comp_hash": "3000",
      "effective_context_window_percent": 95,
      "experimental_supported_tools": [],
      "input_modalities": ["text", "image"],
      "supports_search_tool": true,
      "use_responses_lite": true,
      "node_repl_auto_review_required": false,
      "node_repl_disabled": false,
      "tool_mode": "code_mode_only",
      "multi_agent_version": "v2"
    },
    {
      "slug": "ce/gpt-5.6-terra",
      "display_name": "GPT-5.6 Terra",
      "description": "Deep reasoning & complex refactoring via 9Router",
      "default_reasoning_level": "high",
      "supported_reasoning_levels": [
        { "effort": "low", "description": "Fast responses with lighter reasoning" },
        { "effort": "medium", "description": "Balanced speed and reasoning depth" },
        { "effort": "high", "description": "Greater reasoning depth for complex problems" },
        { "effort": "xhigh", "description": "Extra high reasoning depth" }
      ],
      "shell_type": "unified_exec",
      "visibility": "list",
      "supported_in_api": true,
      "priority": 2,
      "additional_speed_tiers": [],
      "service_tiers": [],
      "availability_nux": null,
      "upgrade": null,
      "include_skills_usage_instructions": true,
      "include_plugin_usage_instructions": true,
      "include_apps_usage_instructions": true,
      "default_reasoning_summary": "none",
      "support_verbosity": true,
      "default_verbosity": "low",
      "apply_patch_tool_type": "freeform",
      "web_search_tool_type": "text_and_image",
      "truncation_policy": { "mode": "tokens", "limit": 10000 },
      "supports_image_detail_original": true,
      "context_window": 272000,
      "max_context_window": 872000,
      "comp_hash": "3000",
      "effective_context_window_percent": 95,
      "experimental_supported_tools": [],
      "input_modalities": ["text", "image"],
      "supports_search_tool": true,
      "use_responses_lite": true,
      "node_repl_auto_review_required": false,
      "node_repl_disabled": false,
      "tool_mode": "code_mode_only",
      "multi_agent_version": "v2"
    },
    {
      "slug": "ce/gpt-5.6-luna",
      "display_name": "GPT-5.6 Luna",
      "description": "Fast & intelligent everyday coding via 9Router",
      "default_reasoning_level": "medium",
      "supported_reasoning_levels": [
        { "effort": "low", "description": "Fast responses with lighter reasoning" },
        { "effort": "medium", "description": "Balanced speed and reasoning depth" },
        { "effort": "high", "description": "Greater reasoning depth for complex problems" },
        { "effort": "xhigh", "description": "Extra high reasoning depth" }
      ],
      "shell_type": "unified_exec",
      "visibility": "list",
      "supported_in_api": true,
      "priority": 3,
      "additional_speed_tiers": [],
      "service_tiers": [],
      "availability_nux": null,
      "upgrade": null,
      "include_skills_usage_instructions": true,
      "include_plugin_usage_instructions": true,
      "include_apps_usage_instructions": true,
      "default_reasoning_summary": "none",
      "support_verbosity": true,
      "default_verbosity": "low",
      "apply_patch_tool_type": "freeform",
      "web_search_tool_type": "text_and_image",
      "truncation_policy": { "mode": "tokens", "limit": 10000 },
      "supports_image_detail_original": true,
      "context_window": 272000,
      "max_context_window": 872000,
      "comp_hash": "3000",
      "effective_context_window_percent": 95,
      "experimental_supported_tools": [],
      "input_modalities": ["text", "image"],
      "supports_search_tool": true,
      "use_responses_lite": true,
      "node_repl_auto_review_required": false,
      "node_repl_disabled": false,
      "tool_mode": "code_mode_only",
      "multi_agent_version": "v2"
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

When testing a model like **`GPT-5.6 Terra`**, you might notice both **`gpt-5.6-terra`** AND **`gpt-5.6-luna`** appearing in 9Router's Recent Requests table.

### Why this happens:
1. **Primary Turn (User Prompt):** Runs on whichever model you selected in the UI dropdown (e.g. `GPT-5.6 Terra`).
2. **Background Helper Agent:** Codex automatically triggers a background agent to generate **thread titles**, **ambient suggestions**, and **memory summaries**.
   - Thread titles and ambient indexing query the catalog for **`priority: 1`**.
   - Subagents query `[agents] default_subagent_model`.

---

## ⚡ Common Errors & Fixes Reference Table

| Issue / Error | Root Cause | Fix |
| :--- | :--- | :--- |
| **`400: Bad Request (upstream_error)`** | Upstream only supports `/v1/chat/completions`, but node was set to `Responses`. | Set **API Type** to **`Chat`** in 9Router node settings. Keep `wire_api = "responses"` in Codex. |
| **`unsupported call: mcp__node_repl`** | Custom model catalog omitted `node_repl_disabled: false` or plugin instruction flags. | Use the full-capability catalog schema from Step 3. |
| **App stuck on *"Windows setup didn't finish • config_load"*** | Incomplete `model_catalog.json` schema missing struct keys. | Use the complete JSON schema from Step 3. Verify with `codex doctor`. |
| **Model shows as generic `"Custom"` in UI** | Model slug is missing from `model_catalog.json`. | Add model slug with `"visibility": "list"` into `model_catalog.json`. |
| **Direct cloud requests bypassing 9Router** | Third-party key still present in `auth.json`. | Replace key in `auth.json` with your local 9Router key. |
