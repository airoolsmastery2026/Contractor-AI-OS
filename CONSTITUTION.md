# Contractor AI OS Constitution

Version: `0.1.0`
Status: `LOCKED`
Effective date: `2026-08-13`

## Article I — Mission and source of truth

Contractor AI OS giúp nhà thầu chuyên ngành đi từ yêu cầu của khách hàng đến báo giá/proposal có thể kiểm chứng và theo dõi cơ hội bán hàng trong một hệ thống.

Repository là source of truth. Quyết định chỉ tồn tại trong chat không có hiệu lực cho đến khi được ghi vào tài liệu chịu trách nhiệm. Khi có xung đột, áp dụng authority order trong `AGENTS.md`.

## Article II — Product boundary

MVP là `AI Quote-to-Close OS`, gồm sáu năng lực:

1. Lead Intake
2. Scope Agent
3. PriceBook
4. Deterministic Quote Engine
5. Proposal + Human Approval
6. Mini CRM

MVP không phải ERP. Các hạng mục kế toán, payroll, marketplace, BIM/CAD, procurement, kho nâng cao, scheduling phức tạp, native mobile, microservices và “đội quân agent” nằm ngoài phạm vi cho đến khi có bằng chứng thị trường và quyết định thay đổi Constitution.

## Article III — Architecture invariant

Hệ thống khởi đầu là modular monolith. Module sở hữu domain model và persistence của chính nó; module khác chỉ tương tác qua application contract được định nghĩa. Không trích microservice trước khi có bằng chứng về scale, isolation hoặc ownership độc lập đủ mạnh.

Business logic nằm trong domain/application, tách khỏi UI, framework, ORM, AI SDK và vendor hạ tầng.

## Article IV — Multi-tenant security invariant

Tenant isolation là điều kiện đúng đắn, không phải tính năng bổ sung.

- Mọi dữ liệu tenant-owned có `tenant_id` bắt buộc hoặc được sở hữu gián tiếp qua quan hệ không thể mơ hồ.
- Tenant context được xác thực tại boundary và truyền tường minh; không nhận `tenant_id` đáng tin từ payload của client.
- Query, unique constraint, cache key, object-storage key, search/vector namespace, event, job và log phải tenant-scoped.
- Database row-level security được dùng như lớp defense-in-depth khi stack hỗ trợ; application authorization vẫn bắt buộc.
- Truy cập cross-tenant chỉ dành cho system operation được định danh, tối thiểu quyền, audit và test riêng.
- Mỗi resource tenant-owned có negative test chứng minh tenant khác không thể đọc, sửa, liệt kê, suy đoán hoặc tham chiếu.

## Article V — Deterministic pricing invariant

LLM không bao giờ tính hoặc quyết định giá cuối cùng.

- Tiền, số lượng và tỷ lệ dùng decimal; không dùng binary floating point.
- Công thức, rounding, currency, tax, price source, effective date và business rule đều có version.
- Quote lưu input snapshot và rule snapshot đủ để tái tạo kết quả.
- `markup` và `gross margin` không được dùng thay nhau. MVP v0.1 dùng `markup_rate`; chiến lược khác cần rule version mới.
- Quote đã duyệt là bất biến; sửa đổi tạo revision mới.
- Chênh lệch tính toán, dữ liệu thiếu hoặc giá hết hạn phải chặn approval.

## Article VI — AI boundary invariant

AI được phép đọc, phân loại, trích xuất có schema, chỉ ra dữ liệu thiếu, đề xuất và giải thích. AI không được:

- bịa thông số, nguồn giá hoặc mức độ chắc chắn;
- ghi trực tiếp vào pricing truth;
- vượt qua validation hoặc authorization;
- tự phê duyệt/gửi proposal;
- thay đổi stage thương mại hoặc tạo cam kết với khách hàng ngoài rule được duyệt.

Mọi provider đi qua `AIProvider` port. Domain không biết model/vendor. Prompt version, model/provider, structured output, confidence, validation result, token/cost metadata phù hợp và hành động người dùng được audit nhưng phải giảm thiểu dữ liệu nhạy cảm.

## Article VII — Human control and auditability

Human approval là bắt buộc trước khi quote/proposal được gửi. Các chuyển trạng thái quan trọng, thay đổi giá/rule, truy cập đặc quyền, AI run và gửi tài liệu đều tạo audit event bất biến theo chính sách retention được phê duyệt.

## Article VIII — Quality gates

Một thay đổi chỉ hoàn thành khi:

- có acceptance criteria và boundary rõ;
- test cho business rule, tenant isolation và authorization tương ứng đã pass;
- calculation có test vector và khả năng tái tạo;
- migration có kế hoạch rollback/forward-fix trước khi áp dụng;
- logs không rò rỉ secret hoặc dữ liệu tenant ngoài nhu cầu;
- source-of-truth và handoff được cập nhật.

MVP chỉ đạt khi luồng trong `docs/PRODUCT.md` chạy end-to-end và audit được.

## Article IX — Dependency and scope discipline

Không cài package, tạo scaffold framework hoặc viết feature trước khi ticket cho phép. Mỗi dependency mới phải chứng minh nhu cầu hiện tại, ownership, security/licensing impact và cách loại bỏ/thay thế. Ưu tiên platform primitives và code đơn giản.

## Article X — Amendment process

Thay đổi Constitution cần:

1. mô tả vấn đề và bằng chứng;
2. nêu điều khoản cũ/mới, tác động và migration;
3. cập nhật version, ngày hiệu lực và tài liệu liên quan;
4. được chủ dự án chấp thuận tường minh.

Không amendment bằng cách để implementation âm thầm đi lệch tài liệu.
