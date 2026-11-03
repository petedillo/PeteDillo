# Pedro Delgadillo

I build systems with AI, and I look for work where an AI agent runs real
operations safely. I build the tooling on hardware I own, and I'm working on one
question:

> **When an agent says it fixed something, how do you prove it did?**

Self-reported success isn't evidence. Infrastructure has no universal test suite
the way software does, so "the service came back up" ends up standing in for
"the repair was correct" — and those are not the same claim. My MS thesis is a
deterministic before-and-after verification framework for LLM-generated
infrastructure repairs: **a model picks what to check; ordinary code does the
checking.**

MS Computer Science, Full Sail University. Previously a web developer, and an
Apple contractor before that.

## Selected work

Four case studies from running the lab, written up at **[pdlab.dev](https://pdlab.dev)**:

- **A review-gated agent team.** Each task gets its own AI session, every pull
  request gets a review, and a tiered merge gate scores each PR's blast radius.
  970 PRs merged across eight repositories, with a peak week of 214. About 98%
  of that week's PRs were opened by Claude sessions. Throughput is the measure,
  not quality.
- **A household finance agent (Muse).** Read-only access to email, calendar and
  bank data. It verifies each fact at its source before it states a number and
  reads back every change it makes.
- **Short-lived SSH access.** Per-role SSH through Vault-signed certificates, with
  a time-limited break-glass path.
- **A self-hosted LLM.** Qwen3 8B on a consumer GPU at about 70 tokens per second,
  with a shared search service, after the budget left no API credit.

## The lab

Not a hobby rack. It's the test environment for the above.

- **About two dozen LXCs and VMs** across three Proxmox nodes
- **Terraform + Ansible**, validate-on-PR, apply-on-merge, so changes are reproducible
- **Local inference** on consumer GPUs, with no frontier API in the loop
- Vault, MinIO, self-hosted runners, Cloudflare tunnel, Prometheus and Grafana

Everything is IaC. The point of owning it is that I can break it on purpose and
put it back.

## What's here

| | |
|---|---|
| **[petedio-iac](https://github.com/PeteDio-Labs/petedio-iac)** | Terraform + Ansible for the whole lab. Apply-on-merge. |
| **[mission-control-backend](https://github.com/PeteDio-Labs/mission-control-backend)** | Control plane — task queue with row locking, zombie reaping, agent health transitions, and a typed human-approval gate over seven mutating action types. |
| **[shared](https://github.com/PeteDio-Labs/shared)** | Agent harness: deterministic / tool-loop / plan-execute execution modes, with `gate()` and `dryRun()` hooks on the tool interface. |
| **[mcp-homelab](https://github.com/PeteDio-Labs/mcp-homelab)** | MCP server exposing the lab as 33 tools, with an approval handshake. |

## Things I learned the hard way

- **Local models fail at tool calling on the chat template, not on model size.**
  Coder models emit the call as plain text in `message.content` so it's never
  parsed into `tool_calls`; general models return structured calls and then
  overwrite instead of append. Identical on `/v1` and native `/api/chat`. Swapping
  to a bigger model doesn't fix it — a deterministic single-shot path does.
- **A passing test suite is not evidence of a complete implementation.** One
  generated PR shipped a third of its spec and went green.
- **Never gate a 7B on another 7B's critique.** An LLM grading an LLM is not a
  verification step, it's a second opinion with the same failure modes.
- **Telemetry semantics lie if you let them.** A dashboard counting
  runs-without-verdicts as "open PRs" is confidently wrong and looks fine.

## Stack

Bun/TypeScript · Python · Terraform · Ansible · Proxmox · Postgres · Ollama ·
Prometheus · MCP

---

<a href="https://pdlab.dev">Portfolio</a> · <a href="https://www.linkedin.com/in/pedrodelgadillo/">LinkedIn</a>
