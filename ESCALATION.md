# The connected agent as confused deputy

Verified against Microsoft Learn on **2026-10-28**.

Most of what goes wrong with connected agents is an availability problem: somebody cannot
reach something. This one is the opposite, and it lives in the guidance article rather than
the how-to, which is why fewer people read it.

## The shape of it

> The connected agent might have access to things the parent agent doesn't. Ensure that
> calling the connected agent doesn't inadvertently bypass restrictions. For example, if the
> parent agent isn't allowed to delete records but the connected agent can, the parent agent
> shouldn't call the connected agent in scenarios where deletion might happen without proper
> approval.

A caller with fewer privileges reaches a callee with more, and the callee acts. That is a
confused deputy, and nothing in the platform prevents you from assembling one. The guidance
is to treat a connected agent call as a powerful action and subject it to the checks or user
consent it warrants.

Connected agents are also noted as a way to **bypass plan limits**, which is a capability
worth knowing and a reason the privilege gap tends to widen rather than close over time.

## Questions for a review

1. For each connected agent, what can it do that the parent cannot?
2. Is there a path by which a user who could not perform an action directly can cause it
   through delegation?
3. Where does the approval for that action live? If the answer is "the agent asks", that is
   an instruction, not a control.
4. Is the delegation itself logged somewhere a reviewer will look?

## On where controls belong

The orchestrator decides when to delegate. A rule expressed only as instruction text is
therefore subject to the orchestrator's judgment, which is the same reason
[hooks](https://learn.microsoft.com/microsoft-copilot-studio/agents-experience/hooks) exist
for deterministic behavior. Put the hard control where the orchestrator cannot route around
it: the connector, the downstream system, or the identity the action runs under.

## Audit

Each connected agent is a separate agent, so it produces **separate transcripts**. Correlating
the parent session with the connected session is a deliberate step, and the documentation says
identifiers in the telemetry are typically what link them. Plan the correlation before the
incident, not during it.

## Context

A **connected** agent has a context-inclusion setting controlling whether it receives
conversation history. Confirm it rather than assuming. A **child** agent always receives the
parent's context.

If the conversation can contain anything sensitive, that setting decides whether it crosses
into an agent with a different access list.

## Sources

- [Multi-agent orchestration patterns and best practices](https://learn.microsoft.com/microsoft-copilot-studio/guidance/multi-agent-patterns)
- [Add a connected agent to an agent (GitHub Copilot)](https://learn.microsoft.com/microsoft-copilot-studio/agents-experience/add-agent-connected)
