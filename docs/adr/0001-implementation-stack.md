# ADR-0001 — Implementation Stack

Status: `ACCEPTED`
Date: `2026-08-13`
Accepted: `2026-08-13`
Decision owner: Project owner
Ticket: `FND-001`

## Context

Contractor AI OS cần một stack đủ nhỏ cho đội ngũ ban đầu nhưng vẫn bảo vệ các bất biến đã khóa:

- một modular monolith, không microservices;
- PostgreSQL multi-tenancy với RLS defense-in-depth;
- deterministic quote engine dùng decimal và calculation trace;
- AI/provider adapters không xâm nhập domain;
- web workflow giàu tương tác cho intake, quote, approval và CRM;
- background work, audit, automated tests và deployment không khóa vendor.

Stack chưa được phép kéo business logic vào framework/ORM hoặc tạo dependency trước khi ADR được chấp thuận.

## Options considered

| Option | Strengths | Costs / risks |
|---|---|---|
| Node.js + TypeScript + Next.js + PostgreSQL + Prisma | Một ngôn ngữ cho UI/server; delivery nhanh; App Router và Node deployment linh hoạt; ecosystem AI/document mạnh; Prisma có typed client, Decimal và SQL migrations tùy chỉnh | JavaScript number không an toàn cho tiền; Next/ORM coupling nếu boundary yếu; RLS cần custom SQL và transaction discipline |
| Python + Django LTS + PostgreSQL | Decimal native; auth/admin/ORM/migrations tích hợp; Python AI ecosystem mạnh; LTS dài | UI tương tác cần thêm frontend conventions/dependencies; chia sẻ types UI/server yếu hơn; RLS vẫn cần custom migration/context wrapper |
| .NET LTS + ASP.NET Core + EF Core + PostgreSQL | Decimal/type system mạnh; background services và enterprise tooling tốt; LTS rõ | Chi phí phát triển/UI và độ chuyên môn vận hành cao hơn cho giai đoạn solo/small-team; AI/web product iteration kém trực tiếp hơn phương án TypeScript |

## Decision

Chọn một single-package full-stack TypeScript modular monolith:

| Concern | Choice |
|---|---|
| Runtime | Node.js `24.x` LTS; không dùng Current release cho production |
| Language | TypeScript `6.x`, strict; chỉ dùng stable version tương thích đồng thời với framework/ORM |
| Web framework | Next.js `16.x` App Router, Node.js runtime |
| Database | PostgreSQL `18.x`; chấp nhận managed PostgreSQL tương thích nếu giữ đủ RLS/backup/migration capabilities |
| Persistence | Prisma ORM + Prisma Migrate, latest stable major validated at bootstrap |
| Exact arithmetic | `decimal.js` trong domain; PostgreSQL `numeric(p,s)` trong persistence |
| Boundary validation | Zod tại delivery/provider boundaries; domain invariants vẫn là domain code |
| Unit/application tests | Stable built-in `node:test` + `node:assert/strict` |
| Integration tests | PostgreSQL thật, migration từ clean database, tenant positive/negative suite |
| E2E tests | Playwright chỉ được thêm khi có user flow cần kiểm chứng |
| Package manager | npm đi cùng Node.js; một `package-lock.json` |

Exact package versions chỉ được pin trong `BOOT-001` sau compatibility smoke check; không dùng prerelease/canary/RC.

## Application boundaries

Accepted topology:

```text
src/
  app/                    # Next.js delivery only
  modules/
    identity-tenancy/
    crm/
    documents-intake/
    catalog-pricebook/
    quoting/
    proposals-approval/
    ai-orchestration/
    audit/
  platform/               # concrete adapters and composition root
```

Mỗi module chứa `domain`, `application` và adapters cần thiết của chính nó. Không tạo monorepo hoặc package nội bộ trước khi có nhu cầu chứng minh được.

Rules:

