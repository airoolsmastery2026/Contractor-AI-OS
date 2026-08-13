# Contractor AI OS

Contractor AI OS là vertical AI SaaS cho nhà thầu chuyên ngành, tập trung vào một luồng duy nhất: biến yêu cầu rời rạc của khách hàng thành scope có cấu trúc, báo giá kiểm chứng được, proposal được phê duyệt và cơ hội bán hàng có thể theo dõi.

## Trạng thái

- Giai đoạn: `DISCOVERY_EVIDENCE_IN_REVIEW`
- Wedge: `AI Quote-to-Close OS`
- Kiến trúc: modular monolith
- Tính giá: deterministic; AI không quyết định giá cuối cùng
- Bảo vệ dữ liệu: multi-tenant isolation từ ngày đầu
- Code tính năng và dependency: chưa được thêm
- Việc kế tiếp duy nhất: owner review `DISC-001` trong [NEXT_TASK.md](NEXT_TASK.md)

## Bắt đầu ở đây

Đọc theo thứ tự:

1. [START.md](START.md) — cách khởi động một phiên làm việc.
2. [CONSTITUTION.md](CONSTITUTION.md) — các bất biến và cổng bắt buộc.
3. [CONTEXT.md](CONTEXT.md) — bối cảnh sản phẩm và các quyết định đã khóa.
4. [CURRENT_STATE.md](CURRENT_STATE.md) — trạng thái có bằng chứng.
5. [NEXT_TASK.md](NEXT_TASK.md) — đúng một việc được phép tiếp tục.

Chi tiết nền tảng nằm trong:

- [Product vision và MVP](docs/PRODUCT.md)
- [Architecture](docs/ARCHITECTURE.md)
- [Data model](docs/DATA_MODEL.md)
- [Accepted implementation stack ADR](docs/adr/0001-implementation-stack.md)
- [Discovery evidence pack](docs/discovery/quote-workflow-v0.1.md)
- [Roadmap](docs/ROADMAP.md)
- [Backlog/tickets](docs/BACKLOG.md)
- [Verification](docs/VERIFICATION.md)
- [Handoff](HANDOFF.md)

## Nguyên tắc đóng góp

Repository là source of truth. Chat, prompt và suy đoán không được ghi đè tài liệu đã khóa. Mọi thay đổi phạm vi hoặc bất biến phải cập nhật tài liệu chịu trách nhiệm, bằng chứng kiểm chứng và trạng thái bàn giao trong cùng một thay đổi.
