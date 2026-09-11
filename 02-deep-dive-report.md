# 02 — Deep-Dive Report (Bài làm nhóm)

> **Nhóm:** *(Điền tên nhóm)*
> **Bài toán:** Xanh SM — Tài xế báo sự cố pin giữa đường, điều phối viên phải tìm trạm sạc phù hợp
> **Công ty thành viên:** Xanh SM (GSM)

---

## Bối cảnh

Xanh SM (GSM) vận hành đội xe taxi điện tại các thành phố lớn Việt Nam. Khi tài xế gặp sự cố hết pin giữa đường, điều phối viên (Dispatcher) tại Trung tâm Điều vận phải xử lý thủ công: tra cứu vị trí xe, tìm trạm sạc trống gần nhất, soạn tin nhắn hướng dẫn và gọi cứu hộ nếu cần. Quy trình này tốn 15 phút/lượt và gây áp lực lớn vào giờ cao điểm.

---

# Phase 3 — DEEP-DIVE

## 3.1. Current-State Workflow Mapping

Quy trình xử lý sự cố hết pin thực địa hiện tại của điều phối viên Xanh SM:

```text
+--------------+     +--------------+     +--------------+     +--------------+
| Bước 1       |     | Bước 2       |     | Bước 3       |     | Bước 4       |
| Nhận cuộc    |     | Tra cứu định |     | Tra cứu trạm |     | Soạn văn bản |
| gọi sự cố   | --> | vị GPS xe    | --> | sạc VinFast  | --> | hướng dẫn    |
|              |     |              |     | còn trụ trống|     | gửi tài xế   |
| Ai: Dispatch |     | Ai: Dispatch |     | Ai: Dispatch |     | Ai: Dispatch |
| Time: 2 phút |     | Time: 2 phút |     | Time: 5 phút |     | Time: 5 phút |
| In: Điện thoại|    | In: Biển số  |     | In: Vị trí   |     | In: Raw data |
| Out: Log SC  |     | Out: Toạ độ  |     | Out: Địa chỉ |     | Out: SMS     |
+--------------+     +--------------+     +--------------+     +--------------+
                                             [BOTTLENECK]        [BOTTLENECK]
                                                                      |
                                                                      v
                                                               +--------------+
                                                               | Bước 5       |
                                                               | Gọi xe cứu   |
                                                               | hộ (nếu cần) |
                                                               | Ai: Dispatch |
                                                               | Time: 1 phút |
                                                               +--------------+

[BOTTLENECK] = Bước gây tắc nghẽn, tốn thời gian nhất
Handoff: Bước 1 -> 2 (chuyển từ tài xế sang điều phối viên)
         Bước 4 -> 5 (chuyển từ hướng dẫn sang cứu hộ nếu pin < 5%)

Tổng thời gian xử lý thủ công: 15 phút/lượt
```

**Phân tích chi tiết các bottleneck:**

- **Bước 3 (5 phút):** Điều phối viên phải mở Dashboard trạm sạc VinFast, lọc theo khu vực gần vị trí xe, kiểm tra từng trụ sạc còn trống hay không, đối chiếu loại cổng sạc (CCS2/GBT) với dòng xe (VF5/VFe34/VF8). Việc này hoàn toàn thủ công và dễ sai khi có nhiều sự cố cùng lúc.

- **Bước 4 (5 phút):** Sau khi tìm được trạm sạc, điều phối viên phải soạn tin nhắn hướng dẫn đường đi bằng tiếng Việt thân thiện, bao gồm địa chỉ cụ thể, khoảng cách ước tính và lưu ý đặc biệt (ví dụ: "rẽ trái tại ngã tư X, trạm sạc nằm trong bãi đỗ VinMart"). Việc soạn thủ công mỗi lần khác nhau, không có template chuẩn.

---

## 3.2. Problem Statement (6-field) — Vin Smart Future Standard

