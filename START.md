# START

Đây là entrypoint bắt buộc cho mỗi phiên làm việc.

## Reading order

1. `CONSTITUTION.md`
2. `CONTEXT.md`
3. `CURRENT_STATE.md`
4. `NEXT_TASK.md`
5. Tài liệu được `NEXT_TASK.md` liên kết
6. `docs/BACKLOG.md`
7. `HANDOFF.md`

Không cần đọc toàn bộ repo nếu nhiệm vụ không yêu cầu. Chỉ mở rộng context khi một dependency hoặc quyết định liên quan trực tiếp.

## Start protocol

Trước khi thay đổi:

- xác nhận authority và ticket;
- tóm tắt scope trong một câu;
- liệt kê file dự kiến thay đổi;
- ghi rõ các non-goals;
- xác định gates và cách kiểm chứng;
- kiểm tra worktree để không ghi đè thay đổi của người khác.

## Current operating mode

- Foundation v0.1 đã được khóa ở mức product invariant, architecture direction, security/AI/pricing boundaries và delivery plan.
- Chưa có application code, package hoặc runtime stack.
- Không bắt đầu ticket khác ngoài `NEXT_TASK.md` nếu chưa cập nhật source-of-truth.

## Finish protocol

Trước khi bàn giao:

- chạy verification tương ứng;
- cập nhật ticket status và bằng chứng;
- cập nhật `CURRENT_STATE.md` bằng sự thật, không bằng ý định;
- đặt đúng một next task trong `NEXT_TASK.md`;
- cập nhật `HANDOFF.md` đủ để một người khác tiếp tục mà không cần đọc chat.
