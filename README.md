# Galyarder Security

**_Verification, threat modeling, and passive review infrastructure for the Agentic Company._**

[![Node](https://img.shields.io/badge/node-18%2B-green.svg)](https://nodejs.org/)
[![License](https://img.shields.io/badge/license-AGPL--3.0-blue.svg)](LICENSE)
[![Status](https://img.shields.io/badge/status-active-22c55e.svg)](https://github.com/muhamadgalihsaputra/shannon-proxy)

---

## What Is Galyarder Security?

`galyarder-security` (formerly Shannon Proxy) is the dedicated security, auditing, and verification layer of **Galyarder Labs**. 

In an **Agentic Company**, autonomous workforces execute real operations, run software development builds, and coordinate digital assets. This setup introduces unique threat vectors: agent tool execution drift, dependency supply chain vulnerabilities, compromised API gateways, and unverified code paths.

`galyarder-security` is built to run passive reviews, coordinate threat modeling, and secure agentic workspace operations through configurable security auditing workflows and proxy routing.

It is designed to route all model operations through a local, auditable proxy structure—giving high-agency operators complete visibility and control over what their agents execute.

---

## Why This Project Exists

In the era of autonomous operator assets (the **1-Man Army** model), security cannot be manual. If an agent has shell access, it requires a localized, deterministic supervisor that analyzes input commands, filters outputs, and audits execution logs.

Existing AI security frameworks focus on chat-box prompt injection. This is insufficient for agents with tools.

This repository provides:
1. **Auditable Proxy Routing**: Intercepts model SDK requests and routes them through a single local monitoring point (`CLIProxyAPI`, LiteLLM, or OpenRouter) rather than direct cloud endpoints.
2. **Temporal Security Workflows**: Structured security sweeps, vulnerability scanning, and threat modeling pipelines built to verify that agentic environments remain secure.
3. **Local Cleanup Routines**: Programmatic cleanup of generated files, test artifacts, and execution residue (e.g., test scripts or model outputs) to prevent pollution and unintentional exposures.

---

## Philosophy

- **Vigilance over trust**: Assume all autonomous systems are prone to drift. Audit every tool execution and command execution.
- **Sovereign Gateways**: All LLM calls and API access route through a local, credential-controlled proxy. No hardcoded SDK endpoints.
- **Auditable Evidence**: Every verification runs through Temporal workflows which store verifiable evidence of security state.
- **Workspace Hygiene**: Agents generate scripts and outputs during runs. Automated workspace cleanup is required to maintain zero-residue states.

---

## How It Works: The Configurable Proxy

Shannon CLI Proxy patches hardcoded model SDK constraints at build time, routing all execution through a customizable local gateway.

```
┌─────────────────────────────────────────────────────────────┐
│                    Autonomous Workspace                     │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   ┌──────────────┐         ┌──────────────┐                 │
│   │  Galyarder   │────────▶│    Local     │                 │
│   │   Agent /    │         │  Auditing    │                 │
│   │   Worker     │         │   Proxy      │                 │
│   │   Runtime    │         │  (Port 8317) │                 │
│   └──────────────┘         └──────────────┘                 │
│                                   │                         │
│                     ┌─────────────┼─────────────┐           │
│                     ▼             ▼             ▼           │
│              ┌──────────┐  ┌──────────┐  ┌──────────┐       │
│              │ Anthropic│  │  Groq    │  │  Local   │       │
│              │   API    │  │   API    │  │  Ollama  │       │
│              │ (Cloud)  │  │ (Cloud)  │  │ (Local)  │       │
│              └──────────┘  └──────────┘  └──────────┘       │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

By decoupling Shannon workflows from specific cloud providers, you retain control over data routing, caching, rate limiting, and output auditing.

---

## Quick Start

### Prerequisites

- Docker and Docker Compose
- A local LLM proxy ([CLIProxyAPI](https://github.com/router-for-me/CLIProxyAPI), LiteLLM, OpenRouter, etc.)
- A model configured in your local proxy

### 1. Clone and Configure

```bash
git clone https://github.com/muhamadgalihsaputra/shannon-proxy.git galyarder-security
cd galyarder-security

# Copy and edit environment
cp .env.example .env
```

Edit `.env`:

```bash
# Your proxy endpoint (accessible from Docker)
ANTHROPIC_BASE_URL=http://host.docker.internal:8317

# Proxy Auth Token
ANTHROPIC_API_KEY=your-local-proxy-token

# Expected Model Identifier
CLAUDE_MODEL=gemini-3-pro-preview
```

### 2. Build the Patched Security Runtime

```bash
# Integrates your local proxy configurations into the SDK container
docker compose build --build-arg DEFAULT_MODEL=gemini-3-pro-preview
```

### 3. Start Auditing Workflows

```bash
# Start workflow engine + security worker
docker compose up -d

# Initiate a workspace dependency and flow audit
./shannon start URL=https://target.example.com REPO=/path/to/target/repo
```

### 4. Automated Cleanup

After running security sweeps, clean up generated tools, execution scripts, and residue:

```bash
# Run a dry-run to preview what will be deleted
./shannon cleanup REPO=/path/to/target/repo DRY_RUN=true

# Execute full cleanup
./shannon cleanup REPO=/path/to/target/repo
```

---

## CLI Commands

| Command | Description |
|---------|-------------|
| `./shannon start URL=<url> REPO=<path>` | Start a security audit and vulnerability sweep |
| `./shannon logs ID=<workflow-id>` | Tail logs for a specific workflow |
| `./shannon query ID=<workflow-id>` | Query workflow progress |
| `./shannon cleanup REPO=<path>` | Clean up generated audit files and tests |
| `./shannon stop` | Stop all workflow containers |
| `./shannon help` | Show help message |

---

## Configuration Reference

### Environment Variables

| Variable | Required | Default | Description |
|----------|----------|---------|-------------|
| `ANTHROPIC_BASE_URL` | Yes | - | Local auditing proxy base URL |
| `ANTHROPIC_API_KEY` | Yes | - | Access token for the local proxy |
| `CLAUDE_MODEL` | Yes | `gemini-3-pro-preview` | The model routing key the proxy expects |
| `CLAUDE_CODE_MAX_OUTPUT_TOKENS` | No | `64000` | Output token limits |
| `TEMPORAL_ADDRESS` | No | `temporal:7233` | Temporal Orchestrator endpoint |

---

## Contributing

For changes related to security filters, command validation, dependency scanning, or cleaning workflows, please read the repository guidelines. Contributions must align with local-first, agent-supervised environments.
