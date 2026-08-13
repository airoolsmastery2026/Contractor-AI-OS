# Quote Workflow Evidence Pack v0.1

Status: `PROVISIONAL — OWNER_REVIEW_REQUIRED`
Ticket: `DISC-001`
Prepared: `2026-08-13`

## Purpose

Tài liệu này ghi lại bằng chứng quan sát được từ archive riêng do project owner cung cấp, nhằm xác nhận workflow và calculation inputs trước `FND-002`. Đây chưa phải business truth đã được owner phê duyệt.

## Evidence language

- `OBSERVED`: nhìn thấy trực tiếp trong source đã cung cấp.
- `RECONCILED`: phép cộng/nhân có thể kiểm tra từ các giá trị hiển thị và khớp kết quả hiển thị.
- `CANDIDATE`: pattern có thể trở thành rule sau khi owner xác nhận.
- `UNKNOWN`: không có bằng chứng; không được suy đoán.
- `WITHHELD`: có trong source riêng nhưng không ghi vào public repository vì PII hoặc commercial sensitivity.

## Permission and sanitization

- Project owner đã chỉ rõ archive cục bộ và yêu cầu tiếp tục phân tích: `OBSERVED`.
- Quyền đọc cục bộ và tạo bản tóm tắt đã ẩn danh: `OBSERVED`.
- Quyền publish ảnh/video, tài liệu gốc, tên file gốc, số điện thoại, địa chỉ, chân dung hoặc bảng giá chi tiết: `NOT_GRANTED`.
- Raw media không được commit. Tên khách hàng, thông tin liên hệ, địa chỉ, source filename và commercial values chi tiết được bỏ hoặc đánh dấu `WITHHELD`.
- Ba case dưới đây là candidate profiles; việc chúng có phải báo giá thật đã gửi khách hay không vẫn là `UNKNOWN` cho đến owner review.

## Source inventory

Archive ngoài repo chứa:

- 235 ảnh JPEG;
- 6 video MP4;
- 1 archive RAR lồng;
- không có file PDF, DOCX, XLSX hoặc CSV gốc ở lớp ZIP ngoài.

Ảnh/video tạo thành ba cụm thời gian. Tất cả ảnh JPEG đọc được; không thấy GPS EXIF. RAR lồng có 102 media entries, 99 tên trùng với ZIP ngoài và 3 ảnh chân dung không liên quan đã bị loại khỏi discovery. Timestamp, raw filename và chân dung không được ghi vào repo.

## Workflow coverage

| Stage | Evidence | State |
|---|---|---|
| Customer request / lead intake | Không có message, brief, call note hoặc customer record ghép chắc chắn với quote | `UNKNOWN` |
| Reference files / site photos | Có ảnh công trình và ảnh chụp bảng giá/báo giá; mapping tới từng case chưa được xác nhận | `OBSERVED`, mapping `UNKNOWN` |
| Structured scope | Quote tables có item, mô tả vật liệu, kích thước, đơn vị và số lượng | `OBSERVED` |
| Missing-data questions | Không có lịch sử câu hỏi và người trả lời | `UNKNOWN` |
| Price source | Có price-list và supplier-web screenshots hỗ trợ; effective date, region và authority chưa đủ | `PARTIAL` |
| Calculation | Hai case hiển thị đủ line/section totals để kiểm tra tổng; một case bị crop | `PARTIAL`, hai case `RECONCILED` |
| Revision / change | Có một bảng phát sinh và một line tạm tính bằng 0 vì chưa thống nhất kết cấu; quan hệ với quote gốc chưa chắc chắn | `CANDIDATE` |
| Approval | Không có actor, timestamp hoặc approval record | `UNKNOWN` |
| Proposal send | Không có send record hoặc exact revision checksum | `UNKNOWN` |
| Won / lost | Có ảnh công trình hoàn thiện nhưng chưa chứng minh thuộc case nào | `UNKNOWN` |
| CRM transition | Không có activity/stage history | `UNKNOWN` |

## Case Q-001 — Simple interior quote candidate

### Observed

- Một spreadsheet screenshot đặt tên như báo giá nội thất cao cấp.
- Tám dòng gồm hạng mục nội thất, mô tả/vật liệu, kích thước, đơn vị, số lượng, đơn giá và thành tiền.
- Có dòng vận chuyển và lắp đặt dạng trọn gói.
- Currency được trình bày là VND.
- Tổng tám line amounts khớp grand total hiển thị: `RECONCILED`; exact commercial values `WITHHELD`.

