# Skills

A curated collection of AI coding agent skills, workflows, and MCP configurations for OneDev (`tod`), GitHub (`gh`), UI/UX engineering, infrastructure, and code quality.

## Available Skills

### OneDev & `tod` CLI

| Skill | Description |
|-------|-------------|
| [using-tod](using-tod/SKILL.md) | Drive OneDev issues, pull requests, and builds through the `tod` CLI |
| [work-on-issue](work-on-issue/SKILL.md) | Create/checkout an issue branch and read issue details via `tod` |
| [work-on-pull-request](work-on-pull-request/SKILL.md) | Review, merge, resolve conflicts, or continue work on a OneDev pull request |
| [submit-issue-work](submit-issue-work/SKILL.md) | Submit completed work for a OneDev issue |
| [submit-pull-request-work](submit-pull-request-work/SKILL.md) | Submit completed work for an existing OneDev pull request |
| [submit-work](submit-work/SKILL.md) | Commit, push, and open a OneDev pull request for an issue |
| [review-pull-request](review-pull-request/SKILL.md) | Perform a structured code review of a OneDev pull request via `tod` |
| [issue-template](issue-template/SKILL.md) | Standard issue template format for creating OneDev issues (`@issue`) |
| [generate-commit-message](generate-commit-message/SKILL.md) | Compose commit messages conforming to `tod get-commit-message-requirement` |
| [standup-report](standup-report/SKILL.md) | Generate a standup report from OneDev PR and issue activity |
| [edit-build-spec](edit-build-spec/SKILL.md) | Create or edit `.onedev-buildspec.yml` using `tod` schema validation |
| [investigate-build-failure](investigate-build-failure/SKILL.md) | Diagnose failing or suspicious OneDev builds via `tod` |
| [fix-failed-build](fix-failed-build/SKILL.md) | Fix a prompt-specified failed OneDev build without issue/PR context |

### UI/UX & Frontend

| Skill | Description |
|-------|-------------|
| [premium-ui-ux-engineer](premium-ui-ux-engineer/SKILL.md) | Senior product designer and frontend architect guidelines for modern, accessible UIs |
| [anti-slop-optimizer](anti-slop-optimizer/SKILL.md) | Audit and optimize web apps to remove AI-generated design/copy tells |
| [vue-best-practices](vue-best-practices/SKILL.md) | Vue 3 best practices using Composition API with `<script setup>` and TypeScript |
| [kmp-glassmorphism-ui](kmp-glassmorphism-ui/SKILL.md) | Production-grade Glassmorphism design system for Kotlin Multiplatform Compose |

### Engineering, Infra & Workflows

| Skill | Description |
|-------|-------------|
| [work-like-boss](work-like-boss/SKILL.md) | High-agency product-minded execution — source of truth first, anti-slop craft, options then lock |
| [code-review-commons](code-review-commons/SKILL.md) | Common guidelines, persona, and constraints for code reviews |
| [golang-code-style](golang-code-style/SKILL.md) | Go code style conventions — line length, variable declarations, control flow |
| [gh-tasks](gh-tasks/SKILL.md) | Perform GitHub tasks (repos, issues, PRs, gists, releases) via `gh` CLI (`@gh`) |
| [kubectl-skill](kubectl-skill/SKILL.md) | Manage Kubernetes clusters, deployments, and pod diagnostics via `kubectl` |
| [deploying-tailscale-for-zero-trust-vpn](deploying-tailscale-for-zero-trust-vpn/SKILL.md) | Deploy Tailscale for zero-trust VPN networking |

### Integrations & Configs

| Item | Description |
|------|-------------|
| [zoho-cliq-mcp](zoho-cliq-mcp/SKILL.md) | Interact with Zoho Cliq using the Zoho MCP server (`@zoho`) |
| [mcp-configs](mcp-configs/README.md) | Complete MCP server configuration examples ([complete-mcp-config.json](mcp-configs/complete-mcp-config.json)) |

## Usage

Each skill directory contains a `SKILL.md` file with instructions, prerequisites, and examples. Clone or symlink the skill folders you need into your agent's skills directory.

---

- **Repository**: https://github.com/sandeshPatel06/Skills
- **Author**: sandeshPatel06