- Server Components gọi application queries; không import Prisma hoặc query database trực tiếp.
- Server Actions chỉ là authenticated/validated UI mutation adapters và gọi application commands.
- Route Handlers chỉ dành cho webhook, external API, file endpoint hoặc HTTP contract thực sự.
- Prisma Client chỉ xuất hiện trong infrastructure repositories và transaction boundary.
- Domain/application không import `next/*`, React, Prisma, AI SDK, storage SDK hoặc message provider.
- Node runtime là mặc định. Không dùng Edge runtime cho database, pricing, document hoặc AI workflows.
- Business transactions không dựa vào Next.js cache. Tenant-owned data không được cache nếu cache key/tag chưa tenant-scoped và test được.

## Multi-tenant database contract

Prisma không phải security boundary. PostgreSQL và application cùng thực thi isolation:

1. Migration role sở hữu schema; application role không phải owner, không superuser và không có `BYPASSRLS`.
2. Tenant-owned tables dùng composite tenant constraints, `ENABLE ROW LEVEL SECURITY` và `FORCE ROW LEVEL SECURITY`.
3. Mọi tenant use case chạy trong một short-lived database transaction.
4. Transaction mở đầu bằng parameterized `set_config('app.tenant_id', tenant_id, true)` và đặt actor/correlation context khi cần.
5. Policy đọc `current_setting('app.tenant_id', true)` và fail closed nếu thiếu/không hợp lệ.
6. Repository vẫn truyền tenant predicate tường minh; RLS là defense-in-depth, không thay authorization.
7. Connection pooling chỉ được chấp nhận khi test chứng minh transaction-local context không rò sang request khác.
8. Migration SQL chứa RLS/policies/roles được review và version-control; không dùng `db push` ngoài disposable local experiments.

Mọi access pattern tenant-owned phải có negative integration test bằng application database role.

## Deterministic pricing contract

- Domain dùng `decimal.js` trực tiếp để không phụ thuộc `Prisma.Decimal`.
- Không tạo decimal từ JavaScript `number` nếu giá trị bắt nguồn từ user/database/API; dùng canonical decimal string.
- DTO/JSON truyền money, quantity và rate dưới dạng string có schema; currency/unit là field riêng.
- Database dùng `numeric(p,s)` được chọn rõ theo field; không dùng PostgreSQL `money`, `real` hoặc `double precision` cho calculation truth.
- Rounding chỉ chạy tại các boundary đã định trong pricing policy version; database coercion không được dùng thay business rounding.
- Prisma mapping phải được test round-trip với high precision, negative cases và snapshot reproduction.

## Initial dependency budget

`BOOT-001` chỉ được đề xuất các direct dependencies sau, mỗi mục vẫn cần exact-version/security review:

| Dependency group | Why now |
|---|---|
| `next`, `react`, `react-dom` | Delivery/UI runtime |
| `@prisma/client`, stable PostgreSQL adapter/driver | Typed persistence adapter |
| `decimal.js` | Exact domain arithmetic độc lập ORM |
| `zod` | Runtime validation cho untrusted boundaries |
| `typescript`, `prisma` | Type checking và reviewed SQL migrations |
| Type declarations cần thiết | Compile-time support |
| ESLint + Next config | Static quality gate cho framework boundary |

Không thêm AI SDK, auth provider, queue, storage SDK, component library, state manager, telemetry vendor hoặc Playwright trong skeleton nếu ticket hiện tại chưa cần.

Node built-in test runner được dùng trước để tránh thêm test framework. Khi React component testing thực sự cần, ticket phải so sánh chi phí với E2E; async Server Components ưu tiên E2E.

## Security and licensing impact

- Chỉ dùng stable supported lines và lock exact versions trong `package-lock.json`.
- Chạy dependency vulnerability và license checks trong BOOT-001/CI trước merge.
- Các thành phần đề xuất dùng permissive open-source licenses (MIT, Apache-2.0 hoặc PostgreSQL License); exact package/transitive inventory phải được lưu bằng evidence khi cài.
- Không dùng Prisma telemetry/logging để ghi query parameters chứa tenant/PII; logging phải redacted theo Constitution.
- Secrets chỉ tồn tại server-side; không dùng prefix public cho database/provider credentials.

## Consequences

Positive:

- Một ngôn ngữ và một deployable giúp iteration nhanh, không tạo API round-trip nội bộ không cần thiết.
- Node runtime giữ khả năng database/crypto/npm đầy đủ và có thể chạy như Node server hoặc container.
- PostgreSQL cung cấp exact numeric, transactions, constraints và RLS.
- Delivery framework, ORM và providers vẫn nằm ngoài domain.

