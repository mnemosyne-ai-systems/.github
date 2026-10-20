# Mnemosyne AI Systems

**Memory and coordination infrastructure for AI agents.**

We build open-source building blocks that give multi-agent systems durable
memory and reliable coordination, without forcing every application team to
reinvent distributed systems.

Our projects share the same bias: keep the data plane simple, make failure
semantics explicit, and document the sharp edges instead of hiding them.

---

## Projects

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

[README](https://github.com/mnemosyne-ai-systems/mneme#readme) ·
[Architecture](https://github.com/mnemosyne-ai-systems/mneme/blob/main/ARCHITECTURE.md) ·
[API](https://github.com/mnemosyne-ai-systems/mneme/blob/main/README.md#api)

---

### [Chorus](https://github.com/mnemosyne-ai-systems/chorus) — distributed agent state on TiKV

Chorus is a single source of truth for multi-agent frameworks: state storage
plus distributed coordination — sessions, leases, claims, and cascades — on a
sharded TiKV transactional store. No Redis, etcd, ZooKeeper, or message queue in
the critical path.

- **TiKV (TxnKV) as the only source of truth**, with PD providing TSO and regions
- **Application-layer sharding** — nodes own shards; clients cache routes and follow redirects
- **Three tiers of concurrency control** — CAS, claim, and lease self-check with demotion, always with a safety margin against TSO skew
- **In-place rebalance** — drain-then-cut split/merge and live shard transfer
- **Explicit failure semantics** — fail-closed on missing TSO, bounded redirect chasing, ABA-free demotion

**Stack:** Go, TiKV, PD, gRPC, protobuf

[README](https://github.com/mnemosyne-ai-systems/chorus#readme) ·
[Design](https://github.com/mnemosyne-ai-systems/chorus/blob/main/DESIGN.md) ·
[Benchmarks](https://github.com/mnemosyne-ai-systems/chorus/blob/main/BENCHMARKS.md) ·
[Operations](https://github.com/mnemosyne-ai-systems/chorus/blob/main/deploy/OPERATIONS.md)

---

## How we build

- **Proven storage over bespoke consensus.** Use TiKV, Pulsar, Redis, and
  ClickHouse for what they are good at; keep coordination logic in the
  application where it can be tested.
- **Failure semantics are part of the API.** Fail closed, bound retries, state
  the safety margins, and make recovery a documented procedure.
- **Honest limitations.** Known gaps and investigated incidents are written
  down next to the design, not discovered by users in production.
- **Cheap defaults.** Features that incur external cost, such as LLM calls, are
  opt-in.

## Status

Both projects are pre-1.0 and under active development. APIs may change; each
repository documents its own compatibility and operational guarantees.

## Contributing and security

Contributions, bug reports, and design discussions are welcome in the
individual repositories. Please use the issue templates and read the
contributing and security guidance in each project before opening a PR.

## License

All projects are released under the [Apache License 2.0](https://www.apache.org/licenses/LICENSE-2.0).
