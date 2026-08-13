# Product Vision and MVP Scope

## Vision

Trở thành operating system đáng tin cậy cho nhà thầu chuyên ngành: mọi yêu cầu khách hàng được chuẩn hóa, mọi giá có nguồn và version, mọi báo giá có thể tái tạo, mọi proposal được con người kiểm soát và mọi cơ hội được theo dõi.

## North Star workflow

`Lead → files/messages → AI intake → structured scope → missing-data questions → PriceBook + rules → deterministic quote → human approval → proposal → send → CRM follow-up → won/lost`

## Primary users

- **Owner/approver:** kiểm soát giá, margin, điều kiện và proposal trước khi gửi.
- **Estimator/sales:** nhập lead, hoàn thiện scope, chọn dữ liệu giá, chỉnh draft và follow-up.
- **Operator/admin:** quản lý tenant, user/role, PriceBook và audit access.

Khách hàng cuối nhận proposal nhưng không phải user nội bộ của MVP.

## MVP modules

### 1. Lead Intake

Nhận text, metadata, ảnh và PDF; liên kết với customer/project; giữ file gốc, nguồn, thời gian và người tải lên. Intake không tự coi nội dung trích xuất là sự thật đã duyệt.

### 2. Scope Agent

Chuyển input thành schema theo category; trả về `value`, `unit`, `source_reference`, `confidence` và danh sách `missing_fields`. User xác nhận/correct trước khi scope trở thành input báo giá.

### 3. PriceBook

Quản lý material/product/labor/accessory/service prices với unit, region, supplier/source, effective window, currency, tax treatment, status và version. Giá hết hạn hoặc không tương thích đơn vị không được silently dùng.

### 4. Deterministic Quote Engine

Nhận scope đã xác nhận, price selection và pricing rule version; tạo calculation trace, warnings và quote revision. Cùng input/rule version phải cho cùng output.

### 5. Proposal and Approval

Tạo proposal từ quote revision, scope, timeline và terms. Approval xác nhận nội dung thương mại; thay đổi sau approval tạo revision mới và hủy hiệu lực approval cũ. Chỉ bản approved mới được gửi.

### 6. Mini CRM

Pipeline tối thiểu:

`NEW → QUALIFYING → QUOTING → PROPOSAL_SENT → NEGOTIATION → WON | LOST`

Activity log lưu thay đổi stage, communication metadata, approval, send và follow-up. AI được đề xuất follow-up nhưng user quyết định hành động.

## MVP user journey

1. User vào một tenant đã xác thực.
2. Tạo customer/lead và nhập yêu cầu hoặc tải tài liệu.
3. AI tạo scope draft có provenance/confidence và liệt kê thiếu sót.
4. User bổ sung, sửa và xác nhận scope.
5. User chọn PriceBook data hợp lệ.
6. Engine tính quote revision bằng rule version đã chọn.
7. User xem calculation trace, warning và chỉnh input/rule được phép.
8. Hệ thống tạo proposal draft.
9. Approver phê duyệt quote/proposal.
10. User gửi approved proposal; event được audit.
11. CRM chuyển stage và theo dõi won/lost/follow-up.
12. Mọi hành động quan trọng có thể truy vết theo tenant, actor, thời gian và revision.

## Definition of Done

MVP chỉ hoàn thành khi toàn bộ user journey trên chạy end-to-end với:

- tenant isolation và RBAC negative tests;
- calculation test vectors, reproducibility và revision history;
- schema validation, missing-field behavior và provider failure handling;
- human approval trước send;
- audit trail cho price/rule/quote/proposal/stage/AI events;
- pilot acceptance trên quote samples đại diện.

## Explicit non-goals

- full ERP, accounting, payroll, inventory optimization;
- marketplace, procurement network hoặc supplier bidding;
- BIM/CAD editor, automated takeoff toàn diện;
- complex project scheduling/resource planning;
- native mobile apps;
- microservices hoặc general-purpose multi-agent platform;
- AI tự negotiate, approve, send hoặc thay đổi price truth.

## Success measures to baseline

Không đặt target giả khi chưa có pilot data. Pilot phải đo baseline và target cho:

- thời gian từ intake đến approved proposal;
- tỷ lệ scope thiếu/sai trước và sau user review;
- chênh lệch quote so với calculation được estimator duyệt;
- tỷ lệ quote phải rework và nguyên nhân;
- lead-to-proposal và proposal-to-win conversion;
- AI latency, failure rate và cost per processed lead;
- số sự cố tenant/auth/data leakage, mục tiêu luôn là zero.
