# Mnemosyne AI Systems

**Memory and coordination infrastructure for AI agents.**

We build infrastructure for AI agents: durable memory and reliable
coordination, without forcing every application team to reinvent distributed
systems.

We open-source the layers where adoption and community compound, and keep the
deepest coordination core focused for design partners.

---

## Projects

| Project | What it provides | Status |
|---|---|---|
| **Mneme** | Long-term memory: user profiles, session summaries, governance | Open source |
| **Chorus** | Distributed state and coordination for multi-agent runtimes | Private beta |

### [Mneme](https://github.com/mnemosyne-ai-systems/mneme) — long-term memory for AI agents

Mneme turns conversation history, agent traces, and explicit memory events into
two durable artifacts: **user profiles** and **session summaries**. Both are
served through a unified, multi-tenant HTTP API with low-latency reads and
first-class data governance.

The architecture follows **write decoupling + hot/cold storage split**: Pulsar
absorbs write peaks, Redis serves millisecond reads, and ClickHouse retains data
long-term and powers analytics. Extraction is rule-based out of the box; LLM
refinement is optional and off by default, so the default deployment has zero
LLM cost.

- Async ingestion via Pulsar, with batched and retried writes
- Redis hot cache with read-through to ClickHouse on a miss
- Soft/hard delete, export, cursor pagination, and an audit log for every mutation
- Admin console, Prometheus metrics, OpenTelemetry tracing, health probes
- HPA and ServiceMonitor examples for Kubernetes
- `file-adapter` for ingesting local Markdown / JSONL files

**Stack:** Go, Pulsar, Redis, ClickHouse, Kubernetes

**License:** Apache 2.0

[README](https://github.com/mnemosyne-ai-systems/mneme#readme) ·
[Architecture](https://github.com/mnemosyne-ai-systems/mneme/blob/main/ARCHITECTURE.md) ·
[API](https://github.com/mnemosyne-ai-systems/mneme/blob/main/README.md#api)

---

### Chorus — distributed state and coordination for multi-agent systems

Chorus gives multi-agent runtimes a single source of truth for session state and
coordination. **TiKV provides the durable, transactional storage layer and PD
provides time and region metadata; Chorus adds the coordination semantics on top
of it** — sessions, leases, claims, cascades, shard ownership, and live
rebalancing. No Redis, etcd, ZooKeeper, or message queue in the critical path.

- Durable session state across long-running and multi-process agents
- Leases and claims that fence stale workers after failover
- Live shard movement and rebalance without stopping the data plane
- Fail-closed behavior when the clock source is unavailable
- gRPC API and Go SDK for agent frameworks

**Stack:** Go, TiKV, PD, gRPC, protobuf

**Status:** Private beta. The core is proprietary; evaluation access, benchmark
results, and architecture details are shared with design partners.

[Request design-partner access](https://github.com/mnemosyne-ai-systems/.github/issues)

---

## How we build

- **Proven storage over bespoke consensus.** Use TiKV, Pulsar, Redis, and
  ClickHouse for what they are good at; keep coordination logic in the
  application where it can be tested.
- **Failure semantics are part of the API.** Fail closed, bound retries, state
  the safety margins, and make recovery a documented procedure.
- **Honest limitations.** We write down known gaps and failure modes before
  users hit them.
- **Cheap defaults.** Features that incur external cost, such as LLM calls, are
  opt-in.

## Status

Mneme is open source and pre-1.0; its APIs may change. Chorus is in private beta
and its core remains proprietary while integration surfaces are shared with
partners.

## Contributing and security

Mneme welcomes contributions, bug reports, and design discussions — see its
[CONTRIBUTING.md](https://github.com/mnemosyne-ai-systems/mneme/blob/main/CONTRIBUTING.md)
and [SECURITY.md](https://github.com/mnemosyne-ai-systems/mneme/blob/main/SECURITY.md).
For Chorus, use the access link above. Please report security issues privately
rather than in public issues.

## License

Mneme is released under the [Apache License 2.0](https://www.apache.org/licenses/LICENSE-2.0).
Chorus is proprietary software; commercial and evaluation licenses are available.
