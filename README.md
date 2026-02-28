# 🚀 Gemini-Kit

<div align="center">

[![Version](https://img.shields.io/badge/version-4.0.0-blue.svg)](https://github.com/nth5693/gemini-kit/releases)
[![License](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE)
[![Tests](https://img.shields.io/badge/tests-291%20passed-brightgreen.svg)]()
[![Agents](https://img.shields.io/badge/AI%20Agents-27-purple.svg)]()
[![Skills](https://img.shields.io/badge/Skills-18-orange.svg)]()
[![Commands](https://img.shields.io/badge/Commands-45-yellow.svg)]()
[![Workflows](https://img.shields.io/badge/Workflows-33-cyan.svg)]()

### 🎯 Transform Your Terminal into an AI Engineering Team

**Gemini-Kit** is an extension for [Gemini CLI](https://github.com/google-gemini/gemini-cli) that brings **27 specialized AI agents**, **45 commands**, and **33 workflows** to help you code 10x faster.

[🚀 Quick Start](#-quick-start) • [🤖 Agents](#-agents) • [🛠️ Skills](#️-skills) • [⌨️ Commands](#️-commands) • [📚 API](docs/API.md)

</div>

---

## 📋 Table of Contents

- [What is Gemini-Kit?](#-what-is-gemini-kit)
- [How the Project Is Driven](#️-how-the-project-is-driven)
- [Quick Start](#-quick-start)
- [Agents](#-agents)
- [Skills](#️-skills)
- [Commands](#️-commands)
- [MCP Tools](#-mcp-tools)
- [Security](#-security)
- [FAQ](#-faq)

---

## 🤔 What is Gemini-Kit?

**Gemini-Kit** transforms Gemini CLI into a **virtual engineering team** with:

| Feature | Count | Description |
|---------|-------|-------------|
| 🤖 **AI Agents** | 27 | Specialized roles (Security, Frontend, Backend, DevOps...) |
| 🛠️ **Skills** | 18 | Knowledge modules (React, Next.js, Laravel, Nuxt, Docker, Security...) |
| ⌨️ **Commands** | 45 | Slash commands for every workflow |
| 🔄 **Workflows** | 33 | Structured development workflows |
| 🔒 **Security** | 30+ | Secret detection patterns |
| 📜 **Scripts** | 50+ | Automation scripts |

### Key Features

- **🔄 Compound System**: `/explore → /plan → /work → /review → /compound` - Each iteration builds a Knowledge Base. Solutions are saved and reused!
- **🧠 Learning System**: AI learns from your feedback. Correct once, it remembers forever
- **📚 23 Critical Patterns**: Common mistakes documented as patterns - AI reads them before coding
- **🎯 Multi-agent Orchestration**: Orchestrator coordinates multiple agents for complex tasks
- **💾 Auto-checkpoint**: Automatic Git backup before changes
- **🔒 Security Hooks**: Real-time blocking of secrets (30+ patterns)
- **📢 Notifications**: Discord & Telegram integration

---

## ⚙️ How the Project Is Driven

Gemini-Kit wires together five layers that turn a user prompt into coordinated AI work:

```
User prompt
    │
    ▼
┌─────────────────────────────────────────────────────┐
│  Hooks  (before-agent.js)                           │
│  • Inject relevant learnings from LEARNINGS.md      │
│  • Inject development rules                         │
│  • Match keywords → inject workflow/doc context     │
└────────────────────────┬────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────┐
│  Commands  (commands/*.toml)                        │
│  /plan /cook /review /skill /scout …                │
│  Each command expands into an agent prompt          │
└────────────────────────┬────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────┐
│  Orchestrator  (src/tools/orchestrator.ts)          │
│  • autoSelectWorkflow() scores keywords → workflow  │
│  • Runs agents sequentially or in parallel          │
│  • Retries failed steps, escalates if needed        │
└────────────────────────┬────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────┐
│  Agents  (agents/*.md)                              │
│  Specialized personas (Planner, Coder, Reviewer…)   │
│  Each agent reads the Skills it needs               │
└────────────────────────┬────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────┐
│  Skills  (skills/**/SKILL.md)                       │
│  Domain knowledge loaded on demand                  │
│  Auto-injected by keyword or agent context          │
└─────────────────────────────────────────────────────┘
```

### Workflow Auto-Selection

When you run `/cook <task>`, the orchestrator scores your task description against keyword patterns to pick the best workflow automatically:

| Keywords in task | Selected workflow |
|------------------|-------------------|
| `fix`, `debug`, `broken` | **quickfix** – Debug → Code → Test |
| `feature`, `implement`, `build` | **feature** – Design → Plan → Code → Test → Docs |
| `refactor`, `restructure` | **refactor** – Scout → Plan → Code → Test → Review |
| `review`, `audit` | **review** – Scout → Review → Security |
| `test`, `tdd`, `spec` | **tdd** – Tests first, then implement |
| `document`, `readme` | **docs** – Scout → Analyze → Write → Review |
| *(anything else)* | **cook** – Full cycle (default) |

### Skill Selection

Skills are picked in one of three ways — you rarely need to choose manually:

1. **By agent**: The `frontend-specialist` automatically brings React/Next.js/Tailwind skills; `backend-specialist` brings API/Docker/Security skills.
2. **By keyword hook**: `before-agent.js` scans every prompt and injects matching skill context (`laravel`, `nuxt`, `filament`, etc.).
3. **Explicitly**: Reference the framework in your prompt (`"using Filament v4…"`) or run `/skill create <technology>` to add a new skill.

---

## 🚀 Quick Start

### Prerequisites

| Requirement | Version | Check |
|-------------|---------|-------|
| Node.js | ≥ 18.0 | `node --version` |
| Git | ≥ 2.0 | `git --version` |
| Gemini CLI | Latest | `gemini --version` |

### Installation (2 minutes)

```bash
# 1. Clone repository
git clone https://github.com/nth5693/gemini-kit.git ~/.gemini/extensions/gemini-kit

# 2. Install & build
cd ~/.gemini/extensions/gemini-kit
npm install && npm run build

# 3. Link extension
gemini extensions link $(pwd)
```

### First Run

```bash
# Go to your project
cd /path/to/your/project

# Start Gemini CLI
gemini

# Try these commands:
> /status           # Check project status
> /explore React    # Research a topic
> /plan Add auth    # Create implementation plan
```

### Update

```bash
cd ~/.gemini/extensions/gemini-kit
git pull && npm install && npm run build
```

---

## 🤖 Agents

### 27 Specialized AI Agents

#### Core Development (5)

| Agent | Role | When to Use |
|-------|------|-------------|
| 📋 **Planner** | Create detailed plans | Starting new features |
| 🔍 **Scout** | Explore codebase | New projects, onboarding |
| 💻 **Coder** | Write clean code | Implementing features |
| 🧪 **Tester** | Write & run tests | Quality assurance |
| 👀 **Reviewer** | Code review | Before merging PRs |

#### Specialists (12) - NEW in v4.0

| Agent | Role | When to Use |
|-------|------|-------------|
| 🔐 **Security Auditor** | Security audit, OWASP | Security reviews |
| ⚛️ **Frontend Specialist** | React, Next.js, UI/UX | Frontend development |
| 🖥️ **Backend Specialist** | API, Database, Docker | Backend development |
| 🚀 **DevOps Engineer** | CI/CD, K8s, GitHub Actions | Infrastructure |
| 🐛 **Debugger** | Root cause analysis | Runtime errors |
| 🗄️ **Database Admin** | Schema, migrations | Database work |
| 🎨 **UI Designer** | Design, animations | UI/UX |
| 🌐 **Fullstack** | End-to-end | Full features |
| 📱 **Mobile Developer** | React Native, Flutter | Mobile apps |
| 🎮 **Game Developer** | Unity, Godot | Game development |
| ⚡ **Performance Optimizer** | Core Web Vitals, profiling | Performance issues |
| 🔓 **Penetration Tester** | Security testing | Pentest |

#### Support & Management (6)

| Agent | Role | When to Use |
|-------|------|-------------|
| 🔀 **Git Manager** | Commits, branches | Version control |
| 📝 **Docs Manager** | Documentation | README, API docs |
| 🔬 **Researcher** | Research | Technology decisions |
| 💡 **Brainstormer** | Ideas | Problem solving |
| 📊 **Project Manager** | Sprint planning | Project management |
| ✍️ **Copywriter** | Marketing copy | Content |

#### Specialized (4)

| Agent | Role | When to Use |
|-------|------|-------------|
| 🎯 **Orchestrator** | Multi-agent coordination | Complex tasks |
| 🏺 **Code Archaeologist** | Legacy code analysis | Refactoring old code |
| 👤 **Product Owner** | Requirements, backlog | Product decisions |
| 📈 **SEO Specialist** | SEO/GEO optimization | SEO work |

### How to Use Agents

```bash
# Mention agent in your request
> Use the security-auditor agent to review authentication
> Use the frontend-specialist to optimize React components
> Use the backend-specialist to design API architecture
```

---

## 🛠️ Skills

### 18 Knowledge Modules

Skills are **context-aware knowledge modules** loaded by agents based on the task at hand.

#### How Skill Selection Works

| Trigger | Mechanism | Example |
|---------|-----------|---------|
| **Agent context** | Each specialized agent embeds the skills it needs | `frontend-specialist` → React, Next.js, Tailwind |
| **Keyword matching** | `before-agent` hook scans the prompt for domain keywords | "Laravel route" → laravel skill injected |
| **Explicit request** | User references skill or framework directly | "Use the nuxt skill to explain SSR" |
| **`/skill` command** | Create, add, optimize, or fix-logs for any skill | `/skill create Stripe integration` |

> **TL;DR**: You usually don't need to choose a skill manually. Ask an agent or use a command — the right skill is injected automatically. For new domains, run `/skill create <technology>`.

---

#### Frontend (6)

| Skill | Content |
|-------|---------|
| **react-patterns** | Hooks, state management, component composition |
| **nextjs** | App Router, Server Components, data fetching |
| **nuxt** | Nuxt 4 file-based routing, composables, server API routes, SSR |
| **tailwind** | Tailwind CSS v4, responsive design |
| **performance** | Core Web Vitals, caching, optimization |
| **mobile** | React Native, Flutter, mobile performance |

#### Backend & Fullstack (5)

| Skill | Content |
|-------|---------|
| **api-design** | RESTful patterns, validation, rate limiting |
| **docker** | Multi-stage builds, Compose, container security |
| **security** | OWASP Top 10, JWT, XSS/CSRF prevention |
| **laravel** | Laravel 12 Eloquent ORM, queues, events, API resources |
| **filament** | Filament v4 admin panel, form/table builder, widgets |

#### Testing & Workflow (7)

| Skill | Content |
|-------|---------|
| **testing** | Vitest, MSW, snapshot testing |
| **code-review** | Review checklist, patterns |
| **debug** | 4-phase debugging methodology |
| **session-resume** | Context recovery |
| **compound-docs** | Knowledge documentation |
| **file-todos** | Task tracking |
| **examples** | Supabase, integrations |

---

## ⌨️ Commands

### 🔄 Core Workflow

**Slash Commands:**
| Command | Description | Example |
|---------|-------------|---------|
| `/plan` | Create detailed implementation plan | `/plan Add user authentication` |
| `/review` | Code review with Reviewer Agent | `/review src/api/auth.ts` |
| `/cook` | Full dev cycle (plan→code→test→review) | `/cook Add login feature` |

**Workflows (type name or use `/workflow`):**
| Workflow | Description |
|----------|-------------|
| `explore` | Research before implementing |
| `work` | Execute plan step by step |
| `compound` | Document solution for future use |
| `housekeeping` | Cleanup before git push |
| `cycle` | Run full workflow cycle |

### 💻 Development

| Command | Description | Example |
|---------|-------------|---------|
| `/code` | Write code for a task | `/code Create UserService class` |
| `/code-preview` | Preview code before applying | `/code-preview` |
| `/cook` | Full development cycle (plan→code→test→review) | `/cook Add login feature` |
| `/debug` | Debug issues with root cause analysis | `/debug Why API returns 500?` |
| `/fix` | Quick fix for errors | `/fix ESLint errors in src/utils` |
| `/test` | Write and run tests | `/test Write tests for UserService` |
| `/fullstack` | End-to-end feature development | `/fullstack Build user dashboard` |

### 📚 Documentation & Content

| Command | Description | Example |
|---------|-------------|---------|
| `/doc` | Update folder documentation | `/doc src/services` |
| `/docs` | Generate documentation | `/docs Create API reference` |
| `/adr` | Create Architecture Decision Record | `/adr Use PostgreSQL over MySQL` |
| `/changelog` | Generate changelog from commits | `/changelog` |
| `/content` | Create content (tutorials, guides) | `/content Write auth tutorial` |
| `/copywrite` | Marketing copy | `/copywrite Landing page hero text` |
| `/journal` | Development journal | `/journal` |

### 🔀 Git & PR

| Command | Description | Example |
|---------|-------------|---------|
| `/git` | Git operations | `/git commit "feat: add auth"` |
| `/pr` | Create Pull Request | `/pr Create PR for feature` |
| `/review-pr` | Review Pull Request | `/review-pr 123` |

### 🔍 Exploration & Research

| Command | Description | Example |
|---------|-------------|---------|
| `/scout` | Explore codebase structure | `/scout src/services` |
| `/scout-ext` | Extended scout with dependencies | `/scout-ext` |
| `/research` | Research technologies | `/research Compare React vs Vue` |
| `/brainstorm` | Brainstorm ideas | `/brainstorm Authentication approaches` |

### 🎨 Design & UI

| Command | Description | Example |
|---------|-------------|---------|
| `/design` | UI/UX design guidance | `/design Create dashboard layout` |
| `/video` | Video content planning | `/video` |
| `/screenshot` | Screenshot annotation | `/screenshot` |

### 🗄️ Database & Integration

| Command | Description | Example |
|---------|-------------|---------|
| `/db` | Database operations | `/db Design user schema` |
| `/integrate` | Integration planning | `/integrate Stripe payment` |
| `/ticket` | Get ticket details (Jira/Linear) | `/ticket ABC-123` |

### 🛠️ Project Management

| Command | Description | Example |
|---------|-------------|---------|
| `/pm` | Project management | `/pm Sprint planning` |
| `/project` | Project overview | `/project` |
| `/status` | Show project status | `/status` |
| `/kit-setup` | Initialize project context | `/kit-setup` |

### ⚡ Workflows (via `/workflow` command)

> **Note**: These are workflow guides in `.agent/workflows/`. Run them using `/workflow [name]` or just type the workflow name as a prompt.

| Workflow | Description |
|----------|-------------|
| `explore` | Deep research before planning |
| `plan-compound` | Create plan with solution search |
| `work` | Execute plan step by step |
| `review-compound` | Multi-pass code review |
| `compound` | Document solution for reuse |
| `housekeeping` | Cleanup before git push |
| `specs` | Create multi-session specifications |
| `triage` | Triage review findings |
| `report-bug` | Report bugs with reproduction steps |
| `adr` | Create Architecture Decision Record |
| `changelog` | Generate changelog from commits |
| `kit-setup` | Initialize project context |

### 🔧 Utilities

| Command | Description |
|---------|-------------|
| `/help` | Show all commands |
| `/ask` | Quick Q&A |
| `/chat` | Free chat |
| `/do` | Execute task |
| `/use` | Use specific agent |
| `/session` | Manage session |
| `/team` | Team orchestration |
| `/workflow` | Run specific workflow |
| `/mcp` | MCP tool operations |
| `/skill` | View/manage skills |
| `/watzup` | Quick status check |

---

## 🔧 MCP Tools

### Core Tools

| Tool | Function |
|------|----------|
| `kit_create_checkpoint` | Git checkpoint before changes |
| `kit_restore_checkpoint` | Rollback to checkpoint |
| `kit_get_project_context` | Get project information |
| `kit_handoff_agent` | Transfer context between agents |

### Knowledge Tools

| Tool | Function |
|------|----------|
| `kit_save_learning` | Save feedback for AI learning |
| `kit_get_learnings` | Get saved learnings |
| `kit_index_codebase` | Index codebase for search |
| `kit_keyword_search` | Search in codebase |

### Integration Tools

| Tool | Function |
|------|----------|
| `kit_github_create_pr` | Create GitHub PR |
| `kit_github_get_issue` | Get issue details |
| `kit_jira_get_ticket` | Get Jira ticket info |

---

## 🔒 Security

### Secret Detection (30+ patterns)

Real-time blocking BEFORE AI writes code:

| Category | Patterns |
|----------|----------|
| Cloud | AWS Access Keys, AWS Secrets |
| Code Hosting | GitHub PAT, GitLab Tokens, npm tokens |
| AI Providers | OpenAI, Anthropic, Google API Keys |
| Auth | Bearer tokens, JWT secrets |
| Keys | RSA, SSH, EC Private Keys |
| Database | MongoDB, PostgreSQL, MySQL connection strings |
| Communication | Slack tokens, webhooks |

### Command Blocking

- 🚫 `rm -rf /`, `rm -rf ~`, `rm -rf *`
- 🚫 Fork bombs (`:(){:|:&};:`)
- 🚫 Pipe to shell (`curl | sh`, `wget | bash`)
- 🚫 Dangerous disk operations (`dd if=`, `mkfs.`)

### Path Traversal Protection

- 🚫 `../` path traversal
- 🚫 `/etc/passwd`, `/etc/shadow`
- 🚫 `~/.ssh/` files
- 🚫 `.env`, `.pem`, `.key` files

---

## 📢 Notifications

| Integration | Description |
|-------------|-------------|
| 💬 **Discord** | Webhook notifications |
| ✉️ **Telegram** | Bot notifications |
| 🔔 **Session Hooks** | Before/After agent actions |

---

## ❓ FAQ

### Is Gemini-Kit free?
✅ **Yes**, completely free and open source (MIT License).

### Do I need an API key?
Configure **Gemini CLI** with your Google account. No separate API key needed.

### Which languages are supported?
✅ TypeScript, JavaScript, Python, Go, Rust, Java, C++, and more.

### Which OS is supported?
✅ macOS, Linux, Windows (WSL recommended)

---

## 📊 Stats (v4.0.0)

| Metric | Value |
|--------|-------|
| Tests | 291 passing |
| Lint | 0 errors |
| Agents | 27 |
| Skills | 18 categories |
| Commands | 45 |
| Workflows | 33 |
| Scripts | 50+ |
| Coverage | ~81% |

---

## 🤝 Contributing

Contributions welcome!

1. Fork the repo
2. Create branch (`git checkout -b feature/amazing-feature`)
3. Commit changes (`git commit -m 'Add amazing feature'`)
4. Push (`git push origin feature/amazing-feature`)
5. Create Pull Request

---

## 📄 License

MIT © 2024-2026

---

<p align="center">
  Made with ❤️ by the Gemini-Kit Team<br>
  <a href="https://github.com/nth5693/gemini-kit">GitHub</a> •
  <a href="https://github.com/nth5693/gemini-kit/releases">Releases</a> •
  <a href="https://github.com/nth5693/gemini-kit/issues">Issues</a>
</p>
