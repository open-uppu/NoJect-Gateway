# NoJect 🛡️ Roadmap to Guardrails Market Parity

**Architected in Alignment with ISO/IEC 27001:2022 & ISO/IEC 42001:2023 Principles**

> **สถานะเอกสาร:** Execution roadmap — ใช้สำหรับจัดลำดับงาน ไม่ใช่คำมั่นวันส่งมอบ
>
> **Planning horizon:** 12 สัปดาห์สำหรับ Core Parity Beta + แผนต่อเนื่องหลัง Beta
>
> **Capacity assumption:** 1 full-time maintainer; ถ้ามี 2–3 คน ให้ทำ workstream แบบขนานตามหัวข้อ 8
>
> **Last updated:** 2026-09-02

---

## 1. Executive Decision

NoJect ไม่ควรชนะด้วยการ clone feature ทุกอย่างของ Guardrails AI, NeMo Guardrails หรือ LLM Guard แต่ควรสร้างตำแหน่งที่ชัดเจนว่าเป็น **self-hosted AI security gateway ที่มี programmable guardrails**:

1. รักษาจุดแข็งเดิม: Go WAF, multi-auth, reverse proxy, tamper-evident audit, PII masking, canary shield และ multi-language SDK
2. ปิด developer-experience gap ที่ขวาง adoption ก่อน: validator contract, structured output, on-fail actions, re-ask และ framework integrations
3. ทำ streaming ให้ปลอดภัยจริง: ห้ามปล่อย chunk ที่ยังไม่ผ่าน policy และต้องตรวจ secret ที่ถูกแบ่งข้ามหลาย chunk
4. สร้าง Hub แบบ **local-first, version-pinned, auditable** ไม่เปิดทางให้ gateway ดาวน์โหลดและรัน arbitrary code อัตโนมัติ
5. ใช้โมเดลหรือไลบรารีภายนอกเป็น optional backend เมื่อเหมาะสม เช่น Presidio, Llama Guard และ NLI models โดยไม่ผูก fast path เข้ากับ heavy ML

### เป้าหมายเมื่อจบ 12 สัปดาห์

ส่งมอบ **Core Parity Beta** ที่มี:

- Validator API/manifest ที่ versioned และใช้ร่วมกันได้ใน Python, TypeScript และ Guard Engine
- SchemaGuard สำหรับ Pydantic และ JSON Schema พร้อม error path ที่นำไปแก้ไขได้
- On-fail actions: `BLOCK`, `ALLOW`, `FIX`, `FILTER`, `REFRAIN`, `REASK`, `FIX_REASK`
- Re-ask แบบมี budget และ caller-owned orchestration เป็นค่าเริ่มต้น
- Local validator registry + CLI MVP พร้อม first-party validators อย่างน้อย 10 ตัว
- LangChain และ LlamaIndex integrations พร้อมตัวอย่าง end-to-end
- gRPC unary transport และ SSE safe-streaming beta โดยยังคง HTTP/JSON fallback
- Benchmark, migration guide, threat model และ release evidence ครบ

### สิ่งที่ไม่ควรสัญญาในรอบ 12 สัปดาห์

- “ครบ 65 validators” — เป้าหมาย Beta คือ contract + 10 first-party validators; 20+ เป็น KPI หลัง launch
- Hosted marketplace หรือ remote inference cloud
- Production GA ของ factuality, topical rails, Kubernetes/WASM และ React SOC SPA
- การรองรับ streaming แบบ zero-buffer ทุก policy; safety ต้องมาก่อน token immediacy

---

## 2. Market Baseline และวิธีอ่านคะแนน

ข้อมูลคู่แข่งเป็น snapshot และเปลี่ยนได้ตลอด จึงใช้ feature/API จากเอกสารทางการเป็นหลัก ส่วนจำนวนดาวเป็นเพียง adoption signal ไม่ใช่ quality score