### Provenance map

| Business field | Local source reference | Confidence |
|---|---|---|
| item description | `LOCAL-Q1/table/rows-1-8` | high |
| material/specification | `LOCAL-Q1/table/description` | high |
| dimensions | `LOCAL-Q1/table/dimensions` | high |
| unit and quantity | `LOCAL-Q1/table/unit-quantity` | high |
| unit price and line amount | `LOCAL-Q1/table/commercial-columns` | high, values withheld |
| grand total | `LOCAL-Q1/table/total-row` | high, value withheld |
| customer/project/date/revision | — | `UNKNOWN` |

### Unknowns

Original workbook, customer request, supplier/effective date, cost versus selling price, waste, overhead, markup, discount, tax treatment, rounding policy, approver, send evidence và outcome đều `UNKNOWN`.

## Case Q-002 — Complex interior estimate candidate

### Observed

- Một bộ dự toán nhiều trang cho bếp và nhiều phòng ngủ.
- Line items có mô tả, hình minh họa, kích thước, đơn vị, hệ số, khối lượng, đơn giá và thành tiền.
- Tách các section cho tủ/bếp, đá-kính-LED, phụ kiện/labor và phòng ngủ.
- Một line tủ áo có đơn giá và thành tiền bằng 0 với ghi chú chưa thống nhất kết cấu.
- Ghi chú nói transport/installation trong một khu vực được bao gồm; thuế được mô tả là chưa bao gồm.
- Tổng line amounts khớp section totals; tổng các section khớp grand total: `RECONCILED`; exact commercial values `WITHHELD`.

### Provenance map

| Business field | Local source reference | Confidence |
|---|---|---|
| section/item hierarchy | `LOCAL-Q2/pages-1-3/sections-A-F` | high |
| description/spec/image | `LOCAL-Q2/pages-1-3/item-detail` | high |
| dimensions/unit/factor/quantity | `LOCAL-Q2/pages-1-3/measurement-columns` | high |
| unit price/line/section/grand totals | `LOCAL-Q2/pages-1-3/commercial-columns` | high, values withheld |
| transport/install inclusion | `LOCAL-Q2/final-page/notes` | medium; owner confirmation required |
| tax exclusion | `LOCAL-Q2/final-page/notes` | medium; exact policy `UNKNOWN` |
| customer/project/date/revision | — | `UNKNOWN` |

### Unknowns

Không biết quote này đã gửi khách chưa, tax base/rate thực tế, source/effective date của từng giá, discount, waste, overhead, markup, rounding boundaries, approval, revision chain, outcome hoặc ảnh công trình liên quan.

## Case Q-003 — Large multi-room quote / revision candidate

### Observed

- Bốn ảnh liên tiếp cho thấy các row 8–41 của một báo giá nội thất nhiều phòng.
- Có phân nhóm phòng, item, mô tả vật liệu/phụ kiện, kích thước millimetre, đơn vị, số lượng và đơn giá.
- Một bảng phát sinh riêng ghi các hạng mục cơ bản và phụ kiện phát sinh, nhưng không có bằng chứng đủ để khẳng định nó thuộc cùng quote.
- Cột thành tiền và grand total của quote chính bị crop hoặc thiếu ở source hiện có, nên không thể tái dựng đầy đủ.

### Provenance map

| Business field | Local source reference | Confidence |
|---|---|---|
| rows 8–41 scope/specification | `LOCAL-Q3/partial-pages/rows-8-41` | high |
| dimensions/unit/quantity/unit price | `LOCAL-Q3/partial-pages/measurement-commercial-columns` | high, values withheld |
| change/addendum candidate | `LOCAL-Q3/change-table` | low; relationship unconfirmed |
| rows 1–7/grand total | — | `UNKNOWN` |
| approval/revision/outcome | — | `UNKNOWN` |

### Unknowns

Original quote header, missing pages, line totals, total, source/effective dates, calculation formula, tax, discount, approval, send/outcome và quan hệ của bảng phát sinh đều `UNKNOWN`.

## Supporting evidence classification

| Evidence type | Permitted use | Restriction |
|---|---|---|
| Interior material/finish price tables | PriceBook field discovery | Không coi là active price; source date/region/authority `UNKNOWN` |
| Supplier web screenshots for polycarbonate | Source/provenance pattern discovery | Không dùng giá vào quote; effective date và capture integrity chưa xác nhận |
| Standalone change table | Revision/addendum workflow candidate | Không ghép vào Q-003 nếu chưa có owner evidence |
| ChatGPT price-advice screenshot | Negative example cho AI boundary | Không được dùng làm price truth hoặc authoritative source |
| Construction photos/videos | Outcome/reference-media taxonomy | Không map vào quote/customer/project trước owner confirmation |

