# NEXT_TASK

## DISC-001 — Owner review evidence pack của ba sample quote

Status: `IN_REVIEW`
Owner: `PROJECT_OWNER + PRODUCT`
Priority: `P0`

## Outcome

Một evidence pack đã được chủ dự án xác nhận, mô tả cách yêu cầu khách hàng thực sự trở thành scope, calculation inputs, quote revisions, approval và kết quả thương mại. Đây là business truth cho `FND-002`; không phải dữ liệu demo tự bịa.

## Evidence prepared

Draft sanitized/provisional: [`docs/discovery/quote-workflow-v0.1.md`](docs/discovery/quote-workflow-v0.1.md).

- Ba candidate case đã được phân loại.
- Hai candidate có arithmetic reconciliation; một candidate thiếu dữ liệu để tính lại.
- Unit labels, source patterns, revision candidates, unknowns và data-classification inputs đã được ghi.
- Raw media, PII, filenames và detailed commercial values không được commit.

## Required owner review

Project owner xác nhận hoặc sửa:

1. mỗi candidate có phải quote thật đã gửi khách hay chỉ là mẫu/tham khảo;
2. project alias, date, category và original request;
3. source/effective date, unit, rounding, tax, transport/install, discount và markup behavior;
4. revision, approval, send và won/lost outcome;
5. quan hệ của bảng phát sinh và mapping ảnh/video;
6. permission cho sanitized numeric test vectors trong public repo.

Trường không biết giữ `UNKNOWN`; không suy đoán để đóng ticket.

## After owner review

- Áp dụng corrections vào evidence pack.
- Nếu được phép, thêm test vectors tối giản; không commit raw documents.
- Trình owner xác nhận bản cuối.
- Chỉ khi toàn bộ acceptance criteria pass mới chuyển `DISC-001` thành `DONE` và chọn next task theo dependency graph.

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

## Stop condition

Owner confirmation là gate bắt buộc. Trong lúc chờ, không bắt đầu `FND-002`, `SEC-001`, `BOOT-001` hoặc code/package work.
