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

| Agent | Role | Connector | MCP tools actually granted | Connection reference | Connected agents |
|---|---|---|---|---|---|
| CRONUS Order Orchestrator | Primary | **none** | **none** | none | Sales, Finance, Inventory |
| CRONUS Sales Specialist | Connected | Cronus Master MCP Server | `search_customer`, `get_customer_details`, `get_customer_pricing`, `get_order_history` | `cr83a_cronusSalesContextAssistant` | none |
| CRONUS Finance Specialist | Connected | Cronus Master MCP Server | `get_credit_profile`, `calculate_credit_exposure` | `cr83a_financeCreditEvaluator` | none |
| CRONUS Inventory Specialist | Connected | Cronus Master MCP Server | `get_inventory`, `check_availability` | `cr83a_inventoryFulfillmentSpecialist` | none |

Every specialist's action declares `kind: UseSpecificTools` and runs with
`connectionProperties: mode: Invoker`, meaning the signed-in user's connection.

## What the matrix shows

**All data access lives in the children.** The orchestrator holds no tools at all. Every fact
it reports, the customer number, the credit math, the stock position, arrives through a
specialist.

**The portal and the source tell different stories, and the source is right.** In the maker
portal all three specialists show the same entry under Tools, `Cronus Master MCP Server`, which
reads like identical access. The cloned YAML shows otherwise: each action declares
`UseSpecificTools` with a **disjoint** tool list, and each agent has its **own** connection
reference on that shared connector. Sales cannot call `get_credit_profile`. Finance cannot call
`get_inventory`.

So the division of labour is enforced, not merely described. It is simply enforced somewhere
the portal's Tools pane does not show you.

**CORRECTION (2026-10-28):** an earlier version of this file claimed the separation was a
prompt rather than a permission, on the strength of the portal view alone. That was wrong. The
tool scoping above is from the agents' own definitions, cloned with `pac copilot clone`.

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
- *Blast radius of sharing one specialist?* That specialist's tool subset, without the
  orchestrator's sequencing and guardrails. Not the whole connector.

## Verified and not verified

**Observed on 2026-10-28:** agent names, harness, models and the connected-agent list from
each agent's Overview page in the maker portal. The connector, per-agent tool grants,
connection references and `Invoker` mode from the agent definitions cloned with
`pac copilot clone`, published at
[cronus-order-management-agents](https://github.com/dgpblogster/cronus-order-management-agents).

**Not exercised:** the sharing behaviour itself. Nobody signed in as a second user and walked
the path, which is the one test [CHECKLIST.md](CHECKLIST.md) says matters most. Everything
here about what a non-owner would experience follows from the documented rules, not from a run.

**Harness note:** this topology runs on the **Standard** harness. Microsoft's connected-agents
how-to is written for the GitHub Copilot harness and names that harness in its prerequisites
for both parent and child, so harness-specific claims need checking against the experience you
are actually in.
