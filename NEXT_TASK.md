# NEXT_TASK

## FND-001 — Review và quyết định ADR-0001

Status: `AWAITING_OWNER_APPROVAL`
Owner: `PROJECT_OWNER`
Priority: `P0`

## Outcome

Một quyết định tường minh: chấp thuận hoặc từ chối implementation stack được đề xuất trong `docs/adr/0001-implementation-stack.md`.

## Proposed stack

`Node.js 24 LTS + TypeScript 6 + Next.js 16 App Router (Node runtime) + PostgreSQL 18 + Prisma ORM/Migrate + decimal.js + Zod + node:test`

## Review questions

- Một ngôn ngữ TypeScript cho UI/server có phù hợp hướng vận hành dự án không?
- Có chấp nhận Next.js chỉ là delivery layer và cấm direct Prisma access trong `src/app` không?
- Có chấp nhận non-owner application DB role, forced RLS và tenant context transaction-local không?
- Có chấp nhận domain Decimal + JSON decimal strings thay cho JavaScript number không?
- Có chấp nhận defer auth, AI, storage, queue, hosting và E2E dependencies sang ticket có nhu cầu không?

## Decision syntax

- `APPROVE ADR-0001`
- `REJECT ADR-0001: <lý do hoặc thay đổi mong muốn>`

## Until decided

- Không cài package.
- Không scaffold application.
- Không viết feature, migration hoặc provider integration.
- Không chuyển `BOOT-001` hoặc ticket phụ thuộc sang `READY`.

## Acceptance criteria

- Owner decision được ghi tường minh.
- Nếu approve: ADR đổi thành `ACCEPTED`, FND-001 thành `DONE` và đúng một next ticket được chọn.
- Nếu reject: ADR/FND-001 giữ mở, feedback được ghi vào decision context.
- `CURRENT_STATE.md`, `docs/BACKLOG.md` và `HANDOFF.md` được đồng bộ.

## Required reading

- `CONSTITUTION.md`
- `docs/adr/0001-implementation-stack.md`
- `docs/ARCHITECTURE.md`
- `docs/VERIFICATION.md`