## Unit taxonomy observed

Các label nhìn thấy gồm `m2`, `md`, `cái`, `bộ`, `hộp`, `món`, `gói` và dimension theo `mm`. Đây là raw labels, chưa phải canonical units.

Normalization candidates:

- `m2` → square metre;
- `md` → linear metre;
- `cái`, `bộ`, `hộp`, `món`, `gói` → count/package/service units cần semantic definition theo category;
- kích thước mm và quantity theo m/m2 phải có conversion rule versioned, không tự suy ra từ label.

## Rule candidates, not confirmed rules

1. `line_amount = quantity × unit_price` cho các line có đủ dữ liệu; rounding boundary chưa biết.
2. Tổng quote có thể được cộng theo section, sau đó thành grand total.
3. Transport/installation có thể là line riêng hoặc được bao gồm trong terms; cần biểu diễn explicit để tránh double count.
4. Accessory và labor có thể nằm ở section riêng.
5. Item chưa thống nhất có thể giữ trong scope với zero amount và blocking warning; không được approval im lặng.
6. Material/finish option có thể thay đổi đơn giá và phải là lựa chọn explicit.
7. Public/supplier price screenshot chỉ là candidate source; cần source, capture time, region, currency, tax treatment và effective window.

Không thấy bằng chứng đủ để xác nhận waste rate, overhead, markup, gross margin, discount order, tax base hoặc rounding policy.

## Conflicts and decision log

| ID | Observation/conflict | Required decision |
|---|---|---|
| D-01 | Tax được ghi là excluded trong một case nhưng các case khác không nói | Xác nhận tax treatment theo line/quote và thời điểm áp dụng |
| D-02 | Transport/install vừa có thể là line vừa có thể nằm trong note | Khóa representation và double-count prevention |
| D-03 | `md`, `m2` và count units cùng xuất hiện; conversion không hiển thị | Khóa canonical units và conversion rules |
| D-04 | Một zero-priced unresolved item vẫn nằm trong estimate | Quyết định đây là approval blocker hay explicit exclusion |
| D-05 | Bảng phát sinh không có link chắc chắn tới quote revision | Xác nhận revision/addendum identity và parent revision |
| D-06 | Ảnh công trình không có mapping tới quote | Owner cung cấp project alias mapping hoặc giữ outcome `UNKNOWN` |
| D-07 | AI-generated advice xuất hiện cùng source price data | Luôn phân loại AI output là non-authoritative draft |

## Data-classification input for SEC-001

- Customer name, phone, exact address: direct identifiers / restricted.
- Project/site photos and videos: potentially identifying/location-sensitive / restricted.
- Supplier contact and non-public terms: commercial confidential.
- Unit prices, discount, markup/margin and quote totals: commercial sensitive.
- Raw documents and filenames: restricted by default because they may embed identity metadata.
- Sanitized unit taxonomy and confirmed formula behavior: project documentation after owner approval.

## Owner review required

Owner must confirm or correct:

1. Q-001, Q-002, Q-003 có phải báo giá thật đã gửi khách hay chỉ là mẫu/tham khảo.
2. Project alias, quote date và category của từng case.
3. Original request, missing-data questions và ai xác nhận scope.
4. Source/effective date, unit semantics và tax/rounding/discount/markup policy thực tế.
5. Revision/approval/send evidence và outcome `WON`, `LOST` hoặc khác.
6. Bảng phát sinh có thuộc Q-003 không.
7. Cụm ảnh/video nào thuộc từng case.
8. Có cho phép commit sanitized numeric test vectors trong public repo hay chỉ lưu derived behavior.

## Acceptance state

| DISC-001 criterion | State |
|---|---|
| At least three representative candidate cases | `PASS — PROVISIONAL` |
| Raw PII/private media excluded from repo | `PASS` |
| Permission/sanitization note | `PASS — raw publication not granted` |
| Provenance map | `PARTIAL` |
| Calculation trace | `PARTIAL — two reconciled, one incomplete` |
| Rule candidates separated from confirmed rules | `PASS` |
| Revision/approval/outcome | `UNKNOWN/PARTIAL` |
| Project owner confirms business truth | `PENDING` |

`DISC-001` không được chuyển `DONE` và `FND-002` không được mở khóa trước owner confirmation.
