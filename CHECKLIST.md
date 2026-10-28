# Pre-share checklist for a connected-agent setup

All claims verified against Microsoft Learn on **2026-10-28**.

The rule that drives all of this: **access to a parent agent does not automatically grant
access to its connected agents. Share each connected agent separately with every intended
user or group.**

## Before you share

- [ ] List every connected agent the primary can reach, including A2A, Foundry and
      Microsoft 365 Agents SDK agents
- [ ] For each one, confirm the **maker** of the primary either owns it or has it shared
      with them, or the connection cannot be made at all
- [ ] Share each connected agent with the same users and groups as the parent
- [ ] List every workflow in the path that uses an **agent node**
- [ ] For each, confirm intended users have access to **that workflow's environment**
- [ ] List every connection each agent uses, and note whose identity it runs under
- [ ] Decide per connection: service principal, environment-level connection, or individual
      authentication
- [ ] Check each connected agent's **context-inclusion** setting
- [ ] Record whether any connected agent holds a privilege the parent does not

## Access matrix

Fill one row per agent in the path. The parent is row one.

| Agent | Type | Owner | Shared with | Connections, and whose identity | Privileges the parent lacks | Context inclusion |
|---|---|---|---|---|---|---|
| | Primary / Copilot Studio / A2A / Foundry / SDK | | | | | n/a for parent |
| | | | | | | on / off |

A row where **Privileges the parent lacks** is not empty is the row a reviewer should look at
first. See [ESCALATION.md](ESCALATION.md).

## Workflow hops

| Workflow | Environment | Called by | Uses agent node | Runs as |
|---|---|---|---|---|
| | | | yes / no | user connection / service principal |

If a workflow uses an agent node with the **user's** connection, every intended user needs
access to that workflow's environment. Sharing the agent does not grant it, and the agent node
action fails **before** calling the second agent, so the symptom shows at the hop rather than
at the cause.

## The three non-transitive things

| What | Does sharing the parent cover it? | Failure looks like |
|---|---|---|
| Connected agent access | **No** | Delegation fails for the user, works for the owner |
| Workflow environment access | **No** | Agent node fails before the second agent is called |
| Connections | **No**, they are per user identity | Agent works for you, not for them |

## Verification

Do not test as yourself. The owner has access to everything by construction, which is exactly
why the demo passed.

- [ ] Sign in as a real user who is not an owner of any agent in the path
- [ ] Complete the whole path end to end, through every hop
- [ ] Confirm the delegated step actually ran rather than being answered by the parent
- [ ] Correlate the parent and connected transcripts, they are **separate**; the telemetry
      identifiers are what link them

## Sources

- [Add a connected agent to an agent (GitHub Copilot)](https://learn.microsoft.com/microsoft-copilot-studio/agents-experience/add-agent-connected)
- [Share agents with other users and makers (GitHub Copilot)](https://learn.microsoft.com/microsoft-copilot-studio/agents-experience/authoring-share-agent)
- [Multi-agent orchestration patterns and best practices](https://learn.microsoft.com/microsoft-copilot-studio/guidance/multi-agent-patterns)
