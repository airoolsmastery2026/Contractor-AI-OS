# CONTEXT

Last updated: `2026-08-13`

## Problem

Nhà thầu nhỏ và vừa thường nhận yêu cầu qua tin nhắn, ảnh, PDF và cuộc gọi. Scope thiếu cấu trúc, giá nằm rải rác, báo giá phụ thuộc trí nhớ cá nhân, proposal chậm và lead dễ bị bỏ quên. Sai giá hoặc thiếu điều kiện thương mại trực tiếp làm mất margin và niềm tin.

## Product thesis

Một vertical AI system có giá trị khi kết hợp:

`workflow ngành + dữ liệu vật tư/giá + business rules + deterministic calculation + human approval + CRM feedback`

Lợi thế dài hạn nằm ở dữ liệu có cấu trúc, lịch sử giá, supplier intelligence, quote template, project outcome và conversion history; không nằm ở prompt dài.

## Initial users and market

- Người dùng đầu tiên: chủ doanh nghiệp, estimator/sales và người duyệt báo giá của nhà thầu nhỏ–vừa.
- Wedge ngành: cửa, cổng, cầu thang, lan can, mái che, cơ khí dân dụng và nội thất.
- Đại Hải Phát có thể là customer zero/pilot, nhưng tenant và dữ liệu của pilot không được hard-code vào sản phẩm.
- Chiến lược kinh doanh: service → productized service → SaaS → recurring revenue.

## Locked decisions

- North Star: yêu cầu khách hàng → scope → quote → approval → proposal → CRM.
- MVP có sáu năng lực được liệt kê trong Constitution.
- Modular monolith trước, PostgreSQL-oriented data model, multi-tenant từ ngày đầu.
- Quote engine deterministic; AI không tính giá cuối cùng.
- AI/provider abstraction; structured output + validation + human approval.
- Repo là source of truth; state/handoff phải được cập nhật cùng thay đổi.

## Open decisions

Các quyết định sau chưa được khóa và không được suy đoán:

- runtime, web framework, ORM/query layer và test runner;
- authentication/identity provider;
- deployment target, object storage, queue/job runner và observability stack;
- currency/locale đầu tiên và chính sách thuế được pilot xác nhận;
- rounding policy cuối cùng theo currency;
- document OCR/parser và AI providers đầu tiên;
- data retention, backup/RPO/RTO và residency requirements;
- kênh gửi proposal và cơ chế chữ ký/acceptance;
- baseline và target định lượng cho tốc độ báo giá, độ chính xác, conversion và cost/run.

Các mục này phải được quyết định bằng ticket/ADR hoặc bằng chứng pilot trước khi phụ thuộc được thêm.
