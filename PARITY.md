# PARITY GAP ANALYSIS

Scope: read-only comparison between the original TypeScript source at `/home/bellman/Workspace/claw-code/src/` and the Rust port under `rust/crates/`.

Method: compared feature surfaces, registries, entrypoints, and runtime plumbing only. No TypeScript source was copied.

## Executive summary

The Rust port has a good foundation for:
- Anthropic API/OAuth basics
- local conversation/session state
- a core tool loop
- MCP stdio/bootstrap support
- CLAW.md discovery
- a small but usable built-in tool set

It is **not feature-parity** with the TypeScript CLI, but the Rust port is materially further along than an MVP-only reading would suggest.

Largest remaining gaps:
- **CLI breadth** is still narrower than TS overall
- **skills** are still local-file centric, without full TS registry/bundled pipelines
- **assistant orchestration** still lacks TS structured/remote transports and richer background-task/session integration
- **services** beyond core API/OAuth/MCP remain thinner than TS
- **tool and workflow surface** is still smaller than the TS implementation

---

## tools/

### TS exists
Evidence:
- `src/tools/` contains broad tool families including `AgentTool`, `AskUserQuestionTool`, `BashTool`, `ConfigTool`, `FileReadTool`, `FileWriteTool`, `GlobTool`, `GrepTool`, `LSPTool`, `ListMcpResourcesTool`, `MCPTool`, `McpAuthTool`, `ReadMcpResourceTool`, `RemoteTriggerTool`, `ScheduleCronTool`, `SkillTool`, `Task*`, `Team*`, `TodoWriteTool`, `ToolSearchTool`, `WebFetchTool`, `WebSearchTool`.
- Tool execution/orchestration is split across `src/services/tools/StreamingToolExecutor.ts`, `src/services/tools/toolExecution.ts`, `src/services/tools/toolHooks.ts`, and `src/services/tools/toolOrchestration.ts`.

### Rust exists
Evidence:
- Tool registry is centralized in `rust/crates/tools/src/lib.rs` via `mvp_tool_specs()`.
- Current built-ins include shell/file/search/web/todo/skill/agent/config/notebook/repl/powershell primitives.
- Runtime execution is wired through `rust/crates/tools/src/lib.rs` and `rust/crates/runtime/src/conversation.rs`.

### Missing or broken in Rust
- No Rust equivalents for major TS tools such as `AskUserQuestionTool`, `LSPTool`, `ListMcpResourcesTool`, `MCPTool`, `McpAuthTool`, `ReadMcpResourceTool`, `RemoteTriggerTool`, `ScheduleCronTool`, `Task*`, `Team*`, and several workflow/system tools.
- Rust tool surface is still explicitly an MVP registry, not a parity registry.
- Rust lacks TS’s layered tool orchestration split.

**Status:** partial core only.

---

## hooks/

### TS exists
Evidence:
- Hook command surface under `src/commands/hooks/`.
- Runtime hook machinery in `src/services/tools/toolHooks.ts` and `src/services/tools/toolExecution.ts`.
- TS supports `PreToolUse`, `PostToolUse`, and broader hook-driven behaviors configured through settings and documented in `src/skills/bundled/updateConfig.ts`.

### Rust exists
Evidence:
- Hook config is parsed and merged in `rust/crates/runtime/src/config.rs`.
- Hook config can be inspected via Rust config reporting in `rust/crates/commands/src/lib.rs` and `rust/crates/claw-cli/src/main.rs`.
- Prompt guidance mentions hooks in `rust/crates/runtime/src/prompt.rs`.
- Hook execution is wired into the runtime loop in `rust/crates/runtime/src/conversation.rs`.
- Rust now has a `/hooks` command in `rust/crates/commands/src/lib.rs` and `rust/crates/claw-cli/src/main.rs`.

### Missing or broken in Rust
- No PreToolUse/PostToolUse mutation/deny/rewrite/result-hook behavior.
- Hook behavior is simpler than TS: deny/warn/report flows exist, but there is no full TS-style mutation/rewrite pipeline.

**Status:** implemented core hooks with lighter behavior than TS.

---

## plugins/

### TS exists
Evidence:
- Built-in plugin scaffolding in `src/plugins/builtinPlugins.ts` and `src/plugins/bundled/index.ts`.
- Plugin lifecycle/services in `src/services/plugins/PluginInstallationManager.ts` and `src/services/plugins/pluginOperations.ts`.
- CLI/plugin command surface under `src/commands/plugin/` and `src/commands/reload-plugins/`.

### Rust exists
Evidence:
- Dedicated Rust plugin subsystem in `rust/crates/plugins/`.
- Plugin manifests, install/update/enable/disable flows, and runtime aggregation are implemented across `rust/crates/plugins/src/lib.rs`, `rust/crates/commands/src/lib.rs`, and `rust/crates/claw-cli/src/main.rs`.
- Plugin-provided tools and hooks are surfaced into the runtime and CLI.

### Missing or broken in Rust
- No full TS-equivalent marketplace/service ecosystem.
- No `/reload-plugins` parity command.
- Plugin extension surface is real, but still smaller than TS in breadth and lifecycle richness.

**Status:** substantial local plugin platform exists; not TS-complete.

---

## skills/ and CLAW.md discovery

### TS exists
Evidence:
- Skill loading/registry pipeline in `src/skills/loadSkillsDir.ts`, `src/skills/bundledSkills.ts`, and `src/skills/mcpSkillBuilders.ts`.
- Bundled skills under `src/skills/bundled/`.
- Skills command surface under `src/commands/skills/`.