| Field | Nội dung chi tiết |
|---|---|
| **1. Actor / Operator** | Điều phối viên (Dispatcher) thuộc Trung tâm Điều vận Xanh SM. Hiện có ~20 điều phối viên/ca trực, mỗi người xử lý 15-25 sự cố/ngày. |
| **2. Current Workflow** | Khi tài xế báo hết pin qua tổng đài, điều phối viên thực hiện 5 bước thủ công: (1) Tiếp nhận cuộc gọi, (2) Tra cứu vị trí GPS xe trên bản đồ nội bộ, (3) Mở Dashboard trạm sạc VinFast tìm trụ sạc trống gần nhất phù hợp loại cổng sạc, (4) Soạn tin nhắn hướng dẫn đường đi gửi qua App tài xế, (5) Gọi đội cứu hộ nếu pin dưới 5%. Toàn bộ quy trình mất 15 phút/lượt. |
| **3. Bottleneck** | Bước 3 và 4 (tổng 10 phút): Tra cứu thủ công trụ sạc trống phù hợp với dòng xe (VF5 dùng cổng GBT, VF8 dùng cổng CCS2) và soạn thảo tin nhắn hướng dẫn chi tiết bằng tiếng Việt. Khi nhiều sự cố xảy ra cùng lúc vào giờ cao điểm (17h-19h), điều phối viên bị quá tải dẫn đến sai sót: gửi sai trạm sạc, chỉ dẫn nhầm loại cổng sạc. |
| **4. Business Impact** | Trung bình ~80 sự cố pin thực địa/ngày tại Hà Nội. Lãng phí 20 giờ làm việc/ngày của team điều vận. Thời gian chờ đợi của tài xế tăng -> xe không thể đón khách -> rò rỉ doanh thu ước tính ~15%. Tài xế bị stress vì chờ đợi lâu dẫn đến tỉ lệ nghỉ việc tăng. |
| **5. Success Metric** | 1. Giảm tổng thời gian xử lý sự cố từ 15 phút xuống dưới 3 phút (Efficiency). 2. Tỉ lệ hướng dẫn đúng địa điểm và đúng loại trụ sạc phù hợp đạt 98% (Quality). 3. Giảm tỉ lệ tài xế phải gọi lại tổng đài vì hướng dẫn sai xuống dưới 2% (Accuracy). |
| **6. Operational Boundary** | AI được phép: Truy xuất API định vị xe, API trạm sạc VinFast trống, tự động soạn thảo tin nhắn hướng dẫn dạng nháp (draft). **CẤM:** (1) AI không được tự động gửi tin đi mà không có điều phối viên phê duyệt (Bắt buộc HITL — Human-in-the-loop). (2) AI không được đề xuất trạm sạc không phù hợp với loại cổng sạc của xe. (3) Khi pin < 5%, AI không được chỉ dẫn đến trạm sạc cách quá 5km — phải tự động đề xuất Xe Cứu Hộ Pin Di Động. |

---

## 3.3. Future-State Flow & AI Fit

### Xác định mức AI Fit:

| Tiêu chí | Rule / State-Machine | LLM Feature | Agentic Loop |
|----------|---------------------|-------------|--------------|
| Độ phức tạp ngôn ngữ | Không xử lý được mô tả tự nhiên | Hiểu ngôn ngữ TV, soạn tin thân thiện | Quá mức cần thiết |
| Cấu trúc quy trình | Cố định, rõ ràng | Cố định, rõ ràng | Không cần linh hoạt |
| Rủi ro khi sai | Cao (sai trạm sạc -> xe chết giữa đường) | Kiểm soát được qua HITL | Khó kiểm soát |
| **Kết luận** | Không đủ | **Phù hợp nhất** | Over-engineering |

**Lựa chọn: LLM Feature** — Lý do: Quy trình có cấu trúc cố định (5 bước rõ ràng), nhưng bước 3-4 cần khả năng xử lý ngôn ngữ tự nhiên để soạn tin nhắn hướng dẫn thân thiện. Không cần Agent tự trị vì rủi ro khi điều phối sai trạm sạc có thể khiến xe cạn kiệt pin giữa đường.

### Future-State Flow:

```text
+--------------+     +--------------+     +--------------+     +--------------+
| Bước 1       |     | Bước 2       |     | Bước 3       |     | Bước 4       |
| Nhận cuộc    |     | [AI] Tự động |     | [AI] Soạn    |     | [HUMAN]      |
| gọi sự cố   | --> | tra cứu vị   | --> | nháp tin nhắn| --> | Điều phối    |
|              |     | trí & trạm   |     | hướng dẫn    |     | viên duyệt   |
|              |     | sạc trống    |     | [DRAFT_ONLY] |     | & gửi tài xế |
| Ai: Dispatch |     | Ai: AI + API |     | Ai: LLM      |     | Ai: Dispatch |
| Time: 2 phút |     | Time: 5 giây |     | Time: 10 giây|     | Time: 30 giây|
+--------------+     +--------------+     +--------------+     +--------------+
                                                                      |
                                                                      v
                                                               Fallback:
                                                               Nếu AI draft lỗi,
                                                               Dispatcher tự viết
                                                               tay lại như cũ.

                                                               Fallback pin < 5%:
                                                               AI tự động đề xuất
                                                               điều xe cứu hộ pin
                                                               di động, KHÔNG chỉ
                                                               dẫn đến trạm xa.

Tổng thời gian xử lý mới: ~3 phút (giảm 80% so với hiện tại)

Ghi chú:
[AI]    = Tác vụ LLM xử lý tự động
[HUMAN] = Bước con người phê duyệt/review (Human-in-the-loop)
```

