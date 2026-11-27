# MCP Configurations

Reference Model Context Protocol (MCP) server configurations. All paths and credentials shown here are **placeholders** — replace them with your own values before use.

> **Security notice:** Never commit real credentials, API tokens, or machine-specific paths to a public repository. Use environment variables or a local `.env` file (added to `.gitignore`) to supply secrets.

## Available MCP Servers

### 1. `gopls-mcp-server`
- **Purpose**: Go language server for code intelligence
- **Binary**: `$(go env GOPATH)/bin/gopls mcp` — replace with your actual Go binary path
- **Used by**: Antigravity, Claude Code, Cursor, VS Code

### 2. `postgres`
- **Purpose**: PostgreSQL database queries and schema introspection
- **Script**: Place a `run-postgres-mcp.sh` wrapper in your project; point `command` at it
- **Used by**: Antigravity, Claude Code, Cursor, VS Code

### 3. `typesense`
- **Purpose**: Search engine operations and document queries
- **Script**: Place a `run-typesense-mcp.sh` wrapper in your project
- **Used by**: Antigravity, Claude Code, Cursor, VS Code

### 4. `playwright`
- **Purpose**: Browser automation and frontend testing
- **Script**: Place a `run-playwright-mcp.sh` wrapper in your project
- **Used by**: Antigravity, Claude Code, Cursor, VS Code

### 5. `kubectl`
- **Purpose**: Kubernetes cluster management
- **Command**: `npx -y mcp-server-kubernetes`
- **Env**: Set `KUBECONFIG` to the path of your kubeconfig file — do **not** hard-code it here
- **Used by**: Antigravity, Claude Code, Cursor, VS Code

### 6. `filesystem`
- **Purpose**: File system access scoped to your project directory
- **Command**: `npx -y @modelcontextprotocol/server-filesystem /path/to/your/project`
- **Security**: Restrict the path to the smallest required directory; never use `/home/username` or `/`
- **Used by**: Antigravity, Claude Code, Cursor, VS Code

### 7. `google-kubernetes-engine`
- **Purpose**: GKE cluster management via Google Cloud
- **URL**: `https://container.googleapis.com/mcp`
- **Auth**: Google credentials (run `gcloud auth application-default login`)
- **Used by**: Antigravity, Claude Code, Cursor, VS Code

### 8. `ZohoMCP`
- **Purpose**: Zoho Cliq messaging integration
- **Command**: `npx mcp-remote`
- **URL**: Obtain your personal MCP endpoint from the Zoho MCP dashboard — format: `https://YOUR_WORKSPACE.zohomcp.com/mcp/YOUR_TOKEN/message`
- **Security**: Treat the URL as a credential. Store it in an environment variable or `.env` file; never commit the real URL.
- **Used by**: Antigravity, Claude Code

### 9. `runpod`
- **Purpose**: RunPod GPU cloud operations
- **URL**: `https://mcp.getrunpod.io/`
- **Used by**: Cursor, Antigravity, Claude Desktop, VS Code

## Configuration Files

- `complete-mcp-config.json` — Template with all servers (all values are placeholders — do not use as-is)

## Placement by Agent

| Agent | Config location |
|---|---|
| Antigravity / Gemini CLI | `~/.gemini/config/mcp_config.json` |
| Claude Code | `~/.claude/mcp_config.json` |
| Claude Desktop | `~/.config/Claude/claude_desktop_config.json` |
| Cursor | `~/.config/Cursor/User/mcp_config.json` |
| VS Code | `~/.config/Code/User/mcp_config.json` |
| OpenCode | `~/.config/opencode/mcp_config.json` |

## Security Checklist

- [ ] Replace all `/path/to/...` placeholders with real paths **locally**, not in this file
- [ ] Store API tokens and MCP URLs in a `.env` file excluded from version control
- [ ] Add `.env` and any `*_local.json` files to `.gitignore`
- [ ] Restrict `filesystem` MCP scope to the minimum required directory
- [ ] Rotate any credentials that were previously committed to a public repository
