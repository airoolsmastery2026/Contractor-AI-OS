# Roadmap

Roadmap dùng evidence gates, không dùng ngày giả. Không bắt đầu phase sau nếu exit criteria phase trước chưa đạt.

## Phase 0 — Foundation lock (`COMPLETE`)

Deliverables: Constitution, source-of-truth chain, product/MVP, architecture/security/pricing/AI boundaries, data model, roadmap, backlog, verification và handoff.

Exit: tài liệu nhất quán, không code/dependency, một next task duy nhất.

## Phase 1 — Pilot truth and technical decisions

- FND-001 chọn stack/ADR.
- DISC-001 thu thập và chuẩn hóa quote workflow thực tế cùng tối thiểu ba sample đại diện.
- FND-002 khóa units/currency/rounding/tax/markup policy v0.1.
- SEC-001 khóa permission matrix, threat model và data classification.

Exit: stack và business calculation inputs được chấp thuận; security gates có test strategy.

## Phase 2 — Tenant and data spine

- Repository skeleton tối thiểu, test harness, migration discipline.
- Identity/tenant context, membership/RBAC, RLS/constraints.
- Audit/outbox primitives và tenant-isolation test harness.

Exit: hai tenant test fixtures chứng minh isolation positive/negative; migrations repeatable; chưa có quote UI cần thiết.

## Phase 3 — PriceBook and deterministic quote core

- Material/product/supplier/PriceBook/rule versions.
- Scope confirmation model.
- Pure calculation engine, trace, revision và approval prerequisites.

Exit: sample quotes tái tạo chính xác, decimal/rounding/unit/error cases pass; approved revision immutable.

## Phase 4 — Intake, AI and proposal

- Lead/customer/project intake và documents.
- Provider abstraction, extraction schema, missing-data workflow.
- Proposal render, approval và idempotent send.

Exit: AI failure/fallback không corrupt truth; proposal không gửi trước approval; provenance/audit đầy đủ.

## Phase 5 — Quote-to-Close vertical slice

- Mini CRM stages, activities và follow-up suggestions.
- End-to-end workflow, permissions, observability và operational runbooks.

Exit: Definition of Done trong `PRODUCT.md` pass trên staging với multi-tenant fixtures.

## Phase 6 — Customer-zero pilot and hardening

- Chạy shadow/pilot với dữ liệu được phép.
- Đo baseline, rework, latency/cost và failure modes.
- Security/privacy review, backup/restore drill và incident handling.

Exit: owner chấp thuận go/no-go dựa trên evidence; backlog sau MVP được reprioritize từ dữ liệu thật.

## Expansion rule

Chỉ mở rộng ngành/module sau khi luồng Quote-to-Close có usage lặp lại, chất lượng chấp nhận được và evidence cho nhu cầu mới. Không đưa non-goals vào MVP bằng cách đổi tên chúng.
