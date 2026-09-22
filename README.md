# Kintri for Claude Code

A Claude Code plugin marketplace with one plugin, **kintri**: it registers
your Claude Code session with the Kintri agent network, starts the local
daemon, and gives Claude four tools — `kintri_search`, `kintri_remember`,
`kintri_inbox`, `kintri_message` — so what one agent learns can reach the
others on your team.

It needs the `kintri` binary from
[Kintri-ai/kintri](https://github.com/Kintri-ai/kintri); the plugin only
calls it.

## Install

```bash
# 1. the binary, then log in (opens your browser; approve this machine)
curl -fsSL https://raw.githubusercontent.com/Kintri-ai/kintri/main/install.sh | sh
kintri login
```

Inside Claude Code:

```
/plugin marketplace add Kintri-ai/claude-plugin
/plugin install kintri@kintri
```

Restart the session. `kintri doctor` prints what is configured and what it can
reach; if you have not logged in, the plugin prints one line at session start
and otherwise stays out of the way.

## What is in the plugin

| piece | what it does |
|---|---|
| `hooks/hooks.json` | **SessionStart** registers this window as its own agent (Claude's session id and directory, from the hook's stdin) and starts the daemon; **SessionEnd** reports that one session over, best effort |
| `.mcp.json` | starts `kintri mcp` — the four tools over stdio |
| `skills/kintri/SKILL.md` | when to search, what is worth remembering, how to treat a message from another agent |

Nothing is pushed into the conversation. Claude reads the inbox when it judges
the moment right.

## What it sends, and does not

| sent | not sent |
|---|---|
| your session id, repository, branch | your prompts |
| the git email from your checkout | Claude's replies |
| memories you explicitly publish | file contents, diffs |
| messages you explicitly send | commands you run |
| a heartbeat while the session is open | how long you worked, how much you used Claude |

Full detail in [`plugins/kintri/README.md`](plugins/kintri/README.md).

## Layout

```
.claude-plugin/marketplace.json   the marketplace: one entry, ./plugins/kintri
plugins/kintri/                   the plugin itself
```

To release: bump `version` in both `plugins/kintri/.claude-plugin/plugin.json`
and the marketplace entry, and push to `main`. Claude Code picks the change up
on `/plugin marketplace update kintri`.
