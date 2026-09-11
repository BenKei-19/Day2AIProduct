# 📄 01 — Problem Scan & Quick Cards (Cá nhân)

> **Deliverable:** I1. Scan & Cards — 15 điểm cá nhân
> **Thực hiện bởi:** *(Điền họ tên và mã sinh viên của bạn tại đây)*
> **Ngày:** 11/09/2026

---

# 🔍 Phase 1 — SCAN (Cá nhân)

Áp dụng **4 Lenses** để quét các bài toán vận hành thực tế trong hệ sinh thái Vingroup.

### 4 Lenses:
1. **Lặp lại (Repetitive):** Tác vụ lặp đi lặp lại nhiều lần hằng ngày.
2. **Tốn thời gian (Time-consuming):** Tác vụ ngốn thời gian xử lý thủ công.
3. **AI có thể tốt hơn (AI-upgrade):** Dịch vụ hiện tại chậm hoặc phản hồi rập khuôn.
4. **Pain từ người khác (Stakeholder Pain):** Bottleneck khiến khách hàng hoặc nhân viên phàn nàn.

---

### 📝 Danh sách 5 bài toán đã quét:

| # | Subsidiary | Lens | Mô tả ngắn bài toán |
|---|------------|------|---------------------|
| 1 | **Xanh SM** | Tốn thời gian | Điều phối viên xử lý thủ công các báo cáo hết pin / sự cố sạc từ tài xế (gọi điện thủ công, tra bản đồ, soạn tin chỉ đường), mỗi lượt mất 12–15 phút, ~80 lượt/ngày tại Hà Nội. |
| 2 | **VinFast** | Lặp lại | Nhân viên tài chính phải đối chiếu thủ công hóa đơn sạc điện từ hàng nghìn trụ sạc liên kết đối tác gửi về mỗi tuần với dữ liệu nội bộ, gây sai sót và mất 2–3 ngày/tuần. |
| 3 | **Vinhomes** | AI-upgrade | Hệ thống CSKH phân loại khiếu nại/phản ánh của cư dân trên App Vinhomes Resident hiện hoàn toàn thủ công, nhân viên mất 12 tiếng để route đúng phòng ban, tỉ lệ route sai ~18%. |
| 4 | **Vinmec** | Pain từ người khác | Bác sĩ mất 20–30 phút/bệnh nhân để tự soạn thảo bản tóm tắt xuất viện từ bệnh án điện tử, gây quá tải vào cuối ngày làm việc và tăng nguy cơ sai sót y khoa do mệt mỏi. |
| 5 | **Xanh SM** | Stakeholder Pain | Khách hàng đặt xe Xanh SM phàn nàn về thời gian chờ đón xe dài bất thường do hệ thống phân bổ tài xế chưa tính đến trạng thái pin thực tế của xe, dẫn đến tài xế được phân bổ phải ghé sạc trước khi đến đón. |

---

# 🃏 Phase 2 — QUICK-ASSESS (Cá nhân)

Chọn **top 3 bài toán** từ danh sách SCAN: **#1 (Xanh SM sự cố sạc), #3 (Vinhomes CSKH), #5 (Xanh SM phân bổ xe)** để phân tích nhanh.

> **Lý do loại bỏ #2 và #4:**
> - **#2 (VinFast đối chiếu hóa đơn):** Bài toán có cấu trúc dữ liệu rõ ràng, lặp lại cố định — rule-based / RPA giải quyết tốt hơn và rẻ hơn LLM.
> - **#4 (Vinmec tóm tắt xuất viện):** Lĩnh vực Y tế cần xác minh an toàn và tuân thủ pháp lý rất nghiêm ngặt trước khi triển khai AI vào quy trình lâm sàng.

---

## 🃏 QUICK PROBLEM CARD #1

```
┌─────────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                           │
│                                                                 │
│ Bài toán: Điều phối viên Xanh SM xử lý thủ công sự cố hết pin  │
│ của tài xế giữa đường: tra cứu trạm sạc, soạn tin chỉ dẫn.     │
│ Công ty thành viên: [x] Xanh SM (GSM)                          │
│                                                                 │
│ Ai đang đau (Actor)?                                            │
│ - Tài xế: Chờ đợi lâu (~15 phút) khi hết pin giữa đường.       │
│ - Điều phối viên: Quá tải xử lý thủ công ~80 sự cố/ngày.       │
│                                                                 │
│ Workflow thủ công hiện tại (5 bước):                            │
│   1. Tài xế gọi điện tổng đài báo sự cố hết pin / cần sạc      │
│   --> 2. Dispatch tra cứu vị trí GPS xe trên dashboard nội bộ   │
│   --> 3. Tìm thủ công trạm sạc VinFast trống phù hợp gần nhất  │
│   --> 4. Soạn tin nhắn chỉ đường tiếng Việt gửi qua App tài xế │
│   --> 5. Gọi xe cứu hộ sạc di động nếu pin < 5%                │
│                                                                 │
│ Bước nào tốn thời gian/lỗi nhất?                                │
│   Bước 3 & 4 (⏱ tổng ~10 phút/lượt — chiếm 67% tổng thời gian)│
│                                                                 │
│ AI có thể nhảy vào hỗ trợ ở bước nào?                           │
│   Bước 3–4: Auto-pull dữ liệu trạm sạc trống theo GPS          │
│   + LLM soạn thảo tin nhắn chỉ dẫn nháp [DRAFT_ONLY]           │
│   + Rule-engine kích hoạt dispatch_mobile_charger khi pin < 5%  │
│                                                                 │
│ Đo thành công bằng gì (Metric có số)?                           │
│   Giảm thời gian xử lý sự cố: 15 phút --> dưới 3 phút (giảm 80%)│
│   Tỉ lệ chỉ dẫn đúng trạm/cổng sạc phù hợp đạt >= 98%         │
│                                                                 │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent    │
└─────────────────────────────────────────────────────────────────┘
```