| Product | ประเภท | จุดแข็งที่ต้องเรียนรู้ | ข้อจำกัดเมื่อเทียบกับตำแหน่งของ NoJect |
|---|---|---|---|
| [Guardrails AI](https://github.com/guardrails-ai/guardrails) | OSS framework | Validator Hub, structured data, on-fail actions, re-ask, streaming validation | ไม่ใช่ perimeter gateway/WAF แบบ NoJect |
| [NVIDIA NeMo Guardrails](https://github.com/NVIDIA-NeMo/Guardrails) | OSS toolkit/runtime | Input/output/dialog/retrieval/execution rails, Colang, streaming rails | Configuration/runtime หนักกว่า YAML policy ที่ NoJect ต้องการ |
| [Protect AI LLM Guard](https://github.com/protectai/llm-guard) | OSS scanner toolkit | Input/output scanner breadth, anonymize/deanonymize vault, toxicity/bias/code/URL checks | Python scanner-centric; ไม่มี Go gateway fast path และ audit chain |
| [Meta Llama Guard](https://github.com/meta-llama/PurpleLlama/tree/main/Llama-Guard) | Safety classification model | Model-based input/output content classification และ policy taxonomy | เป็น backend/model ไม่ใช่ gateway, workflow หรือ audit platform |
| [Azure AI Content Safety](https://learn.microsoft.com/en-us/azure/ai-services/content-safety/overview) | Proprietary SaaS | Text/image moderation, Prompt Shields, groundedness และ protected-material checks | Cloud dependency, regional/language constraints และไม่ตอบโจทย์ self-hosted ทั้งหมด |

### Coverage snapshot

| Capability | NoJect วันนี้ | Market bar | Decision |
|---|---|---|---|
| L7 WAF + auth + reverse proxy | แข็งแรง | คู่แข่งส่วนใหญ่ไม่ครอบคลุม | **Keep & amplify** |
| Hash-chained audit | มีและ verify ได้ | ไม่ใช่ core ของคู่แข่ง | **Keep & amplify** |
| Prompt injection/jailbreak/PII/canary | มี heuristic + optional judge | Market มีหลาย backend/scanner | **Keep, benchmark deeper** |
| Unicode/NFKC/confusables | มี | coverage ไม่สม่ำเสมอในตลาด | **Keep & test cross-language** |
| Structured output | ไม่มี | Pydantic/JSON Schema เป็น baseline | **P0** |
| Resolution/on-fail actions | มีลักษณะ allow/block/sanitize แต่ contract ยังไม่เป็นระบบ | Market มี fix/filter/refrain/reask/custom | **P0** |
| Re-ask/correction | ไม่มี | เป็น developer workflow สำคัญ | **P0** |
| Validator ecosystem | detector แบบ hard-coded | registry/plugin model เป็น adoption engine | **P0 core; P1 ecosystem growth** |
| Streaming validation | response ถูก buffer; proto เป็น unary draft | modern LLM apps ต้อง SSE/async streaming | **P0 beta** |
| Content moderation | ไม่มี dedicated module | toxicity/bias/NSFW เป็น baseline | **P1 ผ่าน validator contract** |
| Factuality/groundedness | ไม่มี | สำคัญต่อ RAG แต่มี cost/latency สูง | **P1 experiment, GA ภายหลัง** |
| Topical/dialog rails | ไม่มี | NeMo เด่นด้านนี้ | **P1 หลัง core policy engine** |
| LangChain/LlamaIndex | ไม่มี | ลด friction ใน adoption | **P0 quick win** |
| PII round-trip vault | masking อย่างเดียว | LLM Guard มี anonymize/deanonymize | **P1** |
| Code/SQL/OpenAPI validators | WAF ไม่เท่ากับ output correctness | มีใน validator/scanner ecosystems | **P1 starter validators** |
| Helm/Envoy WASM | ไม่มี | enterprise deployment differentiator | **P2** |
| Dynamic rule sync | ไม่มี | differentiator แต่ supply-chain risk สูง | **P2** |
| React SOC SPA | embedded dashboard | polish/operations value | **P2** |

> คะแนนเดิม `ชนะ 8 · เสมอ 4 · แพ้ 9 · Partial 5` ใช้เป็น directional baseline เท่านั้น เพราะ feature บางรายการซ้อนกันและให้น้ำหนักไม่เท่ากัน ตั้งแต่นี้ให้ตัดสิน release จาก acceptance criteria และ KPI ไม่ใช่ผลรวมช่องใน matrix

---

## 3. Prioritization และ Dependency

ใช้ 4 ปัจจัยในการจัดลำดับ: **adoption impact, security impact, dependency leverage และ delivery risk**

| Gap | Scope | Priority | Effort | เหตุผล/Dependency |
|---|---|:---:|:---:|---|
| GAP-4 | Decision contract + on-fail actions | P0 | M | เป็นฐานให้ SchemaGuard, re-ask และ validators ทุกตัว |
| GAP-2 | SchemaGuard | P0 | M | high-demand DX และใช้ทดสอบ FILTER/FIX/REASK ได้ชัด |
| GAP-3 | Re-ask orchestration | P0 | M | ต้องอาศัย decision contract และ structured errors |
| GAP-9A | Validator API + local registry + CLI MVP | P0 | L | ecosystem foundation; แยกจากการโตเป็น 20+ community validators |
| GAP-13 | LangChain + LlamaIndex | P0 | S | distribution quick win หลัง Guard API นิ่ง |
| GAP-10 | gRPC unary + SSE safe streaming beta | P0 | L | transport และ stream safety ต้องทำหลัง decision contract |
| GAP-7 | Toxicity/bias/NSFW | P1 | M | ส่งเป็น validators บน registry ใหม่ ไม่เพิ่ม hard-coded pipeline |
| GAP-8 | JSON/URL/SQL/OpenAPI/code validators | P1 | S–M | starter catalog; parser-based ไม่ใช้ WAF semantics |
| GAP-1 | PII anonymize/deanonymize vault | P1 | M | ต้องมี tenant isolation, TTL และ key management ก่อน GA |
| GAP-5 | YAML topical rails | P1 | L | ต้อง reuse validator/policy engine ไม่สร้าง runtime อีกชุด |
| GAP-6 | Factuality/groundedness | P1 | L | ต้องมี evaluation dataset, retrieved context contract และ latency tier |
| GAP-9B | Public/community Hub ≥20 validators | P1 | XL/ongoing | community program หลัง API และ security model stable |
| GAP-12 | Signed zero-day rule sync | P2 | M | ต้องมี provenance, signature, rollback และ offline behavior |
| GAP-14 | React/Tailwind SOC SPA | P2 | M | ทำเมื่อ telemetry event schema นิ่ง |
| GAP-11 | Helm + Envoy WASM | P2 | XL | แยก Helm ก่อน WASM; WASM ไม่ควรแบก Python/LLM tier |

### Dependency graph

```text
Decision/Result contract (GAP-4)
├── SchemaGuard (GAP-2) ──┬── Re-ask (GAP-3)
│                         └── Code/SQL/OpenAPI validators (GAP-8)
├── Validator API/registry (GAP-9A)
│   ├── Moderation validators (GAP-7)
│   ├── Framework integrations (GAP-13)
│   ├── Topical rails (GAP-5)
│   └── Factuality (GAP-6)
└── Transport event contract
    ├── gRPC + SSE streaming (GAP-10)
    └── SOC event schema (GAP-14)
```

---

## 4. Architecture Contracts ที่ต้องนิ่งก่อนสร้าง Feature

### 4.1 Versioned decision model

ปัจจุบัน Python/TypeScript ใช้ `is_blocked`/`isBlocked` และ Guard Engine ใช้ `allowed`; ก่อนเพิ่ม actions ต้องรวม semantics ให้ชัด โดยคง fields เดิมไว้ตลอด v1.x เพื่อ backward compatibility

```text
GuardDecision v1
├── schema_version
├── status: PASS | FAIL | ERROR
├── action: ALLOW | BLOCK | FIX | FILTER | REFRAIN | REASK | FIX_REASK
├── original_value / resolved_value
├── risk: category, severity, score, confidence
├── findings[]: validator_id, code, path/span, reason, evidence
├── reask_request?: messages, schema, failed_paths, attempt, budget
├── trace_id / policy_id / policy_version
└── timings_ms[]
```

Rules:

- `status` บอกผล validation; `action` บอก resolution — ห้ามใช้ boolean ตัวเดียวแทนสองความหมาย
- `REFRAIN` ต้องไม่เปลี่ยน failure ให้กลายเป็น pass
- `FILTER` ใช้ได้เฉพาะ path/field ที่ระบุได้; plain text ต้อง fallback ตาม policy
- `FIX` ต้องเก็บทั้ง original hash, resolved value และ validator ที่แก้ เพื่อ audit โดยไม่ log raw PII
- unknown action/version ต้อง fail closed ที่ gateway เมื่อ route เป็น protected route
- audit chain format เดิมใน `internal/audit/logger.go` ห้ามเปลี่ยน; เพิ่มข้อมูลผ่าน versioned event payload เท่านั้น

### 4.2 Validator contract

Validator ทุกตัวต้องประกาศ:

```yaml
apiVersion: noject.io/validator/v1alpha1
kind: Validator
metadata:
  name: valid-json
  version: 0.1.0
  license: Apache-2.0
spec:
  stages: [output]
  runtimes: [python]
  modes: [sync, async, stream-final]
  categories: [structured-output]
  configSchema: schemas/valid-json.schema.json
  permissions:
    network: false
    filesystem: false
  resources:
    model: none
    memoryMB: 64
  integrity:
    sha256: "..."
```

Runtime API ต้องรองรับ `validate(value, metadata, context) -> ValidationResult` และห้ามให้ validator เลือก action เอง; validator ส่ง judgment ส่วน policy engine เป็นผู้ resolve action

Security requirements:

- install แบบ explicit, pin version และ verify checksum/signature
- default deny network/filesystem; manifest ต้องแจ้ง permission ที่ต้องใช้
- generate SBOM และ scan dependency ก่อน publish first-party package
- ไม่มี auto-update ใน production; update ต้องผ่าน staged rollout และ rollback ได้
- registry outage ต้องไม่ทำให้ validators ที่ติดตั้งไว้หยุดทำงาน

### 4.3 Re-ask contract

Core library ควร **สร้าง `ReaskRequest` แต่ไม่ยิง LLM แบบซ่อนค่าใช้จ่าย**; SDK integration หรือ gateway adapter เป็นผู้ execute เมื่อเปิดใช้ชัดเจน

Budget controls ขั้นต่ำ:

- `max_attempts` ค่าเริ่มต้น 1, สูงสุดที่ config อนุญาต
- `max_total_tokens`, `max_total_cost`, `deadline_ms`
- retry เฉพาะ failure ที่แก้ได้ เช่น schema/field validation; ห้าม re-ask prompt injection ระดับ HIGH/CRITICAL
- preserve fields ที่ผ่านแล้ว และ re-ask เฉพาะ failed paths เมื่อ provider รองรับ structured output
- detect repeated identical output เพื่อหยุด loop
- บันทึก attempt, reason และ cost metadata ใน audit โดยไม่บันทึก secret/PII ดิบ

### 4.4 Safe-streaming contract

Streaming มี 3 policy modes:

| Mode | Behavior | Use case |
|---|---|---|
| `buffer_until_validated` | buffer เป็น sentence/record แล้ว validate ก่อนปล่อย | ค่าเริ่มต้นสำหรับ protected routes |
| `terminate_on_failure` | ส่งเฉพาะ chunk ที่ผ่านแล้วและตัด stream เมื่อพบภัย | low-risk text ที่ยอมรับ partial response ได้ |
| `final_only` | stream ภายใน upstream แต่ buffer ทั้ง response ก่อน client | structured JSON หรือ validator ที่ incremental ไม่ได้ |

ข้อกำหนด:

- Canary/secret scan ใช้ rolling window ยาวอย่างน้อย `max_secret_length - 1`
- UTF-8 boundary, escaped JSON, base64 fragments และ SSE event boundary ต้องมี test vectors
- ห้ามส่ง invalid bytes, half-redacted entity หรือ secret prefix ที่ย้อนกลับไม่ได้
- client cancellation ต้อง cancel upstream และ detector work
- HTTP/JSON transport ยังอยู่เป็น fallback ตลอด Beta; gRPC rollout ด้วย feature flag/canary

---

## 5. Work Packages และ Definition of Done

### GAP-4 · Decision Contract & On-Fail Actions — P0

**Deliverables**

- เพิ่ม versioned `GuardDecision`, `Finding`, `Action`, `Resolution` ใน Python/TS/Guard Engine และ transport schema
- policy mapping ต่อ route/validator พร้อม secure defaults
- handlers สำหรับ `FIX`, `FILTER`, `REFRAIN`, `REASK`, `FIX_REASK` และ custom callback เฉพาะ in-process SDK
- migration adapter ให้ `is_blocked`/`allowed` เดิมยังทำงานใน v1.x

**Primary areas:** `packages/noject-python/noject/guard.py`, `packages/noject-ts/src/types.ts`, `packages/noject-ts/src/guard.ts`, `guard-engine/server.py`, `internal/router/`, `internal/config/`, `proto/guard.proto`

**Done when**

- contract tests ให้ผล semantic เดียวกันทั้ง Python/TS/HTTP/gRPC
- unknown action, invalid fix และ validator error มี fail-open/fail-closed behavior ตาม route config
- audit verification เดิมยังผ่าน และไม่มี raw PII เพิ่มใน log

### GAP-2 · SchemaGuard — P0

**Deliverables**

- Python: Pydantic v2 + JSON Schema; TypeScript: JSON Schema ก่อน แล้วค่อยเพิ่ม adapter ตาม ecosystem ที่เลือก
- parse fenced JSON, report JSON Pointer paths, distinguish parse/schema/validator failures
- nested objects, arrays, unions, optional/default fields และ strict/coercion mode
- partial-field filter/fix/re-ask บน structured output

**Primary areas:** `packages/noject-python/noject/schema_guard.py`, `packages/noject-ts/src/schemaGuard.ts`, `packages/*/tests/`

**Done when**

- golden corpus ≥100 cases ครอบคลุม malformed JSON, nested schema, unicode และ adversarial keys
- ไม่มี code execution จาก schema/format string
- API docs มี sync/async, provider-neutral และ FastAPI/Express examples
- Pydantic เป็น optional dependency; base install ยังเบาและ `uv`/`pip` ใช้ได้

### GAP-3 · Self-correcting Re-ask — P0

**Deliverables**

- `ReaskRequest` เป็น data contract, caller-owned executor เป็น default
- adapters สำหรับ provider-neutral callable และ framework integrations
- field-level merge พร้อม conflict detection
- retry/token/cost/deadline budget และ circuit breaker

**Primary areas:** `packages/noject-python/noject/reask.py`, `packages/noject-ts/src/reask.ts`, `guard-engine/`, `internal/router/`

**Done when**

- deterministic fake-LLM tests: success, exhausted budget, timeout, repeated output และ malicious re-ask response
- false-block recovery ดีขึ้น ≥30% บนชุด benign-but-invalid ที่ versioned
- ไม่มี hidden network call เมื่อไม่ได้ส่ง executor/เปิด gateway adapter

### GAP-9A · Validator API, Registry & CLI MVP — P0

**Deliverables**

- manifest JSON Schema, validator SDK, local registry/index และ CLI commands: `list`, `inspect`, `install`, `verify`, `remove`
- first-party starter validators 10 ตัว: valid JSON, JSON Schema, URL syntax, URL allowlist, SQL parse-only, OpenAPI, token limit, regex, secrets และ toxicity baseline
- author template, conformance test kit, version compatibility policy และ security review checklist

**Primary areas:** `guard-engine/registry/`, `packages/noject-python/noject/validators/`, `packages/noject-python/noject/cli.py`, `docs/validators/`

**Done when**

- clean/offline install จาก pinned artifact ทำซ้ำได้
- tampered artifact ถูกปฏิเสธ; dependency/network permissions มองเห็นก่อน install
- third-party sample validator ผ่าน conformance suite โดยไม่แก้ core
- ยังไม่แยก public registry repo จน manifest ผ่านอย่างน้อย 10 first-party implementations

### GAP-13 · LangChain & LlamaIndex — P0

**Deliverables**

- LangChain callback/runnable middleware และ LlamaIndex input/output integration
- sync/async/error propagation และ streaming behavior ระบุชัด
- examples สำหรับ RAG, agent tool call และ PII masking

**Primary areas:** `packages/noject-python/noject/integrations/`, `packages/noject-python/tests/`, `docs/integrations/`

**Done when**

- integration tests pin supported minimum/maximum major versions
- import core package ได้โดยไม่ต้องติดตั้ง LangChain/LlamaIndex
- compatibility matrix และ upgrade policy อยู่ใน docs

### GAP-10 · gRPC + SSE Safe Streaming — P0 Beta

**Deliverables**

- ขยาย proto จาก unary draft เป็น versioned unary + bidirectional stream โดยไม่ reuse field numbers
- generated Go/Python code, health/readiness และ shared-key → mTLS roadmap
- SSE parser/encoder ที่รักษา event fields, comments, `[DONE]` และ provider error events
- rolling-window egress scan, backpressure, cancellation และ bounded buffers
- feature flags: `transport=http|grpc`, `stream_mode=buffer_until_validated|terminate_on_failure|final_only`

**Primary areas:** `proto/guard.proto`, `internal/guardclient/`, `internal/router/`, `guard-engine/`, `configs/gateway.yaml`

**Done when**

- transport contract tests เทียบ HTTP กับ gRPC ได้ผลเดียวกัน
- split-canary, split-UTF-8, split-base64, malformed SSE, slow consumer และ cancellation tests ผ่าน
- p95 unary regression เทียบ HTTP/JSON <5% ใน payload 1 KiB หรือมี documented exception พร้อมผลวัด
- streaming memory เป็น bounded; 100 concurrent streams ไม่โตตาม response แบบไม่จำกัด
- HTTP fallback ผ่าน chaos test เมื่อ gRPC unavailable ตาม configured fallback policy

### GAP-7 · Content Moderation — P1

ทำเป็น validators แบบ optional tier: toxicity, bias และ NSFW text ก่อน; Llama Guard/โมเดลอื่นเป็น pluggable backend. ต้องมี per-language model card, thresholds, calibration corpus และไม่ claim Thai coverage หากยังไม่ผ่าน test ภาษาไทย

**Done when:** per-category precision/recall/F1, latency/cost และ false-positive slices ถูก publish; base package ไม่ดึง model weights

### GAP-6 · Factuality/Groundedness — P1

เริ่มจาก **context-grounded entailment** ก่อน Wikipedia lookup: รับ `claim`, `retrieved_context`, `citations`; แยก unsupported ออกจาก contradicted. Wikipedia/provenance connector เป็น adapter ภายหลัง ไม่ใช่ source of truth อัตโนมัติ

**Done when:** versioned RAG evaluation set, citation-level evidence, abstain state และ p95 ของ heavy tier ถูกวัด; ห้ามใช้ factuality verdict เป็น security block โดยไม่มี policy opt-in

### GAP-5 · YAML Topical Rails — P1

ใช้ YAML policy เดิมและ validator API; รองรับ allowed/blocked intents, examples, threshold, fallback action และ policy version. ไม่สร้าง Colang clone หรือ general-purpose dialog language

**Done when:** deterministic config validation, ambiguous/unknown intent behavior, multilingual corpus และ hot-reload rollback tests ผ่าน

### GAP-1 · PII Round-trip Vault — P1

รองรับ in-memory สำหรับ dev และ Redis-compatible backend สำหรับ production; token ต้อง opaque, tenant-scoped, TTL-bound และห้ามเดาได้. Deanonymization ทำเฉพาะ response/tenant/trace ที่ตรงกัน

**Done when:** cross-tenant access, replay, expiry, partial placeholder, concurrent request และ key-rotation tests ผ่าน; log/metrics ไม่มี PII ดิบ

### GAP-8 · Code/SQL/OpenAPI Validators — P1

แยก **security WAF** ออกจาก **output correctness validator** ให้ชัด: SQL validator ตรวจ parse/dialect/read-only policy ไม่อ้างว่าแทน database authorization; OpenAPI ใช้ schema parser; URL แยก syntax, scheme, DNS/IP policy และ SSRF-safe resolution

### GAP-11/12/14 · Platform Polish — P2

- **Helm:** chart, network policies, PodDisruptionBudget, HPA, secret references, probes และ upgrade/rollback ก่อน
- **Envoy WASM:** จำกัดเฉพาะ deterministic edge checks; ส่ง semantic inspection ไป guard service
- **Rule sync:** signed bundle, provenance, staged activation, dry-run, expiry, rollback และ last-known-good
- **SOC SPA:** สร้างหลัง telemetry schema stable; RBAC, redaction, live events, filter/export และ CSP เป็น release gates

---

## 6. 12-Week Execution Plan — 1 FTE

### Release train

| สัปดาห์ | Milestone | งานหลัก | Exit gate |
|---:|---|---|---|
| 1 | **M0 Baseline locked** | freeze public API inventory, benchmark dataset/version, ADR สำหรับ decision model/validator/re-ask/streaming; define deprecation policy | baseline `make test`, `make bench`, `make all`; risk register และ ADR approved |
| 2 | **v1.1 contract alpha** | implement versioned decision/finding/action types + compatibility adapters ใน Python/TS/HTTP | cross-language golden contract tests; audit verify ผ่าน |
| 3 | **v1.1 SchemaGuard alpha** | Pydantic v2/JSON Schema, error paths, strict/coercion, structured FILTER/FIX | ≥100 schema cases; package extras และ docs ผ่าน |
| 4 | **v1.2 resolution beta** | complete action resolver, route policy config, secure fallback semantics | policy matrix tests ทุก action × failure mode |
| 5 | **v1.2 re-ask beta** | `ReaskRequest`, executor adapters, budgets, field merge, fake-provider tests | ≥30% recovery target บน versioned corpus; no hidden calls |
| 6 | **v1.3 registry alpha** | manifest schema, local registry, CLI, integrity verification, conformance kit | install/verify/remove offline + tamper tests ผ่าน |
| 7 | **v1.3 ecosystem beta** | 10 first-party validators + LangChain/LlamaIndex integrations | integration matrix, minimal installs, examples และ security review |
| 8 | **v1.4 transport alpha** | protobuf versioning, unary gRPC, HTTP fallback, health/readiness | HTTP↔gRPC parity + p95 gate |
| 9 | **v1.4 streaming alpha** | SSE parser, bounded buffering, cancellation/backpressure | malformed/slow/cancel/provider fixture tests ผ่าน |
| 10 | **v1.4 streaming beta** | rolling-window canary, incremental policies, concurrency/chaos tests | split-secret/UTF-8/base64 tests + memory ceiling ผ่าน |
| 11 | **RC hardening** | security review, fuzz/property tests, migration guide, docs, examples, packaging | full test/build/bench; zero known Critical/High in new paths |
| 12 | **Core Parity Beta launch** | release notes, compatibility matrix, signed artifacts/SBOM, benchmark report, feedback channels | release checklist signed off; rollback artifacts พร้อม |

### Scope cut order เมื่อเวลาไม่พอ

ตัดตามลำดับนี้โดยไม่ลด safety gates:

1. ลด starter validators จาก 10 เหลือ 8
2. เลื่อน LlamaIndex advanced agent/streaming example แต่คง basic integration
3. ปล่อย gRPC unary เป็น Beta และ SSE เป็น Experimental
4. เลื่อน TypeScript SchemaGuard adapter ขั้นสูง แต่คง JSON Schema contract
5. **ห้ามตัด:** compatibility tests, bounded buffers, split-secret tests, audit verification, artifact integrity และ secure fallback

---

## 7. Post-Beta Roadmap

### เดือน 4–6: Breadth & Trust

- GAP-7 moderation GA พร้อม Thai/English evaluation slices
- GAP-8 code/SQL/OpenAPI validator set
- GAP-1 tenant-safe vault beta
- GAP-5 topical rails beta
- public validator catalog และ community contribution workflow
- เป้าหมาย 20+ validators โดยนับเฉพาะตัวที่ผ่าน conformance/security checklist

### เดือน 7–9: Enterprise & RAG

- GAP-6 groundedness beta → GA ตาม benchmark
- Helm chart GA, mTLS ระหว่าง gateway↔guard, Kubernetes deployment guide
- signed rule bundle format และ offline last-known-good
- telemetry event schema v1 สำหรับ SOC UI

### เดือน 10–12: Edge & Operations

- Envoy WASM deterministic filter prototype
- React/Tailwind SOC SPA บน stable event schema
- staged dynamic rule sync พร้อม dry-run/rollback
- ecosystem governance, maintainer policy, vulnerability response SLA

---

## 8. Workstreams และ Ownership

ถ้ามีหลายคน ให้แบ่งขนานโดยรักษา contract owner คนเดียว:

| Workstream | Scope | Owner profile | Depends on |
|---|---|---|---|
| A — Core Contracts | decisions, actions, schema, re-ask | Python/TS API maintainer | none; เป็น source of truth |
| B — Gateway Transport | Go config/router, gRPC, SSE, backpressure | Go/network engineer | decision/event contract |
| C — Ecosystem | registry, CLI, validators, integrations | Python ecosystem maintainer | validator contract |
| D — Quality & Security | corpus, fuzz, benchmarks, supply chain, docs evidence | security/test maintainer | ทำต่อเนื่องทุกสัปดาห์ |

RACI แบบย่อ:

- Contract changes: A accountable; B/C consulted; D approves release evidence
- Transport/security fallback: B accountable; D reviewer
- New validator publish: C accountable; D security gate
- Benchmark/KPI change: D accountable; maintainer lead approves

---

## 9. Quality, Security และ Release Gates

ทุก milestone ต้องผ่าน gate ที่เกี่ยวข้อง ไม่รอ Week 11:

### Functional

- `make test` ผ่านครบ Go + Guard Engine + Python SDK + TypeScript SDK
- feature ใหม่มี unit, integration และ negative/adversarial tests
- generated protobuf/code ถูกตรวจว่า reproducible และไม่มี uncommitted drift
- examples ใน docs ถูก smoke-test ใน CI

### Security

- `tests/security_score_test.go` ไม่เสีย measured detection/FPR baseline
- new parser/stream/validator paths มี fuzz/property tests
- dependency/SBOM scan ไม่มี known Critical/High ที่ไม่มี mitigation
- fail-open ใช้ได้เฉพาะ route ที่ operator กำหนดชัดและต้อง emit audit/metric
- secrets, raw PII, prompts และ model responses ไม่เข้า log โดย default

### Performance

- รักษา Go deterministic fast path แยกจาก network/ML tier
- รายงาน attack fast-exit และ clean full-scan แยกกัน; ห้ามใช้ benchmark path เดียวอ้างแทนทุก path
- gateway pipeline, guard HTTP/gRPC, SchemaGuard, each validator และ streaming TTFT มี histogram แยก
- benchmark ระบุ hardware, OS, dataset version, payload size, concurrency และ cold/warm state

### Compatibility

- audit hash-chain format ยัง verify ด้วย `./bin/noject-gateway -verify-audit`
- HTTP/JSON endpoint เดิมและ `is_blocked`/`allowed` fields เดิมยังใช้ได้ใน v1.x
- breaking change ต้องมี deprecation อย่างน้อยหนึ่ง minor release + migration guide
- Python รองรับ `uv` และ `pip/build`; TypeScript strict mode และ declarations ยังผ่าน

### Documentation

- API reference, config schema, threat model, model card/eval card และ operational rollback guide
- ทุก claim ด้าน latency/accuracy เชื่อมถึง reproducible benchmark artifact
- ใช้ถ้อยคำ ISO ที่กำหนดและมี disclaimer; ห้ามใช้คำว่า certified/compliant หากไม่มีการรับรอง

---

## 10. KPI Scorecard

ก่อนเริ่ม Week 1 ต้องบันทึก baseline และระบุ data owner; KPI ที่ไม่มี instrumentation ไม่นับว่า track ได้

| Objective | Metric | Baseline | Target | Source/Cadence |
|---|---|---:|---:|---|
| Ecosystem | validators ที่ผ่าน conformance + security review | 0 | 10 Beta; 20+ ภายใน 3 เดือนหลัง launch | registry CI / weekly |
| Structured output adoption | สัดส่วนผู้ตอบแบบสำรวจหรือ opt-in users ที่ใช้ SchemaGuard | TBD W1 | ≥25% ของ measured Python SDK users ภายใน 90 วัน | opt-in telemetry + survey / monthly |
| Correction quality | recovery rate ของ benign-but-invalid corpus | TBD W1 | ดีขึ้น ≥30% เทียบ block-only baseline | versioned eval / per PR |
| Correction safety | harmful output ที่ re-ask แล้วหลุด | TBD W1 | 0 Critical; ไม่แย่กว่า baseline | adversarial eval / per PR |
| Transport | p95 unary latency regression gRPC vs HTTP ที่ 1 KiB | TBD W8 | <5% regression | benchmark CI / release |
| Streaming | added TTFT ที่ sentence-buffer mode | TBD W9 | budget กำหนดจาก workload; publish p50/p95/p99 | load test / release |
| Streaming safety | split-secret/encoding test detection | 0 suite | 100% versioned corpus | security CI / per PR |
| Moderation | macro F1 + per-language/per-category FPR | TBD post-Beta | macro F1 ≥0.90 โดยไม่มี slice ต่ำกว่าเกณฑ์ที่ประกาศ | eval card / release |
| Factuality | supported/contradicted/unknown macro F1 | TBD experiment | ≥0.85 ก่อน Beta label; GA target ตั้งหลัง pilot | RAG eval / release |
| Distribution | PyPI weekly downloads หลัง integrations | snapshot W1 | +50% ภายใน 90 วัน โดยรายงาน seasonality | PyPI stats / monthly |
| Reliability | guard-induced 5xx rate | TBD W1 | <0.1% และไม่มี unbounded-memory incident | metrics / weekly |
| Compatibility | downstream contract test pass rate | none | 100% supported matrix | CI / per PR |
| Supply chain | signed/verifiable first-party validator artifacts | 0 | 100% | release pipeline / every artifact |

### North-star metric

**Safe successful requests** = สัดส่วน request ที่ผ่าน policy หรือถูกแก้ไขสำเร็จ โดยไม่มี confirmed harmful escape และไม่เพิ่ม user-visible error

ต้องรายงานแยก 4 ค่าเสมอ: `blocked attacks`, `recovered benign failures`, `false blocks`, `harmful escapes` เพื่อไม่ให้ optimization ของตัวเลขเดียวบดบังความเสี่ยง

---

## 11. Observability ที่ต้องเพิ่มพร้อม Feature

Metrics ขั้นต่ำ:

- `noject_validator_runs_total{validator,result,action}`
- `noject_validator_duration_seconds{validator,tier}`
- `noject_reask_attempts_total{reason,outcome}` และ token/cost histogram เมื่อ provider ส่งข้อมูล
- `noject_stream_buffer_bytes`, `noject_stream_terminations_total{reason}`
- `noject_guard_transport_requests_total{transport,status}`
- `noject_policy_fallback_total{route,mode,reason}`
- `noject_registry_verification_failures_total{reason}`

Cardinality rules:

- ห้ามใช้ prompt, user ID, trace ID, raw validator reason หรือ URL เต็มเป็น metric label
- validator/policy IDs ต้อง normalized และ bounded
- detailed evidence อยู่ใน redacted audit/event store ไม่ใช่ Prometheus labels

---

## 12. Risks และ Mitigations

| Risk | Impact | Mitigation / Trigger |
|---|---|---|
| 1 FTE scope overload | feature ครึ่งเสร็จหลายตัว | enforce scope-cut order; Beta labels; ห้ามเริ่ม P1 ก่อน P0 exit gates |
| Decision semantics แตกต่างข้ามภาษา | bypass/behavior ไม่ตรง | golden contract corpus shared ระหว่าง Python/TS/Go |
| Re-ask เพิ่ม cost/latency แบบคาดไม่ถึง | bill/timeout incident | caller-owned default, explicit budgets, circuit breaker, metrics |
| Streaming ปล่อยข้อมูลก่อนตรวจ | secret/harm leak แก้คืนไม่ได้ | buffer-by-default, rolling window, terminate tests, no unsafe zero-buffer claim |
| Hub กลายเป็น supply-chain vector | arbitrary code execution | pin/sign/verify, permissions, SBOM, manual prod promotion |
| Heavy ML ทำ fast path ช้า | เสีย differentiator | optional dependency/tier, lazy loading, route opt-in, separate benchmarks |
| Factuality overclaims certainty | false trust | entailment states + abstain + evidence; no automatic block by default |
| PII vault ข้าม tenant | privacy breach | tenant/trace binding, TTL, opaque tokens, redacted logs, adversarial tests |
| Competitor matrix ล้าสมัย | priority ผิด | quarterly review พร้อม source snapshot; stars ไม่ใช้เป็น release KPI |
| ISO wording ถูกตีความเป็น certification | legal/trust risk | ใช้ exact alignment wording + disclaimer ทุก artifact ที่เกี่ยวข้อง |

---

## 13. Anti-pattern Guard

| อย่าทำ | เหตุผล | ทางเลือก |
|---|---|---|
| สร้าง Colang-like DSL ใหม่ | parser/runtime/governance cost สูง | YAML policy บน validator contract เดิม |
| rebuild Presidio | scope ใหญ่และไม่ใช่ differentiator | optional adapter + Thai-specific recognizers/eval |
| แข่ง train model กับ Llama Guard โดยตรง | compute/data moat ไม่ใช่จุดแข็ง | pluggable local backend พร้อม model card |
| hosted validator cloud | ขัด self-hosted/privacy promise | signed local catalog/artifacts |
| content-moderation SaaS | หลุด mission | moderation validators แบบ optional tier |
| ผูก core guard กับ provider SDK | lock-in และ re-ask cost ซ่อน | provider-neutral callable/adapters |
| ให้ gateway auto-install/update plugin | supply-chain และ availability risk | explicit staged install + last-known-good |
| เรียก SQL WAF ว่า SQL correctness validator | threat model คนละแบบ | parser/dialect/read-only validator แยก |
| stream token ก่อนตรวจแล้วค่อย revoke | revoke ข้อมูลที่ส่งไปแล้วไม่ได้ | bounded pre-release buffer |
| ใช้จำนวน validators เป็น KPI เดี่ยว | กระตุ้น low-quality plugins | นับเฉพาะ conformance + security reviewed |

---

## 14. Go/No-Go Checklist สำหรับ Core Parity Beta

Beta เปิดได้เมื่อทุกข้อเป็นจริง:

- [ ] GAP-4, GAP-2, GAP-3 และ GAP-9A ผ่าน Definition of Done
- [ ] first-party validators ≥10 หรือใช้ scope-cut ที่ประกาศไว้และไม่น้อยกว่า 8
- [ ] LangChain/LlamaIndex basic integrations ผ่าน compatibility matrix
- [ ] HTTP fallback + gRPC unary ผ่าน parity/chaos tests
- [ ] SSE modes ถูก label ตาม maturity; safe default เป็น `buffer_until_validated`
- [ ] full `make test`, `make bench`, `make all` ผ่านบน release commit
- [ ] security-score baseline ไม่ regress และ new adversarial streaming corpus ผ่าน
- [ ] audit verification backward compatible
- [ ] signed release artifacts, checksum และ SBOM พร้อม
- [ ] migration, rollback, threat model, benchmark methodology และ known limitations พร้อม
- [ ] ไม่มี known Critical/High ที่ยังไม่มี accepted mitigation

ถ้าข้อใดไม่ผ่าน ให้ลด label เป็น Alpha/Experimental หรือเลื่อนเฉพาะ capability นั้น ห้ามลด release gate แบบเงียบ ๆ

---

## 15. References

- [AGENTS.md](../AGENTS.md) — mission, architecture และ golden rules
- [README.md](../README.md) — current public surface
- [BENCHMARKS.md](BENCHMARKS.md) — current performance/accuracy evidence
- [SECURITY_STANDARDS.md](SECURITY_STANDARDS.md) — MITRE/OWASP mapping
- [REDTEAM_FINDINGS.md](REDTEAM_FINDINGS.md) — security baseline
- [proto/guard.proto](../proto/guard.proto) — current unary draft
- [Guardrails AI features](https://github.com/guardrails-ai/guardrails/blob/main/guardrails-features-list.md)
- [NeMo Guardrails](https://github.com/NVIDIA-NeMo/Guardrails)
- [LLM Guard scanners](https://github.com/protectai/llm-guard)
- [Meta Llama Guard model card](https://github.com/meta-llama/PurpleLlama/blob/main/Llama-Guard/MODEL_CARD.md)
- [Azure AI Content Safety](https://learn.microsoft.com/en-us/azure/ai-services/content-safety/overview)

---

## ISO Alignment Disclaimer

NoJect is **Architected in Alignment with ISO/IEC 27001:2022 & ISO/IEC 42001:2023 Principles**. This statement describes architectural intent and control mapping only. It does not represent ISO certification, legal compliance advice, or a guarantee that deploying NoJect makes an organization compliant. Certification and compliance depend on the organization’s full management system, operating procedures, risk treatment, evidence, and independent assessment.
