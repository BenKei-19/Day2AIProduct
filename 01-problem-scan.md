# 01 — Problem Scan & Quick Cards (Bài làm cá nhân)

> **Họ tên:** *(Điền tên bạn)*
> **Mảng kinh doanh chính:** VinFast — Xe điện thông minh, trạm sạc & chuỗi cung ứng

---

# Phase 1 — SCAN: Tìm kiếm cơ hội (Cá nhân)

Sử dụng **4 Lenses** quét qua hoạt động vận hành của các công ty thành viên Vingroup, tập trung mảng VinFast và các mảng liên quan.

### List bài toán của tôi:

| # | Subsidiary (VinFast/Xanh SM...) | Lens | Mô tả ngắn bài toán |
|---|----------------------------------|------|---------------------|
| 1 | **VinFast** | AI có thể tốt hơn | **Chẩn đoán lỗi xe từ mô tả tiếng Việt của khách hàng:** Khách hàng mô tả hiện tượng lỗi bằng ngôn ngữ tự nhiên (VD: *"xe đi qua gờ giảm tốc kêu cụp cụp ở bánh trước"*), nhân viên CSKH hiện tại phải mất 15-20 phút đọc hiểu, tra cứu thủ công bảng mã lỗi kỹ thuật để phân loại ban đầu trước khi chuyển cho kỹ thuật viên. |
| 2 | **VinFast** | Lặp lại | **Đối chiếu hóa đơn sạc điện từ trạm đối tác:** Mỗi tuần, kế toán VinFast phải so khớp hàng nghìn giao dịch sạc điện từ các trạm sạc liên kết bên ngoài (Charge Point Operator) với hóa đơn thực tế gửi về hệ thống ERP tài chính. Quy trình lặp lại, dễ sai sót khi xử lý thủ công trên Excel. |
| 3 | **VinFast** | Tốn thời gian | **Trợ lý hướng dẫn trạm sạc thông minh cho khách hàng:** Khi khách hàng gọi hotline hỏi trạm sạc gần nhất, nhân viên tổng đài phải tra cứu thủ công Dashboard trạm sạc để tìm trụ trống phù hợp với loại cổng sạc (CCS2/GBT) của từng dòng xe (VF5, VFe34, VF8, VF9). Mất 5-8 phút/cuộc gọi. |
| 4 | **VinFast** | Pain từ người khác | **Quản lý và phân loại phản hồi sau bảo dưỡng xe:** Sau mỗi lần bảo dưỡng tại trung tâm dịch vụ, khách hàng gửi đánh giá qua App VinFast. Đội CSKH mất 2-3 giờ/ngày đọc và phân loại hàng trăm review để tìm ra phàn nàn khẩn cấp (lỗi tái phát, thái độ kỹ thuật viên) vs phản hồi tích cực. |
| 5 | **Vinmec** | Tốn thời gian | **Soạn thảo tóm tắt hồ sơ xuất viện (Discharge Summary):** Bác sĩ tại Vinmec mất 20-30 phút/bệnh nhân để trích xuất thông tin lâm sàng từ bệnh án điện tử, xét nghiệm, và ghi chú điều trị để soạn thảo bản tóm tắt xuất viện bằng ngôn ngữ dễ hiểu cho bệnh nhân. |
| 6 | **Xanh SM** | Lặp lại | **Phân tích lý do hủy chuyến từ ghi âm cuộc gọi:** Đội phân tích vận hành Xanh SM mất thời gian nghe lại hàng trăm file ghi âm hủy chuyến mỗi tuần để phân loại 10 nguyên nhân phổ biến nhất (tài xế đến muộn, sai điểm đón, thái độ...). |

---

# Phase 2 — QUICK-ASSESS: 3 Quick Problem Cards (Cá nhân)

Chọn **top 3 bài toán** từ danh sách SCAN: **#1 (VinFast Chẩn đoán lỗi xe), #2 (VinFast Đối chiếu hóa đơn sạc), #3 (VinFast Trợ lý trạm sạc).**

---

