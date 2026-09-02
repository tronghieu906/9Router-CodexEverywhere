# 🚀 9Router + OpenAI Codex Desktop Integration Guide

A complete setup guide, configuration reference, and troubleshooting guide for connecting custom LLM providers and models through [9Router](https://github.com/decolua/9router) into the **OpenAI Codex Desktop App** (and Codex CLI).

---

## 🏗️ Architecture Overview

Codex Desktop and 9Router work together as a translation bridge:

```text
┌─────────────────────────┐
│   Codex Desktop App     │ (wire_api = "responses")
└────────────┬────────────┘
             │ http://127.0.0.1:20128/v1
             ▼
┌─────────────────────────┐
│     9Router Gateway     │ (Translates Responses API -> Chat Completions)
└────────────┬────────────┘
             │ POST /v1/chat/completions
             ▼
┌─────────────────────────┐
│  Upstream Provider API  │ (e.g., CodexEverywhere, OpenRouter, DeepSeek, etc.)
└─────────────────────────┘
```

---

## ⚙️ Working Configuration Files

### 1. Codex Configuration (`~/.codex/config.toml`)

Location:
- **Windows:** `%USERPROFILE%\.codex\config.toml` (e.g. `C:\Users\<Username>\.codex\config.toml`)
- **macOS / Linux:** `~/.codex/config.toml`

```toml
model_provider = "9router"
model = "ce/gpt-5.6-terra"
model_catalog_json = 'C:\Users\<Username>\.codex\model_catalog.json'
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

### 2. Custom Model Catalog (`~/.codex/model_catalog.json`)

To display clean, customized model names in the Codex UI dropdown (instead of a generic `"Custom"` label), define your models in `model_catalog.json`.

> **Important:** Codex uses strict internal Rust deserialization. Each model object **must** include `supported_reasoning_levels`, `shell_type`, `priority`, and instruction template metadata to avoid crashing the startup routine.

```json
{
  "fetched_at": "2026-09-02T04:10:43.683056900Z",
  "client_version": "0.152.1",
  "models": [
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
      "priority": 1,
      "additional_speed_tiers": [],
      "service_tiers": [],
      "availability_nux": null,
      "upgrade": null,
      "include_skills_usage_instructions": false,
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
      "priority": 2,
      "additional_speed_tiers": [],
      "service_tiers": [],
      "availability_nux": null,
      "upgrade": null,
      "include_skills_usage_instructions": false,
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
      "include_skills_usage_instructions": false,
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

### 3. Local Auth Safety Net (`~/.codex/auth.json`)

Prevent Codex from silently making direct cloud fallback requests around 9Router:

```json
{
  "OPENAI_API_KEY": "sk-5e80ddbe7401abd4-3lwsxb-9fbd47b5"
}
```

---

## 🛠️ Hiccups & Solutions Reference

### 1. `400 Bad Request: {"error":{"message":"Bad Request","type":"upstream_error"}}`
- **Root Cause:** Upstream endpoints (like `codex-easy.ai`) only support `/v1/chat/completions` and reject `/v1/responses`.
- **Solution:** In the 9Router Web Dashboard (`http://localhost:20128`), edit your Provider Node and set **API Type** to **`Chat`** (`chat` / `chat/completions`). Keep `wire_api = "responses"` in Codex's `config.toml`. 9Router will handle the protocol translation automatically.

---

### 2. App Stuck on *"Windows setup didn't finish • config_load"*
- **Root Cause:** Incomplete `model_catalog.json` schema. Codex's strict Rust parser requires all model fields (`supported_reasoning_levels`, `shell_type`, `priority`, instruction templates).
- **Solution:** Use the complete `model_catalog.json` structure provided above and run `codex doctor` to verify before launching the desktop GUI.

---

### 3. Multiple Requests Logged per Single Prompt (e.g. Luna + Terra)
- **Root Cause:** Codex Desktop automatically runs background helper tasks for thread title generation, ambient suggestions, and memories using the configured `default_subagent_model` / default `model`.
- **Solution:** Set `default_subagent_model = "ce/gpt-5.6-terra"` in `config.toml` to unify your active and background models.

---

### 4. Direct Cloud Bypasses
- **Root Cause:** Leaving direct cloud API keys in `~/.codex/auth.json` allows Codex to fall back to direct cloud requests during default sessions.
- **Solution:** Replace keys in `auth.json` with your local 9Router key.

---

## 📄 License

MIT License. Free to use and share with the AI coding community!
