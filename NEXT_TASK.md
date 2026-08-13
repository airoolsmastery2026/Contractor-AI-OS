# NEXT_TASK

## FND-001 — Chọn implementation stack và ghi ADR đầu tiên

Status: `READY`
Owner: `UNASSIGNED`
Priority: `P0`

## Outcome

Một ADR được chủ dự án chấp thuận, lựa chọn bộ stack nhỏ nhất có thể hiện thực modular monolith, PostgreSQL multi-tenancy, decimal pricing, background work, provider abstraction và automated tests mà không khóa domain vào vendor.

## In scope

- So sánh tối đa ba lựa chọn thực tế.
- Quyết định runtime/language, web framework, persistence/migration layer và test runner.
- Xác định cách triển khai transaction, decimal, tenant context và database RLS.
- Nêu dependency tối thiểu dự kiến, security/licensing impact và phương án thay thế.
- Tạo một ADR; cập nhật source-of-truth nếu quyết định làm thay đổi assumption.

## Out of scope

- Không cài package.
- Không scaffold app.
- Không viết feature, migration hoặc integration.
- Không chọn AI model chỉ vì quen thuộc; provider selection có ticket riêng.

## Acceptance criteria

- ADR có context, options, decision, consequences, rejected alternatives và review trigger.
- Quyết định thỏa tất cả điều khoản trong Constitution.
- Có phương án test tenant isolation và deterministic quote engine.
- Danh sách dependency ban đầu nhỏ, có lý do cho từng mục.
- `CURRENT_STATE.md`, `docs/BACKLOG.md` và `HANDOFF.md` được đồng bộ.

## Required reading

- `CONSTITUTION.md`
- `docs/ARCHITECTURE.md`
- `docs/DATA_MODEL.md`
- `docs/VERIFICATION.md`
