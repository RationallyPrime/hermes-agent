# Sokrates Fork Inventory

Fork base: `NousResearch/hermes-agent` at `7ad47ace51ef28ad613e5f7152a9d0401061636e`.

This inventory captures the upstream layout before Sokrates-specific trimming.

## Providers found

Hermes does not have a `src/hermes_agent/providers/` package. Provider support is registry-driven:

- `hermes_cli/auth.py` has the operational `PROVIDER_REGISTRY` for auth and runtime URLs.
- `hermes_cli/providers.py` merges models.dev provider metadata with Hermes overlays.
- `hermes_cli/runtime_provider.py`, `agent/auxiliary_client.py`, and `agent/credential_pool.py` consume those registries.
- `hermes_constants.py` defines the OpenRouter base URL.

Provider identities found in Hermes-owned registries:

- `openrouter` - OpenAI-compatible aggregator and primary inference path.
- `anthropic` - native Anthropic Messages API path.
- `openai-codex` - OpenAI Codex / Responses API OAuth path.
- `nous` - Nous Portal OAuth device-code path.
- `qwen-oauth` - Qwen OAuth path.
- `copilot`, `copilot-acp`, `github-copilot` - GitHub Copilot API and ACP paths.
- `gemini` - Google AI Studio OpenAI-compatible endpoint.
- `zai` - Z.AI / GLM OpenAI-compatible endpoint.
- `kimi-coding`, `kimi-coding-cn`, `kimi-for-coding` - Kimi / Moonshot endpoints.
- `minimax`, `minimax-cn` - MiniMax Anthropic-compatible endpoints.
- `deepseek` - DeepSeek OpenAI-compatible endpoint.
- `alibaba` - Alibaba Cloud DashScope endpoint.
- `arcee` - Arcee AI endpoint.
- `xai` - xAI endpoint.
- `ai-gateway`, `vercel` - Vercel AI Gateway.
- `opencode`, `opencode-zen`, `opencode-go` - OpenCode gateway endpoints.
- `kilocode`, `kilo` - Kilo Code gateway endpoint.
- `huggingface` - Hugging Face router endpoint.
- `xiaomi` - Xiaomi MiMo endpoint.
- `custom`, local aliases (`lmstudio`, `ollama`, `vllm`, `llama.cpp`) - user-defined or local OpenAI-compatible endpoints.

Candidate trim targets are the registry entries outside Sokrates' preserve set: OpenRouter, Anthropic, and OpenAI Codex. This trim is not a directory deletion; it requires updating registries, model setup flows, docs/tests, and provider fallback code.

## Platform adapters found

Adapters live in `gateway/platforms/` and are enumerated in `hermes_cli/platforms.py`:

- `api_server.py` - OpenAI-compatible HTTP gateway.
- `bluebubbles.py` - BlueBubbles/iMessage bridge.
- `dingtalk.py` - DingTalk bot.
- `discord.py` - Discord bot.
- `email.py` - IMAP/SMTP email gateway.
- `feishu.py` - Feishu/Lark bot.
- `homeassistant.py` - Home Assistant conversation gateway.
- `matrix.py` - Matrix bot.
- `mattermost.py` - Mattermost bot.
- `qqbot.py` - QQ official bot API.
- `signal.py` - Signal bot via signal-cli-rest-api.
- `slack.py` - Slack bot.
- `sms.py` - SMS/Twilio webhook.
- `telegram.py`, `telegram_network.py` - Telegram bot plus networking fallback helpers.
- `webhook.py` - generic webhook ingress.
- `wecom.py`, `wecom_callback.py`, `wecom_crypto.py` - WeCom integrations.
- `weixin.py` - Weixin/iLink integration.
- `whatsapp.py` - WhatsApp bridge integration.

Preserve per Sokrates phase-0 requirements: Discord, Slack, Telegram, WhatsApp, and Signal. Other adapters are candidate trims only if the registry, CLI, docs, and tests can be updated cleanly.

## Tools / toolsets found

Tool registration is centralized through `tools/registry.py`; `model_tools.py` imports tool modules to self-register. Tool modules found:

- Web/search: `tools/web_tools.py`.
- Terminal/process: `tools/terminal_tool.py`, `tools/process_registry.py`.
- File operations: `tools/file_tools.py`, `tools/file_operations.py`, `tools/patch_parser.py`, `tools/path_security.py`.
- Vision/image generation: `tools/vision_tools.py`, `tools/image_generation_tool.py`.
- Browser automation: `tools/browser_tool.py`, `tools/browser_camofox.py`, `tools/browser_camofox_state.py`, `tools/browser_providers/*`.
- Skills: `tools/skills_tool.py`, `tools/skill_manager_tool.py`, `tools/skills_guard.py`, `tools/skills_hub.py`, `tools/skills_sync.py`.
- MCP: `tools/mcp_tool.py`, `tools/mcp_oauth.py`, `mcp_serve.py`.
- RL training: `tools/rl_training_tool.py`.
- Text/voice: `tools/tts_tool.py`, `tools/transcription_tools.py`, `tools/voice_mode.py`, `tools/neutts_synth.py`.
- Memory/session: `tools/memory_tool.py`, `tools/session_search_tool.py`.
- Messaging: `tools/send_message_tool.py`.
- Planning/clarification: `tools/todo_tool.py`, `tools/clarify_tool.py`.
- Code execution/delegation: `tools/code_execution_tool.py`, `tools/delegate_tool.py`.
- Cron: `tools/cronjob_tools.py`.
- Home Assistant: `tools/homeassistant_tool.py`.
- Security/support: `tools/osv_check.py`, `tools/tirith_security.py`, `tools/url_safety.py`, `tools/website_policy.py`, `tools/credential_files.py`, `tools/env_passthrough.py`, `tools/tool_backend_helpers.py`, `tools/tool_result_storage.py`, `tools/managed_tool_gateway.py`.

Toolsets live in `toolsets.py`. Named toolsets include `web`, `search`, `vision`, `image_gen`, `terminal`, `moa`, `skills`, `browser`, `cronjob`, `messaging`, `rl`, `file`, `tts`, `todo`, `memory`, `session_search`, `clarify`, `code_execution`, `delegation`, `homeassistant`, scenario presets, `hermes-cli`, `hermes-acp`, `hermes-api-server`, and per-platform `hermes-*` toolsets.

## Memory providers found

Memory providers live under `plugins/memory/`:

- `byterover` - ByteRover memory plugin.
- `hindsight` - Hindsight memory plugin.
- `holographic` - local holographic memory implementation.
- `honcho` - Honcho memory plugin and CLI/client/session helpers.
- `mem0` - Mem0 memory plugin.
- `openviking` - OpenViking memory plugin.
- `retaindb` - RetainDB memory plugin.
- `supermemory` - Supermemory plugin.

All memory providers are preserved per the phase-0 runbook.

## Gateways found

Gateway core lives in `gateway/`:

- `run.py` - gateway runner.
- `config.py`, `display_config.py`, `channel_directory.py` - gateway configuration and channel metadata.
- `delivery.py`, `mirror.py`, `stream_consumer.py` - delivery and stream handling.
- `session.py`, `session_context.py`, `status.py` - session state.
- `hooks.py`, `builtin_hooks/boot_md.py` - hook support.
- `pairing.py`, `restart.py`, `sticker_cache.py` - operational helpers.
- `platforms/` - inbound channel adapters listed above.

## Environments found

Training and evaluation environments live in `environments/` and `tools/environments/`. These are **PRESERVE - strategic payoff #4**.