Negative:

- Team phải chủ động ngăn direct-database access từ `app/` và framework-driven domain design.
- RLS với pooled connections là high-risk path, bắt buộc transaction wrapper và integration tests.
- Exact arithmetic cần explicit string/Decimal discipline vì JavaScript mặc định dùng binary floating point.
- Background worker build/deployment vẫn là quyết định deferred; không được giả định Next request lifecycle là durable queue.

## Rejected alternatives

### Django LTS

Không bị loại vì thiếu năng lực. Django phù hợp nếu đội ngũ Python-first hoặc pilot ưu tiên admin/server-rendered workflow. Hiện tại lợi ích một ngôn ngữ UI/server, typed contracts và tốc độ product iteration của TypeScript cao hơn. Revisit nếu document/ML processing trở thành phần lớn hệ thống hoặc frontend scope giảm mạnh.

### .NET LTS

Không bị loại vì technical quality. Revisit nếu team trở thành .NET-first, có enterprise integration yêu cầu hệ sinh thái Microsoft hoặc background/transaction workload vượt xa web product workload.

### Drizzle ORM

Drizzle cho phép kiểm soát SQL/RLS trực tiếp hơn, nhưng API/migration generation đang có thay đổi lớn quanh major tiếp theo. Prisma được ưu tiên cho productivity và migration history ổn định hơn; custom SQL vẫn giữ RLS/constraints. Revisit nếu Prisma transaction/RLS integration không pass gate hoặc query control trở thành bottleneck.

## Acceptance evidence and conditions

- Project owner explicitly approved with `APPROVE ADR-0001` on `2026-08-13`.
- Document-level compatibility: `PASS` — supported Node/Next/PostgreSQL/Prisma lines and minimum requirements were checked against primary documentation.
- RLS test design: `PASS` — non-owner application role, forced RLS and transaction-local tenant context are mandatory.
- Decimal test design: `PASS` — canonical strings, domain Decimal and PostgreSQL numeric round-trip are mandatory.
- Dependency budget: `PASS` — no package/provider outside the stated budget has been authorized.
- Exact-version installation and compatibility smoke test: `DEFERRED_TO_BOOT-001` by design; no package was installed during FND-001.

## Review triggers

Review lại ADR nếu:

- RLS/connection-pool isolation không thể pass bằng Prisma;
- deployment bắt buộc Edge runtime hoặc platform không hỗ trợ long-lived Node/transactions;
- pilot chứng minh workload chủ yếu là document/ML batch thay vì interactive web;
- team ownership thay đổi mạnh sang Python hoặc .NET;
- supported/LTS status của một thành phần kết thúc trước production launch.

## Decision record

`APPROVE ADR-0001` received from the project owner on `2026-08-13`. This completes `FND-001`. Future changes follow the Constitution amendment/ADR process and the review triggers above.

## Evidence consulted

- [Node.js release status](https://nodejs.org/en/about/previous-releases)
- [Node.js TypeScript support](https://nodejs.org/dist/latest/docs/api/typescript.html)
- [Next.js support policy](https://nextjs.org/support-policy)
- [Next.js deployment options](https://nextjs.org/docs/app/getting-started/deploying)
- [PostgreSQL exact numeric](https://www.postgresql.org/docs/18/datatype-numeric.html)
- [PostgreSQL row security](https://www.postgresql.org/docs/18/ddl-rowsecurity.html)
- [PostgreSQL transaction-local settings](https://www.postgresql.org/docs/18/functions-admin.html)
- [Prisma system requirements](https://docs.prisma.io/docs/orm/reference/system-requirements)
- [Prisma Decimal](https://docs.prisma.io/docs/orm/prisma-client/special-fields-and-types)
- [Prisma Migrate](https://docs.prisma.io/docs/orm/prisma-migrate)
- [Django supported versions](https://www.djangoproject.com/download/)
- [.NET lifecycle](https://learn.microsoft.com/en-us/lifecycle/products/microsoft-net-and-net-core)
