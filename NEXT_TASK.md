# NEXT_TASK

## DISC-001 — Xác nhận workflow bằng tối thiểu ba sample quote

Status: `READY`
Owner: `PROJECT_OWNER + PRODUCT`
Priority: `P0`

## Outcome

Một evidence pack đã được chủ dự án xác nhận, mô tả cách yêu cầu khách hàng thực sự trở thành scope, calculation inputs, quote revisions, approval và kết quả thương mại. Đây là business truth cho `FND-002`; không phải dữ liệu demo tự bịa.

## Required inputs

Cung cấp tối thiểu ba báo giá đại diện đã được phép sử dụng và đã xóa/che dữ liệu nhạy cảm không cần thiết:

1. một case đơn giản, scope tương đối đầy đủ;
2. một case có thông tin thiếu, lựa chọn vật liệu/phụ kiện, vận chuyển hoặc phát sinh;
3. một case có revision, discount, VAT/thuế, approval hoặc thay đổi sau trao đổi.

Mỗi case nên có những gì thực sự tồn tại: yêu cầu gốc, ảnh/PDF tham chiếu, scope cuối, nguồn/đơn giá, đơn vị, số lượng, labor, accessory, waste, transport, overhead, markup, discount, tax, total, người duyệt và kết quả won/lost. Trường không biết phải ghi `UNKNOWN`; không suy đoán.

## Execution

- De-identify customer, phone, address chi tiết, supplier secrets và commercial data không được phép lưu.
- Lập provenance map từ input gốc đến từng scope/calculation field.
- Ghi missing-data questions và ai là người trả lời.
- Tái dựng calculation trace; đánh dấu chỗ formula hiện tại không giải thích được.
- So sánh ba case để tạo rule candidates, unit taxonomy và exception list.
- Ghi approval/revision/send/CRM stage transitions thực tế.
- Trình project owner xác nhận evidence pack trước khi hoàn thành ticket.

## Deliverables

- `docs/discovery/quote-workflow-v0.1.md`
- các sample đã sanitize dưới dạng Markdown/JSON tối giản nếu được phép commit;
- decision log về unknowns/conflicts;
- input cho `FND-002` calculation policy và `SEC-001` data classification.

## Non-goals

- Không cài package hoặc scaffold application.
- Không viết quote engine/migration/schema implementation.
- Không đưa PII, secret, full private document hoặc dữ liệu chưa được phép vào repo.
- Không ép ba case khác nhau vào một formula bằng suy đoán.

## Acceptance criteria

- Tối thiểu ba case đại diện, có provenance và permission/sanitization note.
- Input, missing fields, price sources, units, cost components, rates, rounding/tax observations, revisions, approvals và outcome được mô tả hoặc ghi `UNKNOWN`.
- Rule candidates được tách khỏi confirmed rules.
- Project owner xác nhận evidence pack phản ánh workflow thực tế.
- `CURRENT_STATE.md`, `docs/BACKLOG.md`, `NEXT_TASK.md` và `HANDOFF.md` được đồng bộ.

## First action required

Project owner cung cấp ba sample quote đã sanitize hoặc chỉ rõ vị trí file được phép đọc. Không gửi dữ liệu cá nhân/bí mật không cần thiết.