- `environments/agent_loop.py` - OpenAI-spec tool-calling loop for environment rollouts.
- `environments/hermes_base_env.py` - base environment for Atropos/Tinker RL integration.
- `environments/agentic_opd_env.py` - on-policy distillation environment.
- `environments/web_research_env.py` - FRAMES-style web research environment.
- `environments/hermes_swe_env/` - SWE environment.
- `environments/terminal_test_env/` - terminal test environment.
- `environments/benchmarks/tblite/` - Terminal-Bench Lite benchmark.
- `environments/benchmarks/terminalbench_2/` - Terminal-Bench 2 benchmark.
- `environments/benchmarks/yc_bench/` - YC Bench benchmark.
- `environments/tool_call_parsers/` - raw model tool-call parsers for DeepSeek, GLM, Hermes, Kimi, Llama, LongCat, Mistral, and Qwen families.
- `tools/environments/` - local, Docker, SSH, Modal, managed Modal, Daytona, and Singularity execution backends used by tools/training.
- `rl_cli.py`, `batch_runner.py`, `trajectory_compressor.py`, and `scripts/sample_and_compress.py` support RL/SFT data generation and trajectory compression.

## Skills found

Primary skills live in `skills/`, with additional optional skills in `optional-skills/`. Top-level skill families found:

- `apple`, `autonomous-ai-agents`, `creative`, `data-science`, `devops`, `diagramming`, `dogfood`, `domain`, `email`, `feeds`, `gaming`, `gifs`, `github`, `inference-sh`, `leisure`, `mcp`, `media`, `mlops`, `note-taking`, `productivity`, `red-teaming`, `research`, `smart-home`, `social-media`, `software-development`.
- `optional-skills` includes `autonomous-ai-agents`, `communication`, `health`, `mcp`, `migration`, and `security`.
- `skills/index-cache/` contains prebuilt marketplace indexes.

The skills system is preserved per the phase-0 runbook.

## Demos / examples found

No top-level `examples/`, `demos/`, or `tutorials/` directories were found. Candidate demo/example data that may be trim-safe:

- `datagen-config-examples/` - sample browser and web-research data generation configs.
- `docs/skins/example-skin.yaml` - example skin config.
- `tools/neutts_samples/` - sample audio assets for NeuTTS.
- `.plans/` and `plans/` - upstream planning notes; not runtime code.

Do not remove docs or website content unless a later pass confirms it is not referenced by packaging/tests.

## Browser automation found

Browser automation is present:

- `tools/browser_tool.py`
- `tools/browser_camofox.py`
- `tools/browser_camofox_state.py`
- `tools/browser_providers/base.py`
- `tools/browser_providers/browser_use.py`
- `tools/browser_providers/browserbase.py`
- `tools/browser_providers/firecrawl.py`
- `toolsets.py` includes browser tools in `_HERMES_CORE_TOOLS`, `browser`, `hermes-cli`, `hermes-acp`, and `hermes-api-server`.
- `model_tools.py` imports `tools.browser_tool` during discovery.
- `website/docs/user-guide/features/browser.md` documents the browser feature.
- `datagen-config-examples/example_browser_tasks.jsonl` and `datagen-config-examples/run_browser_tasks.sh` are browser-oriented examples.

This is a candidate trim surface, but it is wired into core toolsets and documentation, so remove it only with corresponding registry/toolset/test updates.

## Optional extras in pyproject.toml

Optional extras found:

- `modal`
- `daytona`
- `dev`
- `messaging`
- `cron`
- `slack`
- `matrix`
- `cli`
- `tts-premium`
- `voice`
- `pty`
- `honcho`
- `mcp`
- `homeassistant`
- `sms`
- `acp`
- `mistral`
- `termux`
- `dingtalk`
- `feishu`
- `web`
- `rl`
- `yc-bench`
- `all`

The `[web]` extra contains `fastapi` and `uvicorn[standard]`. The `[rl]` extra also contains `fastapi` and `uvicorn[standard]`. The `[all]` meta-extra includes `[web]`.

## Website / docs structure

Docs and web surfaces are preserved:

- `website/` - Docusaurus documentation site with `website/docs/`, `website/src/`, `website/static/`, and npm metadata.
- `web/` - local web dashboard frontend with Vite/React.
- `docs/` - internal/upstream docs, migrations, plans, specs, and skins.
- `README.md`, `AGENTS.md`, `CONTRIBUTING.md`, and release notes are kept for now.

## Trim notes

The most separable cut candidates are demo/sample files and browser automation only if toolset registration is updated in the same commit. Provider trimming is not separable by directory; it must be done by carefully narrowing provider registries and all flows that enumerate providers.
