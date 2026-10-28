# A worked example: the CRONUS order-qualification agents

A real connected-agent topology, built for a conference demo against CRONUS USA, Inc., the
standard Microsoft Dynamics demo company. Inspected in the maker portal on **2026-10-28**.

It is useful precisely because it is ordinary. Nobody built it as a governance puzzle, and it
still has every joint the [checklist](CHECKLIST.md) asks about.

## The shape

```text
CRONUS Order Orchestrator          Standard harness, Claude Sonnet 4.6
  |  no tools of its own
  |
  +-- CRONUS Sales Specialist      Claude Opus 4.6     -> Cronus Master MCP Server
  +-- CRONUS Finance Specialist    Claude Sonnet 4.6   -> Cronus Master MCP Server
  +-- CRONUS Inventory Specialist  Claude Sonnet 4.6   -> Cronus Master MCP Server
```

None of the three specialists has connected agents of its own, so the graph is one level deep.

The orchestrator helps an inside sales manager decide whether to commit to an incoming order.
Its delegation order, from its own instructions:

1. Call **Sales** first with the customer name. Get back the canonical customer number, the
   negotiated unit price, and any notes.
2. Compute the order amount as quantity times that negotiated price.
3. Call **Finance** and **Inventory** in parallel. Finance takes customer number and order
   amount; Inventory takes item number, quantity and required-by date.
4. Synthesize one recommendation. If any specialist fails or data is missing, default to
   "Hold and escalate".

## The access matrix, filled in

| Agent | Role | Tools | Connected agents | Privileges the parent lacks |
|---|---|---|---|---|
| CRONUS Order Orchestrator | Primary | **none** | Sales, Finance, Inventory | n/a |
| CRONUS Sales Specialist | Connected | Cronus Master MCP Server | none | **Yes: the MCP server** |
| CRONUS Finance Specialist | Connected | Cronus Master MCP Server | none | **Yes: the MCP server** |
| CRONUS Inventory Specialist | Connected | Cronus Master MCP Server | none | **Yes: the MCP server** |

## What the matrix shows

**All data access lives in the children.** The orchestrator holds no tools at all. Every fact
it reports, the customer number, the credit math, the stock position, arrives through a
specialist.

**All three specialists hold the same tool.** Not one MCP server each. The same
`Cronus Master MCP Server`.

That second row is the one worth sitting with. The division of labour between Sales, Finance
and Inventory is real in the instructions and absent in the permissions. Inventory's
instructions say, in as many words, not to provide pricing or credit information. Nothing stops
it. The scope separation is a prompt, and the three agents stand on identical ground.

Two consequences, pulling in opposite directions:

**Share the parent alone and you have shared nothing useful.** A user with only the
orchestrator gets an agent that reasons and delegates, and every delegation fails. By the
orchestrator's own error handling that surfaces as "Hold and escalate", which reads like a
business judgment rather than a permissions failure. A plausible wrong answer is the worst
symptom to debug.

**Share any one specialist and you have shared the whole MCP server.** The orchestrator holds
the sequencing, the exposure math, the response format and the rule never to approve on
missing data. A user talking to Finance directly gets none of that, and so does a user talking
to Inventory, because the tool behind both is the same.

Neither is a defect in a demo. Both are what a reviewer should expect to find, which is why
the matrix is worth filling in before the sharing conversation rather than after.

## Questions this answers from the checklist

- *Who owns each connected agent?* One owner here, which is why the demo worked. Split that
  across a team and the agents cannot even be connected until sharing happens in both
  directions.
- *Does any connected agent hold a privilege the parent lacks?* All three, and the same one.
- *How many hops to verify?* Four, with Finance and Inventory in parallel.
- *Blast radius of sharing one specialist?* The full MCP server, without the orchestrator's
  guardrails.

## Verified and not verified

**Observed in the maker portal on 2026-10-28:** every agent name, harness, model, tool, and
connected-agent list in the tables above, read from each agent's Overview page.

**Not exercised:** the sharing behaviour itself. Nobody signed in as a second user and walked
the path, which is the one test [CHECKLIST.md](CHECKLIST.md) says matters most. Everything
here about what a non-owner would experience follows from the documented rules, not from a run.

**Harness note:** this topology runs on the **Standard** harness. Microsoft's connected-agents
how-to is written for the GitHub Copilot harness and names that harness in its prerequisites
for both parent and child, so harness-specific claims need checking against the experience you
are actually in.