### Rust exists
Evidence:
- `Skill` tool in `rust/crates/tools/src/lib.rs` resolves and reads local `SKILL.md` files.
- CLAW.md discovery is implemented in `rust/crates/runtime/src/prompt.rs`.
- Rust supports `/memory` and `/init` via `rust/crates/commands/src/lib.rs` and `rust/crates/claw-cli/src/main.rs`.

### Missing or broken in Rust
- No bundled skill registry equivalent.
- No `/skills` command.
- No MCP skill-builder pipeline.
- No TS-style live skill discovery/reload/change handling.
- No comparable session-memory / team-memory integration around skills.

**Status:** basic local skill loading only.

---

## cli/

### TS exists
Evidence:
- Large command surface under `src/commands/` including `agents`, `hooks`, `mcp`, `memory`, `model`, `permissions`, `plan`, `plugin`, `resume`, `review`, `skills`, `tasks`, and many more.
- Structured/remote transport stack in `src/cli/structuredIO.ts`, `src/cli/remoteIO.ts`, and `src/cli/transports/*`.
- CLI handler split in `src/cli/handlers/*`.

### Rust exists
Evidence:
- Shared slash command registry in `rust/crates/commands/src/lib.rs`.
- Rust slash commands now cover `help`, `status`, `compact`, `model`, `permissions`, `clear`, `cost`, `resume`, `config`, `hooks`, `memory`, `init`, `diff`, `version`, `export`, `session`, `agents`, `skills`, and `plugin`.
- Main CLI/repl/prompt handling lives in `rust/crates/claw-cli/src/main.rs`.

### Missing or broken in Rust
- Missing major TS command families such as `/mcp`, `/plan`, `/review`, `/tasks`, and many others.
- No Rust equivalent to TS structured IO / remote transport layers.
- No TS-style handler decomposition for auth/plugins/MCP/agents.
- JSON prompt mode is improved on this branch, but still not clean transport parity: empirical verification shows tool-capable JSON output can emit human-readable tool-result lines before the final JSON object.

**Status:** functional local CLI core, much narrower than TS.

---

## assistant/ (agentic loop, streaming, tool calling)

### TS exists
Evidence:
- Assistant/session surface at `src/assistant/sessionHistory.ts`.
- Tool orchestration in `src/services/tools/StreamingToolExecutor.ts`, `src/services/tools/toolExecution.ts`, `src/services/tools/toolOrchestration.ts`.
- Remote/structured streaming layers in `src/cli/structuredIO.ts` and `src/cli/remoteIO.ts`.

### Rust exists
Evidence:
- Core loop in `rust/crates/runtime/src/conversation.rs`.
- Stream/tool event translation in `rust/crates/claw-cli/src/main.rs`.
- Session persistence in `rust/crates/runtime/src/session.rs`.

### Missing or broken in Rust
- Hook-aware orchestration exists, but it is still less capable than the TS stack.
- No TS structured/remote assistant transport stack.
- No richer TS assistant/session-history/background-task integration.
- JSON output path is no longer single-turn only on this branch, but output cleanliness still lags TS transport expectations.

**Status:** strong core loop, missing orchestration layers.

---

## services/ (API client, auth, models, MCP)

### TS exists
Evidence:
- API services under `src/services/api/*`.
- OAuth services under `src/services/oauth/*`.
- MCP services under `src/services/mcp/*`.
- Additional service layers for analytics, prompt suggestion, session memory, plugin operations, settings sync, policy limits, team memory sync, notifier, voice, and more under `src/services/*`.

### Rust exists
Evidence:
- Core Anthropic API client in `rust/crates/api/src/{client,error,sse,types}.rs`.
- OAuth support in `rust/crates/runtime/src/oauth.rs`.
- MCP config/bootstrap/client support in `rust/crates/runtime/src/{config,mcp,mcp_client,mcp_stdio}.rs`.
- Usage accounting in `rust/crates/runtime/src/usage.rs`.
- Remote upstream-proxy support in `rust/crates/runtime/src/remote.rs`.

### Missing or broken in Rust
- Most TS service ecosystem beyond core messaging/auth/MCP is absent.
- No TS-equivalent plugin service layer.
- No TS-equivalent analytics/settings-sync/policy-limit/team-memory subsystems.
- No TS-style MCP connection-manager/UI layer.
- Model/provider ergonomics remain thinner than TS.

**Status:** core foundation exists; broader service ecosystem missing.

---

## Critical bug status in this worktree

### Fixed
- **Prompt mode tools enabled**
  - `rust/crates/claw-cli/src/main.rs` now constructs prompt mode with `LiveCli::new(model, true, ...)`.
- **Default permission mode = DangerFullAccess**
  - Runtime default now resolves to `DangerFullAccess` in `rust/crates/claw-cli/src/main.rs`.
  - Clap default also uses `DangerFullAccess` in `rust/crates/claw-cli/src/args.rs`.
  - Init template writes `dontAsk` in `rust/crates/claw-cli/src/init.rs`.
- **Streaming `{}` tool-input prefix bug**
  - `rust/crates/claw-cli/src/main.rs` now strips the initial empty object only for streaming tool input, while preserving legitimate `{}` in non-stream responses.
- **Unlimited max_iterations**
  - Verified at `rust/crates/runtime/src/conversation.rs` with `usize::MAX`.

### Remaining notable parity issue
- **JSON prompt output cleanliness**
  - Tool-capable JSON mode now loops, but empirical verification still shows pre-JSON human-readable tool-result output when tools fire.
