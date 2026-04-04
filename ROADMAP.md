# Roadmap

## Now

- Keep the Rust harness stable on Windows, macOS, and Linux
- Tighten operator documentation around sessions, hooks, plugins, and background agents
- Reduce stale-state surprises in local workflows

## Near Term

- Add prompt/context observability so operators can inspect what the harness assembled before a run
- Add durable task checkpoints for long-running research and build workflows
- Add artifact manifests for agent outputs and handoffs
- Improve bundled plugin examples and plugin author documentation
- Add more end-to-end tests for background-agent orchestration

## Next Major Improvements

- Durable workflow continuity beyond conversation compaction
- Richer MCP identity and server observability
- Better approval and policy pipelines on top of hooks
- Release packaging and installer flows
- Stronger browser-facing and app-style operator UX

## Suggested First Issues

- Add a `docs/setup/windows.md` with the verified Windows build/test flow
- Add a prompt-debug command that prints assembled context sections and sources
- Add a run-artifact index for background agents
- Add explicit cleanup/pruning commands for stale local runtime state
- Add CI coverage for more Windows-specific shell and path behaviors