### Cơ chế Human-in-the-loop (HITL):
- Điều phối viên LUÔN LUÔN xem lại bản nháp tin nhắn trước khi gửi cho tài xế
- AI chỉ soạn nháp (draft), không bao giờ tự động gửi
- Nút "Duyệt & Gửi" và "Từ chối & Soạn lại" hiển thị rõ ràng trên giao diện

### Cơ chế Fallback:
1. **AI draft lỗi / không tự tin:** Nếu AI trả về confidence score < 80%, hiện cảnh báo cho điều phối viên và chuyển sang chế độ soạn thủ công
2. **Pin < 5% + trạm xa > 5km:** AI tự động chuyển sang đề xuất xe cứu hộ pin di động, không đề xuất trạm sạc xa
3. **API trạm sạc lỗi:** Nếu không kết nối được API trạm sạc, hiện thông báo lỗi và chuyển về quy trình thủ công

---

# Phase 5 — EVALUATE

## AI Readiness Checklist:

- [x] Chúng tôi có sẵn dữ liệu mẫu/logs sạch để test? — Có: Log sự cố pin từ hệ thống điều vận Xanh SM, dữ liệu trạm sạc từ API VinFast, lịch sử tin nhắn hướng dẫn cũ
- [x] Rủi ro khi AI sai có nằm trong tầm kiểm soát (qua HITL hoặc Fallback)? — Có: Điều phối viên luôn duyệt trước khi gửi. Khi pin < 5% có cơ chế fallback tự động
- [x] Stakeholders sẵn sàng thay đổi quy trình làm việc cũ? — Có: Đội điều vận đang quá tải, họ hoan nghênh công cụ giảm tải. Đã khảo sát 15/20 điều phối viên đồng ý thử nghiệm

## Quyết định cuối cùng của Ban Giám Đốc Vin Smart Future:

[x] **GO (Bắt đầu xây dựng Prototype):** Bắt đầu phát triển với scope hẹp.
[ ] **NOT YET (Cần tích lũy thêm dữ liệu/xác lập baseline):** Trì hoãn để chuẩn bị thêm.
[ ] **NO-GO (Không khả thi / Rule-based tốt hơn):** Hủy bỏ dự án AI này.

**Justification (Lý giải quyết định dựa trên bằng chứng kỹ thuật và chi phí):**

> Dự án được đánh giá đạt mức độ **GO** vì các lý do sau:
>
> 1. **Bài toán cụ thể, metric rõ ràng:** Giảm thời gian xử lý từ 15 phút xuống 3 phút, đo lường được bằng log hệ thống điều vận.
>
> 2. **Giải pháp công nghệ đơn giản mà hiệu quả:** Chỉ cần LLM Feature (single prompt + structured output + API lookup), không cần kiến trúc phức tạp. Chi phí API Gemini ước tính ~$0.01/lượt xử lý, rẻ hơn nhiều so với chi phí nhân sự 15 phút/lượt.
>
> 3. **Ranh giới an toàn kiểm soát chặt:** Có 3 lớp bảo vệ: (a) HITL bắt buộc — điều phối viên duyệt trước khi gửi, (b) Pin < 5% tự động chuyển cứu hộ, (c) Fallback về thủ công khi AI không tự tin.
>
> 4. **Stakeholder sẵn sàng:** 75% điều phối viên (15/20 người) đồng ý tham gia pilot. Đội ngũ IT Xanh SM có thể tích hợp API trong 2 tuần.
>
> **Scope pilot đề xuất:** Thử nghiệm với 5 điều phối viên tại Trung tâm Hà Nội trong 2 tuần, chỉ áp dụng cho ca ngày (8h-17h) để theo dõi và điều chỉnh trước khi mở rộng.
