# Architecture

Status: `FOUNDATION_V0_1_LOCKED`

## Style and dependency rule

Contractor AI OS là modular monolith. Một deployable application chứa các module độc lập về domain/persistence boundary. Chỉ tách service khi có bằng chứng, không tách theo màn hình hoặc vendor.

Dependency direction:

`delivery/infrastructure adapters → application use cases → domain`

Domain không import web framework, ORM, AI SDK, storage SDK hoặc email/messaging provider. Cross-module work đi qua application contract; không query trực tiếp table nội bộ của module khác.

## Logical modules

| Module | Owns | May depend on |
|---|---|---|
| Identity & Tenancy | tenant, membership, role, tenant context | platform auth port |
| CRM | customer, lead, project, activity, pipeline transitions | Identity & Tenancy |
| Documents & Intake | document metadata, ingestion, extraction draft | CRM contracts, AI port, storage/parser ports |
| Catalog & PriceBook | material, product, supplier, price/rule versions | Identity & Tenancy |
| Quoting | scope confirmation, quote/revision/item/calculation trace | CRM and PriceBook contracts |
| Proposals & Approval | proposal revision, approval, send state | Quoting and CRM contracts |
| AI Orchestration | task policy, provider routing, structured result, run metadata | AI provider ports; application contracts only |
| Audit | append-only security/business events | receives sanitized events from all modules |

Các module có thể cùng database nhưng phải có ownership rõ. ORM relation không được trở thành đường tắt phá boundary.

## Multi-tenant request path

1. Delivery adapter xác thực principal.
2. Tenant được lấy từ route/session và đối chiếu membership; client payload không phải authority.
3. `TenantContext` gồm tenant, actor, role/scopes và correlation ID được tạo.
4. Application use case authorize hành động.
5. Repository bắt buộc tenant scope; transaction đặt DB security context nếu dùng RLS.
6. Response, event, cache/storage/search keys và audit record giữ đúng tenant boundary.

Background jobs mang signed/validated tenant context tối thiểu, re-authorize system capability và idempotency key. Không có “default tenant”. Thiếu tenant context phải fail closed.

## Authorization baseline

Role sơ bộ: `OWNER`, `ADMIN`, `ESTIMATOR`, `SALES`, `VIEWER`. Permission cụ thể được khóa bằng ticket security; không hard-code kiểm tra role rải rác. Các hành động cần capability riêng ít nhất gồm quản lý member, sửa PriceBook/rule, approve, send, privileged export và cross-tenant support.

## Deterministic quote contract v0.1

Đầu vào đã validate:

- currency và minor-unit rounding policy;
- quote lines với type, quantity, unit, unit cost, source/version;
- `waste_rate`, labor, accessories, transport, other cost;
- `overhead_rate`, `markup_rate`, discount, `tax_rate`;
- pricing rule version và effective time.

Công thức chuẩn:

```text
material_base = Σ round(quantity × unit_cost) for material lines
waste_cost = round(material_base × waste_rate)
other_direct_cost = Σ labor + accessories + transport + other cost lines
direct_cost = material_base + waste_cost + other_direct_cost
overhead = round(direct_cost × overhead_rate)
cost_basis = direct_cost + overhead
markup = round(cost_basis × markup_rate)
pre_tax_subtotal = max(0, cost_basis + markup - discount)
tax = round(pre_tax_subtotal × tax_rate)
grand_total = pre_tax_subtotal + tax
```

Mỗi line/component được round theo policy version. Rate có range validation. Unit conversion chỉ dùng conversion rule versioned; không đoán. `target_gross_margin` không thuộc formula v0.1. Mọi output gồm calculation trace và hash/identifier của input/rule snapshots.

Quote state:

`DRAFT → READY_FOR_APPROVAL → APPROVED → SENT → ACCEPTED | REJECTED | EXPIRED`

Thay đổi commercial input sau `APPROVED` tạo revision `DRAFT` mới; bản cũ giữ bất biến và có thể `SUPERSEDED`.

## AI provider abstraction

Application gọi một port khái niệm:

```text
AIProvider.execute(task, schema, sanitized_context, policy) -> AIResult
AIResult = structured_output + provenance + confidence + usage + model + provider + prompt_version
```

Adapter chịu trách nhiệm API, retry có giới hạn, timeout và mapping lỗi. Orchestrator chịu trách nhiệm chọn task policy/provider, nhưng validation nằm ngoài model. Provider fallback chỉ chạy khi privacy/cost/capability policy cho phép và không làm mất idempotency.

AI output luôn là draft. Parser/OCR/AI không được gọi PriceBook hoặc ghi quote trực tiếp; application use case validate, authorize và lưu kết quả có provenance.

## Documents, async work and idempotency

Binary object nằm trong tenant-scoped storage prefix; database giữ metadata, checksum, classification và access policy. Upload, parsing, AI extraction, proposal rendering và send có thể là background jobs. Mỗi job có idempotency key, retry policy, dead-letter/failed state và audit event; retry không được tạo duplicate proposal/send.

## Transactions and events

Một use case ghi dữ liệu và durable domain/outbox event trong cùng transaction khi cần side effect. In-process handlers không được che giấu failure. External send chỉ xảy ra sau commit và idempotent.

## Observability and privacy

Mọi request/job có correlation ID, tenant-safe metrics và structured error category. Log mặc định metadata, không log raw document/prompt/PII, secrets, private margin hoặc full provider response. Debug access cần capability, expiry và audit.

## Deployment constraints

Deployment topology, framework và vendors chưa được chọn. Lựa chọn phải hỗ trợ:

- transactional relational database và migrations;
- tenant isolation/RLS defense-in-depth;
- durable object storage và background work;
- secret management, backups và point-in-time recovery phù hợp;
- provider adapters thay thế được;
- automated unit, integration, security và end-to-end tests.