---

## 🃏 QUICK PROBLEM CARD #2

```
┌─────────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                           │
│                                                                 │
│ Bài toán: Phân loại và điều hướng tự động các khiếu nại/phản   │
│ ánh của cư dân gửi qua App Vinhomes Resident đến đúng ban       │
│ quản lý phụ trách từng tòa/khu vực.                             │
│ Công ty thành viên: [x] Vinhomes                                │
│                                                                 │
│ Ai đang đau (Actor)?                                            │
│ - Nhân viên CSKH Vinhomes: Phân loại thủ công ~200 ticket/ngày. │
│ - Cư dân: Chờ phản hồi 12h trong khi sự cố (mất nước, điện)    │
│   cần xử lý khẩn cấp trong vòng 1–2 tiếng.                      │
│                                                                 │
│ Workflow thủ công hiện tại (4 bước):                            │
│   1. Cư dân gửi phản ánh qua App (text tự do + ảnh/video)      │
│   --> 2. Nhân viên CSKH đọc nội dung và phân loại danh mục     │
│   --> 3. Forward thủ công ticket đến đúng phòng ban phụ trách   │
│   --> 4. Phòng ban xếp lịch xử lý và phản hồi cư dân           │
│                                                                 │
│ Bước nào tốn thời gian/lỗi nhất?                                │
│   Bước 2 & 3 (⏱ ~5 phút/ticket; tỉ lệ route sai ~18%)          │
│                                                                 │
│ AI có thể nhảy vào hỗ trợ ở bước nào?                           │
│   Bước 2–3: LLM đọc nội dung, phân loại danh mục (điện/nước/   │
│   tiếng ồn/hành chính...) và tự động route ticket + draft phản  │
│   hồi xác nhận cho cư dân [DRAFT_ONLY, duyệt trước khi gửi].    │
│                                                                 │
│ Đo thành công bằng gì (Metric có số)?                           │
│   Giảm thời gian route ticket: 12 tiếng --> dưới 2 phút         │
│   Tỉ lệ phân loại đúng phòng ban >= 95%                         │
│   Giảm tỉ lệ route sai: 18% --> dưới 3%                         │
│                                                                 │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent    │
└─────────────────────────────────────────────────────────────────┘
```

---

## 🃏 QUICK PROBLEM CARD #3

```
┌─────────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                           │
│                                                                 │
│ Bài toán: Hệ thống phân bổ tài xế Xanh SM chưa tính đến mức   │
│ pin thực tế của xe, khiến tài xế được điều phối phải ghé sạc   │
│ trước khi đón khách, làm tăng thời gian chờ đợi của khách.     │
│ Công ty thành viên: [x] Xanh SM (GSM)                          │
│                                                                 │
│ Ai đang đau (Actor)?                                            │
│ - Khách hàng: Chờ đón xe lâu hơn dự kiến (+8–15 phút).         │
│ - Tài xế: Bị đánh giá thấp do đến muộn, dù lỗi từ hệ thống.   │
│ - Dispatcher: Phải xử lý phàn nàn của khách, phân bổ lại.      │
│                                                                 │
│ Workflow thủ công hiện tại (4 bước):                            │
│   1. Khách đặt xe, hệ thống phân bổ tài xế gần nhất            │
│   --> 2. Tài xế nhận lệnh nhưng xe pin còn 8%, cần ghé sạc     │
│   --> 3. Tài xế tự quyết định ghé trạm sạc rồi mới đến đón     │
│   --> 4. Khách chờ lâu, gọi CSKH phàn nàn, dispatcher xử lý   │
│                                                                 │
│ Bước nào tốn thời gian/lỗi nhất?                                │
│   Bước 1–2: Hệ thống phân bổ không nhìn thấy trạng thái pin.   │
│   (⏱ thêm 8–15 phút/ca, ~40 ca/ngày = lãng phí ~600 phút/ngày)│
│                                                                 │
│ AI có thể nhảy vào hỗ trợ ở bước nào?                           │
│   Bước 1: Rule-engine + AI tích hợp telemetry pin xe thực tế    │
│   vào thuật toán phân bổ — chỉ điều phối tài xế có đủ pin.     │
│   Nếu không có xe đủ pin, AI cảnh báo và đề xuất ETA thực tế.  │
│                                                                 │
│ Đo thành công bằng gì (Metric có số)?                           │
│   Giảm % chuyến bị trễ do thiếu pin: 12% --> dưới 2%           │
│   Giảm phàn nàn CSKH liên quan đến chờ xe: 40 ca --> < 5 ca/ngày│
│                                                                 │
│ Quick Architecture: [ ] No AI  [x] Rule  [ ] LLM  [ ] Agent    │
│ (Rule-engine tích hợp real-time pin telemetry là ưu tiên)       │
└─────────────────────────────────────────────────────────────────┘
```

---

> **Đề xuất cho nhóm Deep-Dive:** Chọn **Card #1 (Xanh SM — Sự cố sạc pin thực địa)**
> vì đây là bài toán có tác động trực tiếp và tức thời nhất đến vận hành thời gian thực,
> đồng thời đã có bản mẫu kỹ thuật (prompt_prototype.py) kiểm chứng tính khả thi
> của Operational Boundary thông qua Adversarial Testing.
