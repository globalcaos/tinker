# 🔧 Tinker — OpenClaw Command Center

> **Stop guessing what your AI costs.** Tinker shows every token, every dollar, every context byte — in real time.

---

## The Problem

You're running Opus through OpenClaw. A single deep conversation burns **$20+ in tokens** with zero warning. You check your provider dashboard three days later and wonder what happened.

That's not a billing problem. That's a **visibility problem**.

## What Tinker Does

Tinker is a **real-time command center** that sits on top of your OpenClaw gateway. Not a chat skin — a control panel for operators who want to see what's actually happening.

### 🗺️ Context Treemap
Interactive squarified treemap of your context window. See exactly what takes space: system prompt, workspace files, conversation history, tool results. Drill down from categories → messages → raw text. When you wonder "why is my context 180K tokens?" — this tells you in one glance.

### 📊 Response Treemap
Same visualization for model output. How much is text, how much is thinking, how much is tool calls? Per LLM call within a run, so you see the real cost of that 8-step tool loop.

### 📈 Context Timeline
Stacked bar chart showing context composition over time. Watch your conversation grow, see compaction events, identify which turns are the token hogs.

### 🕸️ Overseer Graph
Sub-agent health monitoring. See which sub-agents are running, their progress, staleness detection — all in a force-directed graph.

### 💰 Live Cost Tracking
Per-provider token usage. Daily and monthly estimates. The 5-hour Claude rate-limit window with countdown timer. Per-auth-key model rows with provider logos and breathing glow on the active model.

### 💬 Full Chat Interface
Not just a dashboard — it's a complete webchat with session switching, markdown rendering, tool call inspection (expand inline, never in a sidebar), and real-time streaming. Use it as your daily driver or just for monitoring.

---

## Quick Start

### With OpenClaw (recommended)

Tinker connects to your running OpenClaw gateway WebSocket.

```bash
# Clone this repo
git clone https://github.com/globalcaos/tinker.git
cd tinker

# Install deps
pnpm install

# Development (hot reload)
pnpm dev
# → http://localhost:18790

# Production build
pnpm build
# → dist/ folder, serve however you like
```

### As an OpenClaw Plugin

If you're using the [globalcaos fork](https://github.com/globalcaos), Tinker ships as a built-in plugin served directly from the gateway:

```
http://localhost:18789/tinker/
```

No separate server needed.

---

## Architecture

```
tinker/
├── src/
│   ├── app.ts                    ← Main shell: sidebar, sessions, WebSocket, chat
│   ├── styles/
│   │   └── base.css              ← Dark theme, information-dense
│   └── panels/
│       ├── context-treemap.ts    ← What fills your context window
│       ├── response-treemap.ts   ← What each response costs
│       ├── context-timeline.ts   ← Context usage over time (stacked bars)
│       └── overseer-graph.ts     ← Sub-agent health graph
├── dist/                         ← Pre-built production bundle
├── index.html
├── vite.config.ts
└── package.json
```

**Stack:** TypeScript + [Lit](https://lit.dev/) + Vite. No React. No heavy frameworks. ~5,700 lines of focused code.

**Zero upstream overlap** — nothing in this repo exists in OpenClaw's `ui/` directory. No merge conflicts, ever.

---

## Gateway Connection

Tinker connects to the OpenClaw gateway WebSocket (default `ws://localhost:18789/ws`).

Authentication: reads the gateway token from your OpenClaw config, or accepts it via URL parameter.

**Key API methods used:**
- `chat.history` — message history per session
- `chat.send` — send messages
- `sessions.list` — list all sessions
- `sessions.usage` — per-provider token usage
- `usage.cost` — daily cost breakdown
- `status` / `health` — gateway status

**Events:**
- `chat` — real-time message streaming (deltas, finals, errors)
- `agent` — tool calls, lifecycle events
- `anatomy` — context window composition data (for treemaps)

---

## Pricing Reference

These are the API costs Tinker tracks:

| Model | Input (per 1M) | Output (per 1M) | Watch out? |
|---|---|---|---|
| Claude Opus 4 / 4.5 | **$15.00** | **$75.00** | ⚠️ One deep session = $20+ |
| Claude Sonnet 4 / 3.5 | $3.00 | $15.00 | Sweet spot |
| Claude Haiku 3.5 | $0.80 | $4.00 | Background tasks |
| GPT-5.2 Pro | $2.50 | $10.00 | Good failover |
| Gemini 3 Pro | $1.25 | $5.00 | Large context window |
| Gemini Flash | $0.10 | $0.40 | Near-free |

---

## Design Principles

1. **Dark theme, information-dense** — this is for operators, not consumers
2. **Panels, not pages** — everything visible at once, resize and collapse
3. **Inline expansion** — clicking expands detail in place, never opens a useless sidebar
4. **Real-time** — WebSocket-driven, no polling
5. **Operator-first** — you should know what your agent is spending before the bill arrives

---

## Requirements

- [OpenClaw](https://github.com/openclaw/openclaw) gateway running (any version)
- Node.js 22+
- pnpm (for development)

---

## Related

- [OpenClaw](https://github.com/openclaw/openclaw) — the AI agent framework
- [ClawHub](https://clawhub.com) — agent skills marketplace
- [The Field Guide for New AI Agents](./docs/field-guide.md) — everything we learned running agents 24/7

---

## License

MIT

---

_Built by [globalcaos](https://github.com/globalcaos). Because your AI shouldn't cost more than your rent — and if it does, you should at least know about it._
