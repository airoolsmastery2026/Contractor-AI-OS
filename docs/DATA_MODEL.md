# Conceptual Data Model

Tài liệu này khóa ownership và invariants, chưa phải migration schema.

## Shared principles

- ID là opaque, không mang tenant identity.
- Mọi tenant-owned row có tenant scope rõ và timestamps.
- Foreign key giữa dữ liệu tenant-owned phải chứng minh cùng tenant; application check không thay thế database constraints khi có thể biểu diễn.
- Unique keys như quote number, customer external ID và PriceBook code là tenant-scoped.
- Money lưu amount decimal + currency; quantity decimal + unit.
- Soft delete không thay thế audit. Legal/retention policy quyết định purge.
- Approved commercial records dùng immutable revision/snapshot.

## Core entities

| Entity | Tenant scope | Purpose / critical fields |
|---|---|---|
| Tenant | global root | name, status, locale/timezone defaults |
| User | global identity | identity subject, status; không chứa tenant role |
| Membership | tenant-owned | tenant, user, role/capabilities, status |
| Customer | tenant-owned | identity/contact fields, owner, status |
| Lead | tenant-owned | customer, source, stage, assigned user, timestamps |
| Project | tenant-owned | lead/customer, site/location metadata, status |
| Document | tenant-owned | owner resource, storage key, checksum, media type, classification |
| ExtractionDraft | tenant-owned | document/input refs, schema version, output, confidence, missing fields, status |
| Material | tenant-owned | code, description, unit, specification, status |
| Product | tenant-owned | code, configuration/specification, unit, status |
| Supplier | tenant-owned | identity, region, status |
| PriceBookItem | tenant-owned | subject, supplier/source, unit, currency, amount, tax treatment, effective window, version |
| PricingRule | tenant-owned | category, rates/policy, formula version, effective window, version |
| Scope | tenant-owned | project/lead, category, confirmed structured values, version, confirmed by/at |
| Quote | tenant-owned | project/lead, human-readable tenant-scoped number, current revision, lifecycle state |
| QuoteRevision | tenant-owned | quote, version, input snapshot, rule snapshot, calculation trace, totals, status |
| QuoteItem | tenant-owned | revision, type, description, quantity/unit, unit cost/source, calculated amount |
| Proposal | tenant-owned | quote revision, current proposal revision, lifecycle/send state |
| ProposalRevision | tenant-owned | rendered content/template version, terms, checksum, approval state |
| Approval | tenant-owned | resource type/id/version, decision, actor, timestamp, reason |
| Activity | tenant-owned | lead/project event, actor, type, safe metadata |
| AgentRun | tenant-owned | task, provider/model/prompt version, input/output refs, validation, usage, status |
| AuditLog | tenant-owned or system | actor/capability, action, resource, before/after refs, correlation, timestamp |
| OutboxEvent | tenant-owned/system | event type, aggregate/version, payload ref, publish/idempotency state |

## Relationship rules

- `User ↔ Tenant` chỉ qua `Membership`; một user có thể thuộc nhiều tenant với role khác nhau.
- `Lead` có thể có `Customer`; `Project` được tạo khi lead đủ điều kiện, không bắt buộc tại intake.
- `Scope` là dữ liệu đã được user xác nhận; `ExtractionDraft` không tự trở thành `Scope`.
- `QuoteRevision` tham chiếu snapshot, không phụ thuộc giá mutable để hiển thị lại lịch sử.
- `ProposalRevision` trỏ đúng một `QuoteRevision`; approval gắn đúng resource version.
- `AgentRun` tham chiếu dữ liệu qua controlled refs; không dùng nó làm business truth.
- `AuditLog` append-only đối với application role.

## Tenant-isolation constraints

- Composite constraints/indexes ưu tiên `(tenant_id, id)` hoặc equivalent để FK cross-tenant bị từ chối.
- Tất cả list/search/export đều tenant-filtered trước pagination/aggregation.
- Storage path dạng `tenants/{tenant_id}/...`; signed URL ngắn hạn và audit khi cần.
- Cache/vector/search indexes dùng tenant namespace và authorization filter; không chỉ filter sau retrieval.
- Analytics chỉ cross-tenant khi dữ liệu đã được phép, aggregate/anonymize phù hợp và chạy bằng system capability riêng.

## Lifecycle and immutability

- Price/rule edits tạo version/effective window; không rewrite giá đã dùng trong approved quote.
- Quote/proposal approval bị vô hiệu khi commercial revision thay đổi.
- Send record lưu recipient reference, provider message ID, revision checksum, actor và time.
- Accepted quote không bị sửa; correction tạo revision/quote thay thế có liên kết.

## Deferred physical decisions

Index strategy, JSON vs normalized columns, partitioning, encryption fields, retention/purge và RLS policy syntax được quyết định sau khi stack và pilot query patterns được khóa. Không tạo table mới chỉ để phản ánh mọi noun trong UI.
