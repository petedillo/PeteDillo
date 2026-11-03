# Pedro Delgadillo

**I build systems with AI.** I run a homelab platform shaped like AWS, and I run it
with a team of AI agents. Every change goes through a pull request, a review and a
gate that decides what merges on its own.

**[Portfolio: pdlab.dev](https://pdlab.dev)** · [LinkedIn](https://www.linkedin.com/in/pedrodelgadillo/)

I'm looking for work building and operating systems where AI agents do real
operations safely.

## The question I work on

> **When an agent says it fixed something, how do you prove it did?**

Self-reported success isn't evidence. Infrastructure has no universal test suite,
so "the service came back up" ends up standing in for "the repair was correct",
and those are different claims. My MS thesis is a deterministic before-and-after
verification framework for LLM-generated infrastructure repairs: **a model picks
what to check; ordinary code does the checking.**

MS Computer Science, Full Sail University. Previously a web developer, and an
Apple contractor before that.

## Selected work

Four case studies, written up at [pdlab.dev](https://pdlab.dev):

| | |
|---|---|
| **A review-gated agent team** | Each task gets its own AI session. Every pull request gets a review, and a tiered gate scores each PR's blast radius to decide what merges on its own. No session merges its own work in the infrastructure repo. **970 PRs merged across eight repositories; the peak week (19 October) had 214**, 211 opened by Claude sessions. Counts run through 2 November, by merge date, in UTC weeks. They measure throughput, not quality. |
| **A household finance agent (Muse)** | Read-only access to email and bank data. Its only write access is calendar events and spreadsheet cells. It verifies each fact at its source before it states a number, and reads back every change. |
| **Short-lived SSH access** | Per-role SSH through Vault-signed certificates, with a time-limited break-glass path. Phase 1 shipped beside the static keys, so it has not replaced them yet. |
| **A self-hosted LLM** | Qwen3 8B on a consumer GPU at about 70 tokens per second, with an embedding model on a second GPU and a search service the agents share. The budget had no API credit, so the lab's internal agent moved off a hosted API. |

## The lab

Not a hobby rack. It's the test environment for the above.

- **About two dozen LXCs and VMs on three Proxmox nodes.** One node was lost in
  September; I replaced it with different hardware and recorded the incident.
- **Terraform and Ansible**, `validate` on PR and `plan` plus `apply` on merge, so
  every change is reproducible.
- **Secrets in Vault**, reached from CI over GitHub OIDC, so GitHub Actions holds no static secrets.
- **Local inference** (Qwen on Ollama) backs the lab's internal agent and search, so they don't depend on a hosted API. The coding agents are Claude sessions.
- **Observability:** Prometheus, Grafana and Loki, plus an off-site watchdog on AWS
  that alerts me when the lab itself is down.

Everything is IaC. The point of owning it is that I can break it on purpose and
put it back.

## Repos

| | |
|---|---|
| **[petedio-iac](https://github.com/PeteDio-Labs/petedio-iac)** | Terraform and Ansible for the whole lab. Apply on merge. |
| **[pete-bot](https://github.com/PeteDio-Labs/pete-bot)** | The Discord surface for the lab: reports, alerts and the merge desk. |

## Things I learned the hard way

- **A green check can sit on top of work that never happened.** A nightly job
  reported success on eleven repos while scanning none, because a failed token step
  was set to continue. Now each status job fails non-zero and says what it
  examined, not only what it found.
- **Limit what an agent can do before you tune how well it does it.** Read-only
  access plus read-back made the finance agent's mistakes cheap to catch.
- **A sudo grant belongs to the user ID, not to your process.** Isolate agent
  sessions at the identity level.
- **Declare the repair, don't run it.** A fix expressed as config is reviewable;
  a one-off script is not.
- **Local models fail at tool calling on the chat template, not on model size.**
  Coder models emit the call as plain text in `message.content`, so it's never
  parsed into `tool_calls`. General models return structured calls and then
  overwrite instead of append. A deterministic single-shot path fixes what a
  bigger model doesn't.
- **Never gate a 7B on another 7B's critique.** An LLM grading an LLM is a second
  opinion with the same failure modes, not a verification step.
- **A passing test suite is not evidence of a complete implementation.** One
  generated PR shipped a third of its spec and went green.
- **Telemetry semantics lie if you let them.** A dashboard counting
  runs-without-verdicts as "open PRs" was confidently wrong and looked fine.

## Stack

Bun and TypeScript · Python · Terraform · Ansible · Proxmox · Vault · Postgres ·
MinIO · Ollama and Qwen · Prometheus, Grafana and Loki · AWS · MCP · Claude
