# Connected agent access

Sharing a Copilot Studio agent does not share what it reaches. This repo is the checklist and
the access matrix for working out what else you have to share, and the review questions for
the case nobody checks.

Companion to [Works on My Agent](https://www.theworkbench.blog) on
[The Workbench](https://www.theworkbench.blog).

> The rule everything here follows from, verbatim from the documentation: **access to a parent
> agent doesn't automatically grant access to its connected agents. Share each connected agent
> separately with every intended user or group.**

> **No script, on purpose.** None of this is exposed anywhere a script could read honestly:
> there is no documented API that enumerates an agent's connected agents, their share lists,
> and the identity each connection runs under. A tool that claimed to would be invention. The
> checklist is the deliverable.

## What is here

| Path | What it is |
|---|---|
| [`CHECKLIST.md`](CHECKLIST.md) | **Start here.** Pre-share checklist, the access matrix to fill in, the workflow-hop table, and how to verify as somebody who is not you. |
| [`EXAMPLE.md`](EXAMPLE.md) | A **real** topology with the matrix filled in: a four-agent order-qualification demo where the parent holds no tools and all three children share one MCP server. |
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
