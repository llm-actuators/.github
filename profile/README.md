# llm-actuators

**Sterile primitives that let an LLM drive a real machine.**

Each binary here does one thing — tap a phone, run a browser, send a message to another Claude session, watch a token budget — with a parseable surface an LLM can call directly. Composition happens at the role layer (skills, doctrine), not inside the tools. That split keeps each tool portable and each role specific.

Start at **[llms](https://github.com/llm-actuators/llms)** for the canonical entrypoint, getting-started, use cases, and the binary↔binary interaction graph.

---

## The fleet

### Device control
Drive phones and browsers from an LLM. All three emit the same semantic schema so a single visual pipeline works across platforms.

| | |
|---|---|
| [`ddb`](https://github.com/llm-actuators/ddb) | Android Device Debug Bridge — tap, swipe, type, screenshot, install, logcat. Replaces raw `adb shell input`. |
| [`idb`](https://github.com/llm-actuators/idb) | iOS Device Debug Bridge — same surface for physical iPhones; drives WebDriverAgent. |
| [`wdb`](https://github.com/llm-actuators/wdb) | Web Debug Bridge — Chrome DevTools Protocol; 33 verbs for browser automation. |
| [`WebDriverAgent`](https://github.com/llm-actuators/WebDriverAgent) | Vendored WDA fork that `idb` drives under the hood. |
| [`semantic-schema`](https://github.com/llm-actuators/semantic-schema) | Shared YAML contract every device bridge emits; `vdb` consumes it. |
| [`semantic-agent-flutter`](https://github.com/llm-actuators/semantic-agent-flutter) | On-device agent emitting `semantic-schema` from Flutter app trees. |

### Visual & design
| | |
|---|---|
| [`vdb`](https://github.com/llm-actuators/vdb) | Visual Debug Bridge — cross-platform drift + region matrices from `semantic-schema` input. |
| [`fdb`](https://github.com/llm-actuators/fdb) | Figma Debug Bridge — emits the same schema from Figma frames; design-vs-built becomes a diff. |

### Coordination
| | |
|---|---|
| [`switchboard`](https://github.com/llm-actuators/switchboard) | File-based pub/sub for multi-Claude sessions. Append-only JSONL, flock-based presence, no daemon. |
| [`recruit`](https://github.com/llm-actuators/recruit) | Mid-session teammate spawner — splits the tmux window, launches `claude-safe`, pre-wires the new pane onto the recruiter's switchboard channel. Repo also ships `dismiss` and `idle-scout`. |

### Session management
| | |
|---|---|
| [`token-monitor`](https://github.com/llm-actuators/token-monitor) | Watches context-window usage and fires milestone alarms (60/70/75/80/85/90%) so the session can compact deliberately before auto-compact wipes the transcript. |
| [`compact-self`](https://github.com/llm-actuators/compact-self) | Injects `/compact` into the current Claude Code TUI via tmux. Pairs with `token-monitor`. |
| [`remote-control`](https://github.com/llm-actuators/remote-control) | Injects `/remote-control` into the current pane so the Claude iOS app can drive the session over a VPN. |

### Orchestration
| | |
|---|---|
| [`tctl`](https://github.com/llm-actuators/tctl) | Preflight → spec → suite test orchestrator. Walks specs, invokes the right device bridge per step, reports state over `switchboard`. Also the deepest toolchain doc authority. |

### Infrastructure
| | |
|---|---|
| [`skill-router`](https://github.com/llm-actuators/skill-router) | PreToolUse hook engine — enforces "always go through skill X" invariants on every Bash call. |
| [`substrate`](https://github.com/llm-actuators/substrate) | Skill ecosystem CLI + scaffold spec. Enrolls repos, deploys T1/T2 skills, validates F1–F16 invariants. Hosts the three META primitives still bundled with the spec (`substrate`, `bootstrap`, `dashboard`); the rest are extracted siblings (`skill-router`, `compact-self`, `token-monitor`, `claude-sandbox`). |
| [`claude-sandbox`](https://github.com/llm-actuators/claude-sandbox) | macOS Seatbelt profile + `nosandbox` escape hatch for tools that fail under the sandbox. |
| [`actuators-doctor`](https://github.com/llm-actuators/actuators-doctor) | Fleet-wide health probe — version drift, dep resolution, schema compat, env preconditions. |

### Docs
| | |
|---|---|
| [`llms`](https://github.com/llm-actuators/llms) | The canonical entrypoint. `llms.txt`, getting-started, use cases, interaction graph, role layer. Read this first. |

---

## Design principles

- **Sterile primitives.** Each binary does one thing with a parseable CLI surface. No domain verbs ("test the login flow"), only mechanical verbs ("tap (450, 800)"). Roles compose them.
- **One tool, one repo.** Independently versioned, independently shipped. Cross-tool contracts (`semantic-schema`, switchboard wire format) are their own repos.
- **Self-describing.** Every binary supports `--version`; the manifest contract (`--manifest json` for dep declarations) is rolling out so `actuators-doctor` can audit relationships mechanically.
- **LLM-first, human-friendly.** Output is structured (JSONL, YAML) for machines; pretty-print modes exist for humans who want to read along.
- **No magic.** Composition is explicit (env vars, tmux tags, schema YAML) — no hidden discovery, no silent fallbacks.
