# 🤖 Multi-Agent Arena: Benchmark & Evaluation Framework

[![Python](https://img.shields.io/badge/Python-3.8%2B-blue)](https://www.python.org/)
[![License](https://img.shields.io/badge/License-MIT-green)](LICENSE)
[![Status](https://img.shields.io/badge/Status-Active-brightgreen)]()

A powerful framework for **benchmarking and evaluating multiple AI agents** in parallel. Assign tasks, let agents compete, measure performance, and track insights across sessions.

---

## 🎯 What is Multi-Agent Arena?

Multi-Agent Arena is a **systematic evaluation framework** that enables you to:

- 🚀 **Deploy agents in parallel** — test Codex, Gemini, Qwen, Ollama, or any LLM-based agent
- 📊 **Measure performance** — consistent scoring across 5 dimensions (correctness, completeness, code quality, creativity, efficiency)
- 💾 **Track results over time** — persistent leaderboards and session history
- 🔄 **Run competitive rounds** — structured tasks with formal evaluation
- ⚡ **Quick experiments** — lightweight WORKSPACE for rapid testing
- 🧠 **Session persistence** — automatic memory system for future reference

**Use cases:**
- Evaluate LLM capabilities across different models
- Track agent performance improvements over iterations
- Compare code-generation quality between frameworks
- Build reproducible benchmarks for AI development
- Analyze agent behavior on structured problem-solving tasks

---

## 📁 Project Structure

```
.
├── ARENA_AGENTES/          # Formal evaluation system
│   ├── README.md           # Arena documentation
│   ├── STATE.md            # Current round state
│   ├── tasks/
│   │   └── bank.md         # Task library (9+ tasks)
│   ├── rounds/             # Completed round history
│   │   └── {YYYY-MM-DD}/   # Results by date
│   └── scores/
│       └── leaderboard.md  # Cumulative rankings
│
├── WORKSPACE/              # Lightweight testing area
│   ├── README.md           # Quick-start guide
│   ├── STATE.md            # Active session tracking
│   └── {agent}/            # Auto-created per agent
│
├── AGENT_CONTEXT/          # Agent profiles & memory
│   ├── agents/
│   │   ├── codex.md        # Codex behavior & strengths
│   │   ├── gemini.md       # Gemini insights
│   │   ├── qwen.md         # Qwen notes
│   │   └── ollama.md       # Ollama observations
│   └── sessions/
│       └── latest.md       # Previous session summary
│
└── .claude/                # Session configuration
    └── WORKSPACE_MEMORY.md # Technical reference
```

---

## 🚀 Quick Start

### 1. **Formal Evaluation Round** (Arena)

Run a complete competitive round with scoring:

- Define a task in ARENA_AGENTES/
- Assign to all agents
- Claude evaluates outputs on 5 dimensions (1–5 each)
- Max: 75 points per round (3 tasks × 25 pts)

**Scores by dimension:**
- ✅ **Correctness** — Runs without errors, produces expected output
- 📋 **Completeness** — All requirements from brief are met
- 🏗️ **Code Quality** — Clean code, good naming, structure
- ✨ **Creativity** — Unexpected approaches, elegant solutions
- ⚡ **Efficiency** — Minimal boilerplate, concise implementation

### 2. **Quick Experiment** (Workspace)

Rapid testing without formal scoring:

- Assign task to WORKSPACE/
- Agents auto-create folders: WORKSPACE/{agent}/
- Each agent works independently
- Report results in WORKSPACE/{agent}/RESULT.md

---

## 📊 How It Works

```
Task Brief → Parallel Dispatch → Agent Execution → Evaluation → Leaderboard Update
```

Each agent receives identical tasks and solves them independently.  
Results are evaluated on consistency, quality, and innovation.

---

## 🎮 Task Library

Default task categories:

- **Python Challenges** — Code generation, algorithms, problem-solving
- **HTML Pages** — Markup, frontend basics, responsive design
- **Data Visualization** — Matplotlib, graphs, plotting

Tasks stored in `ARENA_AGENTES/tasks/bank.md` and organized by difficulty.

---

## 📈 Scoring System

| Dimension | Points | Criteria |
|-----------|--------|----------|
| **Correctness** | 1–5 | Runs without errors, correct output |
| **Completeness** | 1–5 | Fulfills all requirements |
| **Code Quality** | 1–5 | Clean, readable, well-structured |
| **Creativity** | 1–5 | Elegant or unexpected solutions |
| **Efficiency** | 1–5 | Minimal boilerplate, conciseness |

**Per task:** 25 points  
**Per round:** 75 points (3 tasks)  
**Cumulative:** Leaderboard across all rounds

---

## 🧠 Memory System

Automatic persistence between sessions:

- **Agent profiles** — Observed strengths, weaknesses, patterns
- **Session history** — What happened, key insights
- **Round results** — Scores, evaluations, comparisons
- **Zero token cost** — Only loads when explicitly requested

Future sessions inherit this context and avoid re-learning.

---

## 🔧 Supported Agents

- **Codex** — Code generation expert
- **Gemini** — Reasoning and creativity
- **Qwen** — Efficient solutions
- **Ollama** — Local/cloud LLM (GLM, qwen-coder, minimax, deepseek, mistral)

Easily extend to add new agents.

---

## 🔌 Agent Plugins — Installation

Each agent runs as a Claude Code plugin. Install once, use across all sessions.

### Common flow (all plugins)

```shell
/plugin marketplace add <repo>   # register the source (one-time)
/plugin install <plugin>@<id>    # install the plugin
/reload-plugins                  # activate in current session
/<agent>:setup                   # verify + authenticate
```

---

### Codex
**Repo:** [openai/codex-plugin-cc](https://github.com/openai/codex-plugin-cc)

```shell
/plugin marketplace add openai/codex-plugin-cc
/plugin install codex@openai-codex
/reload-plugins
/codex:setup
```

**Auth:**
```shell
npm install -g @openai/codex   # if not installed
!codex login                   # OAuth login
```

**Key commands:** `/codex:review` `/codex:task` `/codex:status` `/codex:result` `/codex:cancel`  
**Activates skills:** `codex:rescue`, `codex:codex-cli-runtime`, `codex:gpt-5-4-prompting`

---

### Gemini
**Repo:** [abiswas97/gemini-plugin-cc](https://github.com/abiswas97/gemini-plugin-cc)

```shell
/plugin marketplace add abiswas97/gemini-plugin-cc
/plugin install gemini@abiswas97-gemini
/reload-plugins
/gemini:setup
```

**Auth (one of):**
```shell
!gcloud auth application-default login   # OAuth via gcloud
# OR set env var:
export GOOGLE_API_KEY=your-key           # from AI Studio
```

**Key commands:** `/gemini:review` `/gemini:task` `/gemini:status` `/gemini:result` `/gemini:cancel`  
**Activates skills:** `gemini:rescue`, `gemini:gemini-cli-runtime`, `gemini:gemini-prompting`

---

### Qwen
**Repo:** [gaduviera/qwen-plugin-cc](https://github.com/gaduviera/qwen-plugin-cc)

```shell
/plugin marketplace add gaduviera/qwen-plugin-cc
/plugin install qwen@gaduviera-qwen
/reload-plugins
/qwen:setup
```

**Auth (one of):**
```shell
!qwen auth login                        # OAuth login
# OR set env var:
export DASHSCOPE_API_KEY=your-key       # from DashScope console
```

**Key commands:** `/qwen:review` `/qwen:task` `/qwen:status` `/qwen:result` `/qwen:cancel`  
**Activates skills:** `qwen:rescue`, `qwen:qwen-cli-runtime`, `qwen:qwen-prompting`  
**Limit:** max ~3 files per session (exit code 53 if exceeded)

---

### Ollama
**Repo:** [gaduviera/ollama-plugin-cc](https://github.com/gaduviera/ollama-plugin-cc)

```shell
/plugin marketplace add gaduviera/ollama-plugin-cc
/plugin install ollama@ollama-plugin-cc
/reload-plugins
/ollama:setup
```

**Auth (cloud models — set the corresponding env var):**

| Model | Env var |
|---|---|
| GLM / glm-5.1:cloud | `ZHIPU_API_KEY` |
| qwen-coder / qwen3-coder:cloud | `DASHSCOPE_API_KEY` |
| minimax-m2.5:cloud | `MM_API_KEY` |
| mistral, deepseek | local OLLAMA (no key needed) |

**Key commands:** `/ollama:setup` `/ollama:task` `/ollama:switch` `/ollama:review` `/ollama:status` `/ollama:result` `/ollama:cancel`  
**Activates skills:** `ollama:rescue`, `ollama:run-model`, `ollama:setup`

---

### Skills MAG activadas por los plugins

Una vez instalados los 4 plugins, el stack de skills MAG queda operativo:

| Skill | Invocación | Rol |
|---|---|---|
| `/mag-agent` | "modo multiagente", "delegá" | Orquestador general — decide quién hace qué |
| `/mag-dispatch` | invocado internamente | Motor de ejecución — comandos exactos por agente |
| `/mag-arena` | "arena", "ronda", "benchmark" | Orquestador del arena — rondas, scoring, leaderboard |

---

## 📚 Guides

- **[Arena Setup](ARENA_AGENTES/README.md)** — Formal round evaluation
- **[Workspace Guide](WORKSPACE/README.md)** — Quick testing
- **[Agent Insights](AGENT_CONTEXT/)** — Behavioral profiles
- **[Technical Docs](.claude/WORKSPACE_MEMORY.md)** — Architecture

---

## 🤝 Contributing

Add new tasks, improve evaluations, or extend agent support:

1. Update task library in `ARENA_AGENTES/tasks/bank.md`
2. Document insights in `AGENT_CONTEXT/agents/`
3. Run a round with new setup
4. Share results via session memory

---

## 📄 License

MIT License — See [LICENSE](LICENSE) for details.

---

**🚀 Ready to start?** Begin with [Workspace](WORKSPACE/README.md) for quick tests or jump to [Arena](ARENA_AGENTES/README.md) for competitive rounds.

Happy benchmarking!