## Card #1 — VinFast Chẩn đoán lỗi xe từ mô tả tiếng Việt (Bài toán chính)

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                       │
│                                                             │
│ Bài toán (1 câu): Khách hàng VinFast mô tả hiện tượng lỗi │
│ xe bằng tiếng Việt tự nhiên, hệ thống cần tự động phân     │
│ loại thành mã lỗi kỹ thuật ban đầu để rút ngắn thời gian  │
│ tiếp nhận và chuyển đúng kỹ thuật viên chuyên môn.         │
│ Công ty thành viên: [x] VinFast                            │
│                                                             │
│ Ai đang đau (Actor)? Nhân viên CSKH tiếp nhận lỗi xe &    │
│ Kỹ thuật viên (chờ đợi phân loại ban đầu chậm)            │
│                                                             │
│ Workflow thủ công hiện tại (5 bước):                        │
│   1. Khách hàng gọi hotline/gửi App mô tả lỗi bằng TV     │
│   → 2. Nhân viên CSKH đọc hiểu mô tả khách hàng           │
│   → 3. Tra cứu thủ công bảng mã lỗi kỹ thuật (500+ mã)    │
│   → 4. Viết phiếu tiếp nhận sửa chữa (Service Order)      │
│   → 5. Chuyển phiếu cho kỹ thuật viên đúng chuyên môn      │
│                                                             │
│ Bước nào tốn nhất? Bước 2-3 (15 phút/lượt)                 │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2-4             │
│ (Tự động phân loại mã lỗi → Draft phiếu tiếp nhận)        │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│ "Giảm thời gian phân loại lỗi từ 15 phút ──> dưới 2 phút, │
│  đạt tỉ lệ phân loại đúng mã lỗi ≥ 90%"                   │
│                                                             │
│ Quick Architecture: [x] LLM Feature (Phân loại NLP + Draft)│
└─────────────────────────────────────────────────────────────┘
```

---

## Card #2 — VinFast Đối chiếu hóa đơn sạc điện đối tác

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                       │
│                                                             │
│ Bài toán (1 câu): Tự động so khớp dữ liệu giao dịch sạc  │
│ điện hằng tuần từ hàng nghìn trụ sạc liên kết đối tác     │
│ với hóa đơn thực tế trong hệ thống ERP tài chính VinFast.  │
│ Công ty thành viên: [x] VinFast                            │
│                                                             │
│ Ai đang đau (Actor)? Kế toán viên đối soát sạc điện        │
│                                                             │
│ Workflow thủ công hiện tại (4 bước):                        │
│   1. Xuất file CSV giao dịch sạc từ CPO Dashboard          │
│   → 2. Xuất file hóa đơn từ hệ thống ERP                   │
│   → 3. So khớp thủ công trên Excel (VLOOKUP/Pivot)         │
│   → 4. Lập báo cáo chênh lệch gửi quản lý phê duyệt       │
│                                                             │
│ Bước nào tốn nhất? Bước 3 (4 giờ/tuần cho ~3000 dòng)      │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 3               │
│ (Tự động matching + highlight bất thường)                   │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│ "Giảm thời gian đối soát từ 4 giờ/tuần ──> dưới 30 phút,  │
│  tỉ lệ phát hiện giao dịch bất thường đạt 99%"             │
│                                                             │
│ Quick Architecture: [x] Rule (ETL pipeline + rule matching) │
└─────────────────────────────────────────────────────────────┘
```

---

## Card #3 — VinFast Trợ lý hướng dẫn trạm sạc thông minh

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                       │
│                                                             │
│ Bài toán (1 câu): Khi khách hàng VinFast gọi hotline hỏi  │
│ trạm sạc, hệ thống tự động đề xuất trạm sạc trống gần     │
│ nhất phù hợp loại cổng sạc xe và draft tin nhắn chỉ dẫn.  │
│ Công ty thành viên: [x] VinFast                            │
│                                                             │
│ Ai đang đau (Actor)? Nhân viên tổng đài & Khách hàng (chờ) │
│                                                             │
│ Workflow thủ công hiện tại (4 bước):                        │
│   1. Khách gọi hotline hỏi trạm sạc gần                    │
│   → 2. Nhân viên hỏi dòng xe + vị trí hiện tại             │
│   → 3. Tra cứu Dashboard trạm sạc tìm trụ trống phù hợp   │
│   → 4. Đọc lại chỉ dẫn đường đi cho khách qua điện thoại   │
│                                                             │
│ Bước nào tốn nhất? Bước 3-4 (5-8 phút/cuộc gọi)            │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 3-4             │
│ (Auto-query API trạm sạc + Draft chỉ dẫn bằng LLM)        │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│ "Giảm thời gian xử lý cuộc gọi từ 8 phút ──> dưới 2 phút, │
│  tỉ lệ chỉ dẫn đúng trạm phù hợp cổng sạc đạt 98%"       │
│                                                             │
│ Quick Architecture: [x] LLM Feature (API lookup + NLG)     │
└─────────────────────────────────────────────────────────────┘
```

---

## Lựa chọn bài toán để Deep-Dive:

Tôi chọn bài toán **Card #1 — VinFast Chẩn đoán lỗi xe từ mô tả tiếng Việt** để đề xuất cho nhóm thực hiện Deep-Dive.

### Lý do lựa chọn và loại bỏ các thẻ khác:

* **Card #2 (Đối chiếu hóa đơn sạc):** Bài toán này có tính chất lặp lại và cấu trúc dữ liệu rõ ràng (CSV ↔ CSV), hoàn toàn có thể giải quyết tốt bằng **Rule-based ETL pipeline** (VLOOKUP tự động, fuzzy matching trên mã trạm + timestamp). Không cần đến LLM, do đó không phù hợp để showcase khả năng AI trong buổi lab.

* **Card #3 (Trợ lý trạm sạc):** Bài toán tốt nhưng tương tự với ví dụ mẫu (Xanh SM Dispatcher) trong file `02-deliverable-example.md` — cùng pattern tra cứu API + draft tin nhắn. Chọn bài này có nguy cơ bị đánh giá là "copy pattern" từ example.

* **Card #1 (Chẩn đoán lỗi xe) — ĐƯỢC CHỌN:** Bài toán **độc đáo**, đòi hỏi khả năng **hiểu ngôn ngữ tự nhiên tiếng Việt** phức tạp (mô tả hiện tượng → mã lỗi kỹ thuật). Ranh giới an toàn rõ ràng (lỗi liên quan an toàn lái xe PHẢI được escalate cho kỹ sư cao cấp). Có metric đo lường cụ thể. Phù hợp với LLM Feature architecture.
