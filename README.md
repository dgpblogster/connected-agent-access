# Connected agent access

Sharing a Copilot Studio agent does not share what it reaches. This repo is the checklist and
the access matrix for working out what else you have to share, and the review questions for
the case nobody checks.

Companion to [Works on My Agent, the New "Works on My Computer"](https://www.theworkbench.blog)
on [The Workbench](https://www.theworkbench.blog). The agents it is written about are published
at [cronus-order-management-agents](https://github.com/dgpblogster/cronus-order-management-agents).

> The rule everything here follows from, verbatim from the documentation: **access to a parent
> agent doesn't automatically grant access to its connected agents. Share each connected agent
> separately with every intended user or group.**

> **No script, on purpose.** Some of the matrix *is* readable: `pac copilot clone` exports an
> agent's connected agents, its per-tool grants, its connection references and its
> context-inclusion setting, and [`EXAMPLE.md`](EXAMPLE.md) is filled in from exactly that. What
> no documented API gives you is the column that decides whether the thing works for anyone
> else: **who each agent is shared with**. A script that claimed to produce the whole matrix
> would be inventing the half that matters. The checklist is the deliverable.

## What is here

| Path | What it is |
|---|---|
| [`CHECKLIST.md`](CHECKLIST.md) | **Start here.** Pre-share checklist, the access matrix to fill in, the workflow-hop table, and how to verify as somebody who is not you. |
| [`EXAMPLE.md`](EXAMPLE.md) | A **real** topology with the matrix filled in, from its own exported definitions: a four-agent order-qualification demo where the parent holds no tools and the three children share one MCP connector but not the same grants on it. |
| [`ESCALATION.md`](ESCALATION.md) | The case that is a security problem rather than an availability one: a connected agent holding privileges the parent lacks. Review questions and where controls belong. |

## The three non-transitive things

Sharing the parent covers none of them:

1. **Connected agent access.** Each one is shared separately, with every intended user or
   group.
2. **Workflow environment access.** If a workflow in the path uses an agent node with the
   user's connection, that user needs access to the workflow's environment. The agent node
   fails *before* calling the second agent, so the symptom appears at the hop.
3. **Connections.** They are tied to each individual user's identity, which is why an agent
   can work for you and not for anyone else.

See [`EXAMPLE.md`](EXAMPLE.md) for what this looks like in a topology that actually exists.

## Verified

All claims checked against Microsoft Learn on **2026-10-28**:

- [Add a connected agent to an agent (GitHub Copilot)](https://learn.microsoft.com/microsoft-copilot-studio/agents-experience/add-agent-connected)
- [Share agents with other users and makers (GitHub Copilot)](https://learn.microsoft.com/microsoft-copilot-studio/agents-experience/authoring-share-agent)
- [Multi-agent orchestration patterns and best practices](https://learn.microsoft.com/microsoft-copilot-studio/guidance/multi-agent-patterns)

## Contributing

This repository is a publishing surface for the blog rather than a community project, so it
does not accept pull requests. If something here no longer matches the documentation, the
comments on the related blog post are the right place.

## License

MIT. See [LICENSE](LICENSE).
