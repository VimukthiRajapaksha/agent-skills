# agent-skills

A collection of reusable agent skills for AI coding assistants. This repository provides the **set-up-wso2-open-banking** skill — a guided, automated workflow that configures the [WSO2 Open Banking Accelerator](https://wso2.com/solutions/financial-services/open-banking/) on WSO2 Identity Server, API Manager, and Streaming Integrator.

## Requirements

- [Node.js](https://nodejs.org/) 18 or later (required to run `npx skills`)
- A supported AI coding agent (see [Supported Agents](https://github.com/vercel-labs/skills#available-agents))

---

### Quickstart

```bash
# Install to your current project (recommended)
npx skills add https://github.com/VimukthiRajapaksha/agent-skills/tree/main/skills/set-up-wso2-open-banking

# Install globally (available in all projects)
npx skills add https://github.com/VimukthiRajapaksha/agent-skills/tree/main/skills/set-up-wso2-open-banking --global
```

### Install to a Specific Agent

```bash
# Claude Code
npx skills add https://github.com/VimukthiRajapaksha/agent-skills/tree/main/skills/set-up-wso2-open-banking -a claude-code

# GitHub Copilot
npx skills add https://github.com/VimukthiRajapaksha/agent-skills/tree/main/skills/set-up-wso2-open-banking -a github-copilot

# Cursor
npx skills add https://github.com/VimukthiRajapaksha/agent-skills/tree/main/skills/set-up-wso2-open-banking -a cursor

# Multiple agents at once
npx skills add https://github.com/VimukthiRajapaksha/agent-skills/tree/main/skills/set-up-wso2-open-banking -a claude-code -a cursor
```

### Non-interactive Install (CI/CD)

```bash
npx skills add https://github.com/VimukthiRajapaksha/agent-skills/tree/main/skills/set-up-wso2-open-banking --all -y
```

### Preview Available Skills Without Installing

```bash
npx skills add https://github.com/VimukthiRajapaksha/agent-skills/tree/main/skills/set-up-wso2-open-banking --list
```

---

## Available Skills

### `set-up-wso2-open-banking`

Automates end-to-end configuration of the WSO2 Open Banking Accelerator. The skill guides your AI agent through every setup phase — from prerequisite checks to server startup validation.

**Supported deployment modes:**

| Mode | Description |
|---|---|
| `IS_ONLY` | WSO2 Identity Server + IAM Accelerator only (OB4) |
| `IS+APIM` | Identity Server + API Manager + their accelerators (OB3 and OB4) |
| `IS+APIM+SI` | Adds Streaming Integrator for data publishing (OB3 only) |

**Supported Open Banking versions:**

| Version | IS | APIM | SI |
|---|---|---|---|
| OB4 | 7.0.0, 7.1.0 | 4.4.0, 4.5.0 (optional) | — |
| OB3 | 5.11.0, 6.0.0, 6.1.0 | 4.0.0, 4.1.0, 4.2.0 | 4.0.0, 4.1.0, 4.2.0 (optional) |

**What the skill does:**

1. **Preflight checks** — verifies Java, MySQL client, `curl`, `keytool`, `unzip`, and required environment variables are present.
2. **Product extraction** — downloads or extracts base product and accelerator zips, resolves canonical paths, and validates directory structure.
3. **Product updates** — runs the `wso2update` binary against each product and accelerator using your WSO2 subscription credentials.
4. **Database configuration** — validates DB credentials, downloads the MySQL JDBC driver, updates `configure.properties` in each accelerator, then runs `merge.sh` and `configure.sh`.
5. **Certificate exchange** — regenerates `wso2carbon` keypairs with correct hostnames, imports cross-component trust, and installs Open Banking sandbox CA certificates.
6. **Server startup & health checks** — starts IS (and optionally APIM/SI) in the correct order and scans logs for critical errors.

---

## Before You Start

### Required Tools

| Tool | Minimum version | Purpose |
|---|---|---|
| Java (JDK) | OB3: 11 or 17 · OB4: 11, 17, or 21 | Run WSO2 servers |
| MySQL client | 8.0+ | Database setup |
| `curl` | any | Downloads |
| `keytool` | bundled with JDK | Certificate management |
| `unzip` | any | Archive extraction |

### Required Environment Variables (Secrets)

Set these before invoking the skill. **Never paste raw secrets into the chat.**

```bash
export OB_AGENT_UPDATE_TOOL_USERNAME="<your-wso2-subscription-username>"
export OB_AGENT_UPDATE_TOOL_PASSWORD="<your-wso2-subscription-password>"
export OB_AGENT_DATABASE_USERNAME="<mysql-username>"
export OB_AGENT_DATABASE_PASSWORD="<mysql-password>"
```

> **Note:** A valid [WSO2 subscription](https://wso2.com/subscription/) is required to run the `wso2update` tool. Contact the [WSO2 Support Portal](https://support.wso2.com/) if you need access.

### Zip Sources

Product and accelerator zips can be provided as:
- **A local file path** — e.g. `/home/user/downloads/wso2is-7.1.0.zip`
- **A direct download URL** — e.g. `https://host/wso2is-7.1.0.zip`

---

## Usage

Once the skill is installed, activate it by prompting your agent. The skill collects all required inputs before executing any commands.

### Example Prompts

```
Set up WSO2 Open Banking OB4 with IS only.
```

```
Set up WSO2 Open Banking OB3 with IS and APIM. Enable data publishing.
```

```
Configure the WSO2 Open Banking Accelerator. IS zip is at /downloads/wso2is-7.1.0.zip
and the IAM accelerator is at /downloads/wso2-ob-iam-accelerator-4.0.0.zip.
```

The agent will:
1. Confirm `OB_VERSION` and deployment mode.
2. Ask for zip sources (URL or local path) for each required component.
3. Ask for optional configuration overrides (hosts, DB host/port, working directory).
4. Execute each phase in order, stopping immediately on any failure and reporting the exact failed command, exit code, and path.

---

## Managing the Skill

```bash
# Check for updates
npx skills check

# Update to the latest version
npx skills update

# List all installed skills
npx skills list

# Remove the skill
npx skills remove set-up-wso2-open-banking
```

## Repository Structure

```
agent-skills/
└── skills/
    └── set-up-wso2-open-banking/
        ├── SKILL.md                          # Skill definition and workflow
        └── resources/
            ├── exchange-certificates.md      # Certificate exchange guide
            ├── v3/
            │   ├── set-up-data-publishing.md # OB3 Streaming Integrator setup
            │   ├── set-up-is-connector.md    # OB3 IS connector setup
            │   └── user-inputs.md            # OB3-specific inputs and compatibility
            └── v4/
                └── user-inputs.md            # OB4-specific inputs and compatibility
```

---

## Related Links

- [WSO2 Open Banking Documentation](https://ob.docs.wso2.com/)
- [WSO2 Identity Server Documentation](https://is.docs.wso2.com/)
- [WSO2 API Manager Documentation](https://apim.docs.wso2.com/)
- [WSO2 Support Portal](https://support.wso2.com/)
- [Agent Skills Specification](https://agentskills.io/)
- [Skills CLI (vercel-labs/skills)](https://github.com/vercel-labs/skills)
- [Skills Directory](https://skills.sh/)
