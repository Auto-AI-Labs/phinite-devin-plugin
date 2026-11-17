# Phinite plugin for Devin

Build, ship, run and debug **Phinite agent graphs** from Devin (app sessions and the Devin CLI). The plugin connects
Devin to the hosted **Phinite MCP server** (`https://mcp.dev.phinite.ai/mcp`) and adds the Phinite playbook as skills. It is generated
from the Claude plugin, so both behave the same, with the same `/phinite:*` commands.

## Install

- **Devin CLI:** `devin plugins install Auto-AI-Labs/phinite-devin-plugin`, or
  `devin plugins install --local <path to this folder>` while developing.
- **Devin app:** Customize → Plugins → add `Auto-AI-Labs/phinite-devin-plugin`, then Connect the `phinite` MCP.

You need Python 3.9+ for the project script (Devin's machines have it). No Phinite token is stored in the repo.

To use another server (production or a local one), set `PHINITE_MCP_URL`, for example
`PHINITE_MCP_URL=http://localhost:8080/mcp`.

## Sign in

The server uses OAuth with dynamic client registration, so there is nothing to configure. In the app:
**Customize → MCPs → phinite → Connect**. In the CLI: `devin mcp login phinite`. A browser opens on the Phinite
login (Google, GitHub or email); pick the org and the workspace. The connection is personal (`tokenScope: user`):
each member signs in as themselves.

## Use

| Command | What it does |
|---|---|
| `/phinite:login [workspace]` | Sign in, check the sign-in, or switch workspace |
| `/phinite:new <folder> [schema] [idea]` | Scaffold a use-case project and design its graphs with you |
| `/phinite:deploy <folder>` | Dry run, then deploy: tools upserted (published when changed), graphs created or updated |
| `/phinite:test <graph> [message \| user_variables]` | Chat or run the graph autonomously, read the log, diagnose |
| `/phinite:ship <graph> [env]` | Publish → build → environment → trigger or Chat API → API key → call it from outside |
| `/phinite:logs <workflow_id>` | Explain a run: tool calls, inputs, outputs, variables, errors |
| `/phinite:eval <graph> [focus]` | Evaluate with LLM judges: cases, metrics, failures and fixes |
| `/phinite:build <idea>` | A whole use case end to end (the Claude plugin's `phinite-builder` agent) |

In a Devin session, "this machine" is Devin's machine: put the use-case folder in the repo Devin works on.

## What's inside

- **Skills** (the same as the Claude plugin's, from the Phinite playbook): `phinite-design`, `phinite-ship`,
  `phinite-debug`, `phinite-api` and `phinite-project`, plus `phinite-quality` for evaluations (LLM judges,
  custom metrics, production scoring).
- **A local project script**, `skills/phinite-project/scripts/phinite_project.py` (Python 3.9+, standard library
  only). It scaffolds use-case folders from `templates/`, runs `graph_spec.py` on your machine, bundles the project
  and sends files through the server's upload links. The hosted server can't read your disk.
- `tests/`: the offline tests for that script: `python -m unittest tests/test_offline.py`.

The MCP tools and the rules they apply are listed in the
[phinite-mcp README](https://github.com/Auto-AI-Labs/phinite-mcp#tools).

## Security notes

- **No secrets on disk or in the chat.** The client stores and refreshes the OAuth token; the assistant never sees
  it. Env values go from your `.env` straight to an upload link and never pass through the chat.
- **Upload links** are valid for 15 minutes and for one purpose. The project script accepts only https links on
  `*.phinite.ai` (or the `PHINITE_MCP_URL` host, or localhost) with the `/u/<ticket>` path, so a link from a file,
  web page or message is refused.
- **Hidden files** (`.env`, `.git`…) in folders are never sent, and `KNOWLEDGE` files must be inside the project.
- **Env variables are workspace-wide**: prefix a use case's own secrets (`MYUC_SLACK_TOKEN`), and the assistant asks
  before overwriting a name that already exists.
- **`graph_spec.py` is code**: `deploy` runs it on your machine. Deploy only projects you wrote or trust.
