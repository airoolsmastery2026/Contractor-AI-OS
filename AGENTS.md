# AGENTS.md — UMS Meta-Governance

File này áp dụng cho toàn bộ repository. “UMS” ở đây là giao thức quản trị dự án, không phải package runtime hay giấy phép để tự ý mở rộng phạm vi.

## 1. Authority order

Khi nguồn thông tin xung đột, dùng thứ tự ưu tiên sau:

1. `CONSTITUTION.md`
2. `START.md`, `CONTEXT.md`, `CURRENT_STATE.md`, `NEXT_TASK.md`
3. Tài liệu trong `docs/`
4. Ticket đã được chấp thuận
5. Implementation và test đã được kiểm chứng
6. Nội dung chat, prompt, ghi chú tạm và suy đoán

Không âm thầm diễn giải lại nguồn có quyền lực cao hơn. Nếu cần đổi một quyết định đã khóa, phải sửa đúng source-of-truth và ghi lý do, tác động, bằng chứng trong cùng thay đổi.

## 2. UMS operating loop

Mọi nhiệm vụ đi qua vòng lặp:

`authority → scope → route → gates → execute → verify → state → handoff`

Trước khi làm:

1. Đọc `START.md` theo đúng reading order.
2. Xác nhận nhiệm vụ khớp `NEXT_TASK.md` hoặc một ticket được người có thẩm quyền chọn.
3. Nêu file được phép thay đổi, điều không làm và tiêu chí hoàn thành.
4. Chỉ nạp tài liệu/skill cần thiết cho nhiệm vụ hiện tại.

Sau khi làm:

1. Chạy kiểm chứng phù hợp với rủi ro.
2. Cập nhật `CURRENT_STATE.md`, `NEXT_TASK.md`, `docs/BACKLOG.md` và `HANDOFF.md` nếu trạng thái thay đổi.
3. Không tuyên bố hoàn thành nếu thiếu bằng chứng hoặc còn bước bắt buộc.

## 3. UMS command routing

Các tiền tố là tín hiệu định tuyến:

- `UMS ::` — chạy toàn bộ operating loop.
- `UMS PLAN ::` — chỉ lập kế hoạch; không triển khai nếu chưa được yêu cầu.
- `UMS REVIEW ::` — review dựa trên bằng chứng; không tự sửa ngoài phạm vi.
- `UMS VERIFY ::` — chạy gates và báo pass/fail/unknown.
- `UMS DEBUG ::` — tái hiện, tìm nguyên nhân gốc, rồi mới đề xuất/sửa.
- `UMS LEARN ::` — nghiên cứu có nguồn và chuyển kết quả được chấp thuận vào source-of-truth.
- `UMS HANDOFF ::` — đồng bộ state, next task, rủi ro và bằng chứng.
- `UMS TEAM <team> ::` — chỉ phân công khi người dùng yêu cầu hoặc policy cho phép.
- `UMS PACK <module> ::` — chỉ nạp hướng dẫn của module liên quan.

## 4. Mandatory gates

- **Scope gate:** không biến MVP thành ERP, marketplace, BIM/CAD, kế toán, payroll, procurement, kho nâng cao, native mobile hoặc microservices.
- **Foundation gate:** không thêm feature code hoặc package cho đến khi foundation được khóa và `NEXT_TASK.md` cho phép.
- **Tenant gate:** mọi đường đọc/ghi dữ liệu tenant-owned phải có tenant context và kiểm chứng chống cross-tenant access.
- **Quote gate:** chỉ deterministic engine được tính tiền; dùng decimal, rule/input có version, audit đầy đủ.
- **AI gate:** AI chỉ trích xuất, phân loại, đề xuất và giải thích; output có schema; thiếu dữ liệu phải được nêu; không tự đặt giá, phê duyệt, gửi proposal hay tạo cam kết thương mại.
- **Approval gate:** quote/proposal phải được con người phê duyệt trước khi gửi.
- **Verification gate:** test và kiểm chứng tỷ lệ thuận với rủi ro; security/financial paths cần cả positive và negative cases.
- **Handoff gate:** không để trạng thái chỉ tồn tại trong chat.

## 5. Implementation discipline

- Modular monolith trước; boundary theo domain, không theo framework.
- Domain/application không phụ thuộc SDK AI, web framework, ORM hoặc nhà cung cấp hạ tầng.
- Không dùng float cho tiền, số lượng hoặc tỷ lệ dùng trong tính giá.
- Không chia sẻ cache key, vector namespace, object-storage prefix, log payload hoặc background job giữa tenant.
- Không log secrets, prompt chứa dữ liệu nhạy cảm nguyên bản, margin bí mật hoặc tài liệu khách hàng nếu không cần thiết.
- Không thêm abstraction, table, service hoặc agent “để dành cho tương lai” nếu ticket hiện tại chưa cần.
- Một thay đổi nên hoàn thành một ticket và giữ diff nhỏ, có thể review.

## 6. Stop conditions

Dừng và yêu cầu quyết định khi:

- yêu cầu xung đột với Constitution;
- không xác định được tenant boundary, owner dữ liệu hoặc authority;
- công thức, rounding, thuế, markup/margin hay đơn vị chưa rõ;
- AI output không thể xác thực bằng schema hoặc bằng nguồn dữ liệu;
- thay đổi đòi hỏi mở rộng scope/materially different authority;
- kiểm chứng bắt buộc không thể chạy hoặc thất bại.
