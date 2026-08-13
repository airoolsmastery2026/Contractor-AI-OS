# Verification and Gates

## Foundation bootstrap gate

Commit bootstrap pass khi tất cả điều sau đúng:

- Required root files: `README.md`, `AGENTS.md`, `CONSTITUTION.md`, `START.md`, `CONTEXT.md`, `CURRENT_STATE.md`, `NEXT_TASK.md`, `HANDOFF.md`.
- Required docs: `PRODUCT.md`, `ARCHITECTURE.md`, `DATA_MODEL.md`, `ROADMAP.md`, `BACKLOG.md`, `VERIFICATION.md`.
- Mọi relative Markdown link trỏ tới file tồn tại.
- Chỉ một task có status `READY` và khớp `NEXT_TASK.md`.
- Constitution, Product, Architecture và Backlog cùng khóa modular monolith, tenant isolation, deterministic quote, AI boundary và human approval.
- Không có package manifest, lockfile, source code, migration, secret hoặc generated binary.
- Không có placeholder quyết định được trình bày như sự thật; open decisions nằm trong `CONTEXT.md`/ticket.
- `HANDOFF.md` ghi kết quả thực tế và commit/ref sau publish.

## Traceability matrix

| Requirement | Source | Planned evidence |
|---|---|---|
| MVP Quote-to-Close | `PRODUCT.md` | E2E-001 workflow acceptance |
| Modular monolith | `CONSTITUTION.md`, `ARCHITECTURE.md` | boundary tests + dependency checks |
| Multi-tenant isolation | `CONSTITUTION.md`, `ARCHITECTURE.md`, `DATA_MODEL.md` | SEC-002 positive/negative tests |
| Deterministic calculation | `CONSTITUTION.md`, `ARCHITECTURE.md` | FND-002 vectors + QUOTE-001 unit/property tests |
| AI provider abstraction | `CONSTITUTION.md`, `ARCHITECTURE.md` | AI-001 contract tests/test double |
| Human approval | `PRODUCT.md`, `ARCHITECTURE.md` | PROP-001/SEND-001 unauthorized/unapproved failures |
| Auditability | `CONSTITUTION.md`, `DATA_MODEL.md` | append-only, actor/revision/correlation integration tests |

## Required verification by risk

### Tenant/security changes

- same-tenant allowed case;
- other-tenant read/write/list/search/reference denial;
- missing/forged tenant context denial;
- role/capability denial;
- cache/storage/job/export isolation;
- privileged system action audit/redaction.

### Quote/pricing changes

- exact expected vectors using decimal;
- rounding boundary and high-precision quantity cases;
- zero/negative/range/overflow validation;
- unit mismatch and missing/stale price blocks;
- same snapshots reproduce identical trace/total;
- approval immutability and revision behavior;
- property tests for invariants such as non-negative total and monotonic cost when no discount/rate change.

### AI/document changes

- valid structured result;
- missing/unknown/conflicting fields;
- malformed output and schema mismatch;
- timeout, rate limit, provider error and allowed fallback;
- prompt/model version recorded without unsafe payload logging;
- malicious document/prompt content cannot bypass authorization, pricing or approval.

### Proposal/send changes

- only approved exact revision can send;
- expired/superseded/changed revision denied;
- retry/idempotency prevents duplicates;
- recipient, checksum, actor, provider reference and outcome audited.

## MVP release gate

Release requires Product DoD, migrations from clean database, tenant negative suite, calculation vectors, provider failure suite, approval/send suite, backup/restore evidence, secrets/privacy check, observability alerts and staging end-to-end pass. Pilot production use additionally requires owner go/no-go.

## Evidence format

Ghi ngắn gọn trong handoff hoặc ticket:

```text
check: <name>
scope: <commit/ref/environment>
result: PASS | FAIL | NOT_RUN
evidence: <command/test/report/link>
notes: <limitations or follow-up>
```

`NOT_RUN` không được diễn đạt thành pass. Mock/unit evidence không thay thế integration/security evidence khi gate yêu cầu lớp đó.
