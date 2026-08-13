# Backlog and Tickets

Status values: `DONE`, `READY`, `BLOCKED`, `PENDING`. Chỉ ticket được chọn trong `NEXT_TASK.md` là công việc mặc định được phép bắt đầu.

| ID | Priority | Status | Outcome | Depends on |
|---|---:|---|---|---|
| GOV-001 | P0 | DONE | Bootstrap source-of-truth và foundation v0.1 | — |
| FND-001 | P0 | READY | Chọn implementation stack và ADR | GOV-001 |
| DISC-001 | P0 | PENDING | Xác nhận workflow bằng ≥3 sample quote được phép sử dụng | GOV-001 |
| FND-002 | P0 | BLOCKED | Khóa units/currency/rounding/tax/markup policy v0.1 | DISC-001 |
| SEC-001 | P0 | BLOCKED | Permission matrix, threat model, data classification | FND-001, DISC-001 |
| BOOT-001 | P0 | BLOCKED | Minimal modular-monolith skeleton + test harness, không feature | FND-001 |
| DATA-001 | P0 | BLOCKED | Tenant/data/audit schema + repeatable migrations | SEC-001, BOOT-001 |
| SEC-002 | P0 | BLOCKED | Tenant context, RBAC, DB isolation và negative-test harness | DATA-001 |
| PRICE-001 | P0 | BLOCKED | Versioned PriceBook và pricing rules | FND-002, SEC-002 |
| QUOTE-001 | P0 | BLOCKED | Pure deterministic quote calculator + test vectors | FND-002, BOOT-001 |
| QUOTE-002 | P0 | BLOCKED | Quote revision, calculation trace và approval readiness | PRICE-001, QUOTE-001 |
| AI-001 | P1 | BLOCKED | AIProvider contract, schema validation và test double | BOOT-001, SEC-001 |
| INTAKE-001 | P1 | BLOCKED | Tenant-safe lead/customer/project/document intake | SEC-002 |
| SCOPE-001 | P1 | BLOCKED | Extraction draft → missing fields → confirmed scope | AI-001, INTAKE-001 |
| PROP-001 | P1 | BLOCKED | Proposal revision + human approval | QUOTE-002 |
| SEND-001 | P1 | BLOCKED | Idempotent approved-proposal send + audit | PROP-001 |
| CRM-001 | P1 | BLOCKED | Mini CRM stages, activities và guarded transitions | INTAKE-001, SEND-001 |
| E2E-001 | P0 | BLOCKED | Quote-to-Close multi-tenant end-to-end acceptance | CRM-001, SCOPE-001 |
| PILOT-001 | P0 | BLOCKED | Customer-zero shadow pilot, measurements và go/no-go | E2E-001 |

## Ticket acceptance notes

### GOV-001 — Foundation bootstrap

Acceptance: các source-of-truth file tồn tại, liên kết hợp lệ, decisions không xung đột, không package/code, handoff có bằng chứng. Đây là ticket duy nhất được hoàn thành trong bootstrap.

### FND-001 — Stack ADR

Acceptance: theo `NEXT_TASK.md`; không cài dependency hoặc scaffold.

### DISC-001 — Pilot workflow truth

Acceptance: sample đã de-identify/được phép; input, missing-data questions, price sources, units, labor/accessory/waste/transport/overhead/markup/tax, approvals và final outcome được mô tả; khác biệt giữa samples được ghi thành rule candidate, không hard-code.

### FND-002 — Calculation policy

Acceptance: currency minor units, rounding boundaries, unit conversions, tax base, discount order, markup semantics, price expiry và error/blocking conditions được owner duyệt; test vectors expected-value được ký xác nhận.

### SEC-001/SEC-002 — Security spine

Acceptance: asset/data-flow/threats, least-privilege capabilities, cross-tenant/support behavior, RLS/constraint plan, audit/redaction và positive/negative automated tests; thiếu tenant context fail closed.

### PRICE-001/QUOTE-001/QUOTE-002 — Financial truth

Acceptance: price/rule provenance/version/effective dates, decimal math, calculation trace, reproducibility, immutable approved revision, stale/missing data blocks và sample quote parity.

### AI-001/SCOPE-001 — AI boundary

Acceptance: provider SDK chỉ trong adapter; schema/version/provenance/confidence, timeout/failure/test double; hallucinated/missing/unit-conflict cases không ghi business truth hoặc tự tiếp tục tính giá.

### PROP-001/SEND-001 — Human control

Acceptance: đúng revision được duyệt; commercial change invalidates approval; unauthorized/unapproved send fails; retries không gửi trùng; audit có actor/revision/checksum/provider reference.

### E2E-001/PILOT-001 — Release evidence

Acceptance: toàn bộ Product DoD, tenant negative cases và operational failure paths pass; pilot metrics được đo, issue được phân loại và owner đưa quyết định go/no-go.

## Backlog policy

- Không đổi `BLOCKED` thành `READY` nếu dependency chưa có evidence.
- Ticket mới phải map tới MVP outcome hoặc được đưa vào post-MVP parking lot.
- Không gộp security/financial verification vào “test later”.
- Khi hoàn thành ticket, cập nhật status, evidence link, state, next task và handoff cùng commit.

## Post-MVP parking lot

Accounting, payroll, advanced inventory, procurement marketplace, BIM/CAD, complex scheduling, native mobile, multi-region enterprise features và microservices. Không estimate hoặc triển khai các mục này trước Expansion rule trong roadmap.
