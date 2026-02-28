# Agent Skills & Capabilities

## Purpose
The "brain" of the AI Agent. This directory contains the definitions, memories, and tools that enable the Agent to work effectively on the codebase.

## How Skills Are Selected

Skills are **not** chosen manually in normal usage. The system selects them through three mechanisms:

### 1. Agent Context (automatic)
Each specialized agent already knows which skills it needs. The `frontend-specialist` reads React, Next.js, and Tailwind skills; the `backend-specialist` reads API, Docker, and Security skills. Just invoke the right agent for the task.

### 2. Keyword Injection (automatic)
The `before-agent.js` hook runs before every agent turn. It scans the user's prompt for domain keywords and injects the matching skill context into the agent's context window automatically. No configuration needed.

### 3. Explicit Reference (manual)
You can always steer skill selection by:
- Mentioning the technology in your prompt: *"Build a Filament v4 resource for Orders"*
- Running `/skill create <technology>` to generate a skill from documentation
- Running `/skill add <skill-name> <reference>` to extend an existing skill

## Skill Selection Quick Reference

| Your task involves… | Skill that gets loaded |
|---------------------|------------------------|
| React components, hooks, state | `react-patterns` |
| Next.js App Router, Server Components | `nextjs` |
| Nuxt 4 SSR, composables, server routes | `nuxt` |
| Tailwind CSS, responsive design | `tailwind` |
| Core Web Vitals, bundle optimization | `performance` |
| React Native, Flutter | `mobile` |
| REST APIs, validation, rate limiting | `api-design` |
| Docker, Compose, containers | `docker` |
| OWASP, JWT, XSS/CSRF | `security` |
| Laravel 12, Eloquent, queues | `laravel` |
| Filament v4 admin panel | `filament` |
| Vitest, MSW, snapshot testing | `testing` |
| Debugging, root cause analysis | `debug` |
| Code review, PR checks | `code-review` |
| Session recovery, context restoration | `session-resume` |
| Documenting solutions for reuse | `compound-docs` |
| Task tracking with todos | `file-todos` |
| Supabase database/auth | `examples/supabase` |

## Components

| Component | Description |
|-----------|-------------|
| `compound-docs/` | Templates and logic for the Compounding Knowledge system. |
| `file-todos/` | Logic for the file-based task management system. |
| `session-resume/` | Context restoration protocols for new sessions. |
| `code-review/` | Checklists and workflows for automated code review. |
| `testing/` | Custom testing infrastructure and patterns. |
| `debug/` | Root-cause analysis and debugging protocols. |
| `react-hooks/` | Best practices and patterns for React development. |
| `nextjs/` | Next.js App Router, Server Components, data fetching patterns. |
| `nuxt/` | Nuxt 4 architecture, composables, server routes, and SSR patterns. |
| `tailwind/` | Tailwind CSS v4 patterns and design systems. |
| `performance/` | Core Web Vitals, caching, and optimization. |
| `mobile/` | React Native, Flutter, and mobile performance. |
| `api-design/` | RESTful patterns, validation, and rate limiting. |
| `docker/` | Multi-stage builds, Compose, and container security. |
| `security/` | OWASP Top 10, JWT, XSS/CSRF prevention. |
| `laravel/` | Laravel 12 patterns, Eloquent ORM, queues, and API development. |
| `filament/` | Filament v4 admin panel, form builder, tables, and widgets. |
| `examples/` | Project-specific or optional skill examples (e.g. Supabase). |

## Component Details

### `compound-docs/`
Contains the `SKILL.md` instruction set and template files used by the `/compound` workflow to generate persistent documentation.

### `file-todos/`
Contains the logic for managing the `todos/` directory, including status transitions and priority handling.

## Changelog

### 2026-02-28
- Added `laravel/`, `filament/`, and `nuxt/` skill definitions.
- Added skill selection documentation and quick-reference table.
- Expanded component listing to cover all 18 skills.

### 2025-12-23
- Initialized README documentation.
