# poslovna — Claude Code plugin

The Croatian business registers, as an MCP server plus a skill that teaches an agent how to read
the answers.

```sh
/plugin marketplace add dlauc/poslovna-plugin
/plugin install poslovna
```

Then get a key at <https://poslovna.com/api/v1/mcp/setup> and export it:

```sh
export POSLOVNA_API_KEY=ps_live_…
```

That is the whole setup. The plugin declares the MCP server, so there is no `claude mcp add` and
no config file to edit; the manifest references the variable rather than a key, so this repository
is publishable and your credential stays out of it.

## What is in here

| | |
|---|---|
| `.claude-plugin/plugin.json` | declares the MCP server and the skill |
| `skills/poslovna/SKILL.md` | tool selection, and the three kinds of absence |
| `skills/poslovna/references/query-grammar.md` | the operator syntax, field by field |
| `skills/poslovna/references/registers.md` | what each register actually covers |
| `skills/poslovna/references/publishing.md` | what may be restated in a published document |

## Why a skill and not just tool descriptions

The server already ships tool descriptions and a set of `instructions`, and both are loaded on
every session — so both have to stay short. Measured: seven tool descriptions plus the
instructions are about 2,100 tokens before a single question is asked.

The material that does not fit there is exactly the material that prevents wrong answers: the
operator grammar, the coverage boundaries of each register, and the redistribution rules. A skill
is read **on demand**, so those can be as long as they need to be and cost nothing until they are
relevant.

## Clients other than Claude Code

The skill format is shared across several agents, so `skills/poslovna/` can be copied into any
of their skills directories. For the server itself, or for a client that cannot send a header at
all (claude.ai, ChatGPT — their connector UIs accept OAuth or nothing), the setup page prints the
right configuration per client: <https://poslovna.com/api/v1/mcp/setup>.
