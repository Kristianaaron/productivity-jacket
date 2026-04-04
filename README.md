# Productivity Jacket

Productivity Jacket is a local agent harness for coding and productivity workflows.

This repository packages a stronger execution layer around the model: layered prompt construction, merged runtime config, session compaction and resume, hookable tool execution, plugin extensibility, background-agent supervision, and better operator visibility.

The main product surface today is the Rust workspace in [`rust/`](rust/), with supporting Python research and porting utilities in [`src/`](src/).

## What This Setup Has

- Layered prompt assembly from workspace context, instruction files, config, git state, and optional LSP enrichment
- Layered runtime config for models, permissions, sandboxing, hooks, plugins, MCP, and OAuth
- Session compaction with explicit continuation instructions for cleaner long-running continuity
- Hookable tool execution with pre-tool and post-tool policy points
- Plugin-driven extensibility for tools, hooks, commands, and lifecycle behavior
- Background-agent supervision with heartbeats, stale detection, timeouts, and status/wait inspection
- Operator surfaces such as `/status`, `/config`, `/hooks`, `/agents active`, and `/agents runs`
- Stronger Windows support for shell resolution, PowerShell execution, OAuth token generation, and test reliability

## Repository Layout

```text
.
|-- rust/                # Main harness implementation
|   |-- crates/api/      # Provider clients and streaming
|   |-- crates/runtime/  # Prompting, sessions, config, hooks, permissions
|   |-- crates/tools/    # Built-in tool implementations
|   |-- crates/commands/ # Slash command registry and reports
|   |-- crates/plugins/  # Plugin model and bundled plugins
|   |-- crates/claw-cli/ # Interactive CLI / REPL
|   |-- crates/lsp/      # LSP integration
|   `-- crates/server/   # Supporting server surface
|-- src/                 # Python-side research and porting utilities
|-- tests/               # Python verification
|-- CLAW.md              # Project/operator guidance
`-- PARITY.md            # Harness parity notes and audit details
```

## Quick Start

### Prerequisites

- Rust stable toolchain
- Cargo
- Git
- Provider credentials for the models you plan to use

### Authentication

Anthropic-compatible models:

```bash
export ANTHROPIC_API_KEY="..."
export ANTHROPIC_BASE_URL="https://api.anthropic.com"
```

Grok models:

```bash
export XAI_API_KEY="..."
export XAI_BASE_URL="https://api.x.ai"
```

OAuth login is also available:

```bash
cd rust
cargo run --bin claw -- login
```

### Build and Run

```bash
cd rust
cargo build --release -p claw-cli
cargo run --bin claw -- --help
cargo run --bin claw --
```

### Windows Notes

If you are building on Windows, use a Visual Studio developer environment and keep the target directory outside OneDrive-synced folders for best reliability.

Example:

```powershell
$env:CARGO_TARGET_DIR='C:\cargo-target\productivity-jacket'
cargo test --workspace -- --nocapture
```

## Common Commands

From the Rust workspace:

```bash
cargo run --bin claw -- prompt "summarize this workspace"
cargo run --bin claw -- --model sonnet "review the latest changes"
cargo run --bin claw --
```

Useful REPL surfaces:

- `/status` for runtime visibility
- `/config` for merged config inspection
- `/hooks` for merged hook inspection
- `/agents active` for live background runs
- `/agents runs` for live plus stale/completed runs
- `/compact` for continuity management

## Verification

Primary workspace verification:

```bash
cd rust
cargo fmt --all
cargo check --workspace
cargo test --workspace -- --nocapture
```

Python-side verification:

```bash
python -m unittest discover -s tests -v
```

## Roadmap

The next planned improvements are tracked in [ROADMAP.md](ROADMAP.md).

## Notes

- This repo intentionally excludes local runtime data such as `.claw/` and `.claw-agents/`
- The harness is source-build first today
- The Rust workspace is the main implementation surface
