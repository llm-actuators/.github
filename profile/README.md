# llm-actuators

**Sterile primitives that let an LLM drive a real machine.**

Each binary here does one thing — tap a phone, run a browser, send a message to another Claude session, watch a token budget — with a parseable surface an LLM can call directly. Composition happens at the role layer (skills, doctrine), not inside the tools. That split keeps each tool portable and each role specific.

Start at **[llms](https://github.com/llm-actuators/llms)** for the canonical entrypoint, getting-started, use cases, and the binary↔binary interaction graph.

---

## The fleet

30 single-purpose tools across Rust, Swift, Go, Python and Shell, organized in the same six layers as the [interactive map](https://llm-actuators.github.io/).

### Entry — where an LLM starts
| | |
|---|---|
| [`llms`](https://github.com/llm-actuators/llms) | The canonical LLM-first index to the whole toolchain — the single "start here" entrypoint. Read this first. |
| [`specs`](https://github.com/llm-actuators/specs) | Cross-repo specifications and workflow references — the shared contracts the tools agree on. |

### Control & policy plane — the guardrails, before an action runs
| | |
|---|---|
| [`skill-router`](https://github.com/llm-actuators/skill-router) | PreToolUse hook engine — matches every tool call against TOML rules and allows, denies, or reroutes it. Policy-as-code, enforced before the action runs. |
| [`workflows`](https://github.com/llm-actuators/workflows) | A workflow state-machine enforcer over hooks — stops an agent skipping required steps or acting out of order. |
| [`substrate`](https://github.com/llm-actuators/substrate) | The scaffolding layer — skills + primitives + configs + hooks + memory, so adding a project, company or primitive is one command. |
| [`hooks`](https://github.com/llm-actuators/hooks) | The hook enforcement + session-lifecycle layer — PreToolUse/PostToolUse gates, plus session-start (pins the true model id) and post-compact (recovers active skills, drives resume). |

### Coordination substrate — how agents and devices talk
| | |
|---|---|
| [`switchboard`](https://github.com/llm-actuators/switchboard) | The message bus. File-based pub/sub for multi-session coordination — append-only JSONL, flock-based presence, no daemon. |
| [`switchboard-ios`](https://github.com/llm-actuators/switchboard-ios) | A native SwiftUI client to watch and post to the switchboard wire from an iPhone. |
| [`todo`](https://github.com/llm-actuators/todo) | A priority-aware, Markdown-backed shared todo list for an agent cohort to coordinate work. |
| [`device-claim`](https://github.com/llm-actuators/device-claim) | A per-device mutex so two agents never drive the same physical device at once — lease-based locking with liveness. |
| [`resources`](https://github.com/llm-actuators/resources) | Enumerates every active device, simulator and emulator and which claims are in flight. |

### Actuation — the bridges an agent uses to touch a surface
| | |
|---|---|
| [`idb`](https://github.com/llm-actuators/idb) | Control a real iPhone. A unified CLI over WebDriverAgent for touch, gestures and app control. |
| [`ddb`](https://github.com/llm-actuators/ddb) | Control a real Android device. One CLI replacing a pile of adb scripts, scrcpy wrappers and heartbeat daemons. |
| [`wdb`](https://github.com/llm-actuators/wdb) | Drive a browser or web admin panel. A single zero-dependency Go binary — "adb for the web". |
| [`fdb`](https://github.com/llm-actuators/fdb) | Read a Figma design as structured data — extracts a UI semantic schema from design frames so intent can be diffed against implementation. |
| [`vdb`](https://github.com/llm-actuators/vdb) | Catch visual UI drift across platforms — cross-platform drift detection and a region matrix over the shared schema. |
| [`visual-qa`](https://github.com/llm-actuators/visual-qa) | The device-automation framework tying the bridges together. Semantic agents extract the live UI tree; tests are declarative YAML, cross-platform. |
| [`semantic-schema`](https://github.com/llm-actuators/semantic-schema) | The shared UI wire format — canonical Rust types that let the Android, iOS and web bridges all speak one schema. |
| [`device-control-ios`](https://github.com/llm-actuators/device-control-ios) | The WebDriverAgent integration that powers `idb`'s control of physical iPhones. |
| [`tctl`](https://github.com/llm-actuators/tctl) | Test-control CLI for authoring and running device test cases against the bridges. Also the deepest toolchain doc authority. |

### Fleet lifecycle — spawning, running and containing the agents
| | |
|---|---|
| [`recruit`](https://github.com/llm-actuators/recruit) | Spawn a teammate mid-session — splits a tmux pane into a fresh agent seat; its counterpart `dismiss` retires one cleanly. Repo also ships `idle-scout`. |
| [`fleet-tui`](https://github.com/llm-actuators/fleet-tui) | The mission-control dashboard — a read-only terminal view of every project bucket and every live agent at a glance. |
| [`compact-self`](https://github.com/llm-actuators/compact-self) | Lets a running session compact its own context by injecting `/compact` into the TUI — self-maintenance under long runs. |
| [`remote-control`](https://github.com/llm-actuators/remote-control) | Injects `/remote-control` into a running session so the Claude iOS app can drive it over a VPN. |
| [`idle-work`](https://github.com/llm-actuators/idle-work) | Chunk + interrupt-safety helpers so long idle-pool work can be paused and resumed without corruption. |
| [`claude-sandbox`](https://github.com/llm-actuators/claude-sandbox) | Runs the agent inside a macOS Seatbelt sandbox, plus the `nosandbox` escape hatch for tools that fail under it. |

### Observability & ops — keeping a supervised fleet dependable and on-budget
| | |
|---|---|
| [`token-monitor`](https://github.com/llm-actuators/token-monitor) | Reads a session's live token usage from the transcript and fires milestone alarms — so spend can be budgeted and the session compacts before auto-compact wipes it. |
| [`burn`](https://github.com/llm-actuators/burn) | Aggregates token-burn across every session in the fleet — the cost picture across many agents at once. |
| [`actuators-doctor`](https://github.com/llm-actuators/actuators-doctor) | A fleet-wide health check that verifies every llm-actuators binary is present, current and working. |
| [`fleet-tooling`](https://github.com/llm-actuators/fleet-tooling) | Fleet-digest + ops tooling — aggregates live state from `switchboard`, `todo` and `resources` into the operator's digest; also the idle-worktree helper for background work. |

---

## Design principles

- **Sterile primitives.** Each binary does one thing with a parseable CLI surface. No domain verbs ("test the login flow"), only mechanical verbs ("tap (450, 800)"). Roles compose them.
- **One tool, one repo.** Independently versioned, independently shipped. Cross-tool contracts (`semantic-schema`, switchboard wire format) are their own repos.
- **Self-describing.** Every binary supports `--version`; the manifest contract (`--manifest json` for dep declarations) is rolling out so `actuators-doctor` can audit relationships mechanically.
- **LLM-first, human-friendly.** Output is structured (JSONL, YAML) for machines; pretty-print modes exist for humans who want to read along.
- **No magic.** Composition is explicit (env vars, tmux tags, schema YAML) — no hidden discovery, no silent fallbacks.

## Interactive architecture map

[**View the interactive toolchain map →**](https://llm-actuators.github.io/)

## How the fleet is governed

[**Read the governance model →**](https://github.com/llm-actuators/.github/blob/main/governance.md) — the problems that forced each rule, and the principles they left behind.

---

Designed, built and operated by **Siniša Marjanović** · [LinkedIn](https://www.linkedin.com/in/sinisa-m-150b3988) · [GitHub](https://github.com/marjan89)
