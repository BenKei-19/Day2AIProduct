# Phase 1-2 — Problem Scan & Quick Problem Cards

## Phase 1 — SCAN

Tôi đóng vai AI Product Engineer tại Vin Smart Future. Các vấn đề dưới đây được chọn theo bốn lens: lặp lại, tốn thời gian, AI có thể nâng cấp và pain từ stakeholder.

| # | Subsidiary | Lens | Mô tả ngắn bài toán |
|---:|---|---|---|
| 1 | VinFast | AI-upgrade | Trợ lý hướng dẫn trạm sạc thông minh: đề xuất lịch trình sạc và trạm còn chỗ, phù hợp với loại cổng sạc của từng dòng xe điện. |
| 2 | Vinhomes | Lặp lại | Phân loại và điều hướng phản ánh cư dân như mất nước, hỏng đèn hoặc ồn ào đến đúng ban quản lý tòa nhà. |
| 3 | Vinmec | Tốn thời gian | Soạn bản nháp tóm tắt hồ sơ xuất viện từ bệnh án điện tử, kết quả xét nghiệm và ghi chú của bác sĩ. |
| 4 | VinUni | Lặp lại | Phân tích lỗi cú pháp/logic trong bài lab lập trình và tạo phản hồi mang tính sư phạm cho sinh viên sau khi autograder chạy. |
| 5 | Vinpearl | Pain từ stakeholder | Tổng hợp review khách sạn từ Booking.com, Agoda và Google Maps, phát hiện phàn nàn khẩn cấp rồi chuyển cho quản lý. |

### Vì sao 5 vấn đề này phù hợp

- Đều có thao tác lặp lại hoặc tốn thời gian, nên có thể đo baseline và hiệu quả sau cải tiến.
- Đầu ra hỗ trợ rõ ràng: đề xuất trạm sạc, phân loại, tóm tắt, phân tích lỗi hoặc tạo cảnh báo.
- Quyết định rủi ro cao vẫn có thể giữ Human-in-the-loop.
- Có thể bắt đầu bằng scope hẹp, dữ liệu mẫu và fallback thủ công.

## Phase 2 — QUICK-ASSESS

### Quick Problem Card #1 — Trợ lý hướng dẫn trạm sạc thông minh

**Bài toán (1 câu):** Chủ xe VinFast mất thời gian tìm trạm sạc còn chỗ và phù hợp với loại cổng của xe, dẫn đến hành trình sạc không tối ưu.

**Công ty thành viên:** [x] VinFast

**Ai đang đau (Actor)?** Chủ xe, tài xế đường dài và nhân viên hỗ trợ khách hàng.

**Workflow thủ công hiện tại (5 bước):**

1. Chủ xe nhập dòng xe, mức pin và điểm đến.
2. Hệ thống xác định vị trí và ước tính nhu cầu năng lượng của hành trình.
3. Nhân viên hoặc người dùng tra cứu thủ công các trạm gần tuyến đường.
4. Kiểm tra trụ còn chỗ và loại cổng tương thích như CCS2/GBT.
5. Chọn trạm, lập lịch trình và gửi hướng dẫn cho người lái.

**Bước tốn thời gian/lỗi nhất:** Bước 3-4, khoảng **8 phút/lượt**. Có thể đề xuất trạm hết chỗ, sai loại cổng hoặc không phù hợp với quãng đường còn lại.

**AI có thể hỗ trợ ở bước nào?** Bước 2-4: phân tích dòng xe, mức pin, điểm đến và dữ liệu trạm để đề xuất các lựa chọn phù hợp. Hệ thống phải dùng dữ liệu trạm thực tế và báo rõ khi không đủ thông tin.

**Đo thành công bằng gì?**

- Giảm thời gian tìm và chọn trạm từ **8 phút xuống dưới 2 phút/lượt**.
- Đạt ít nhất **98%** độ chính xác về tương thích cổng sạc.
- Giảm số lần người dùng đến trạm không còn chỗ ít nhất **30%**.

**Quick Architecture:** [ ] No AI  [x] Rule  [x] LLM Feature  [ ] Agent

**Ranh giới sơ bộ:** AI chỉ đề xuất từ danh sách trạm và dữ liệu tồn kho đã xác thực; không tự đặt chỗ, thanh toán hoặc cam kết trạm còn chỗ. Nếu thiếu dữ liệu hoặc độ tin cậy thấp, phải yêu cầu người dùng kiểm tra lại.

---

### Quick Problem Card #2 — Phân loại và phản hồi phản ánh cư dân

**Bài toán (1 câu):** Nhân viên Vinhomes phải đọc, phân loại và chuyển từng phản ánh của cư dân bằng tay, khiến yêu cầu bị chuyển nhầm hoặc chậm SLA.

**Công ty thành viên:** [x] Vinhomes

**Ai đang đau (Actor)?** Nhân viên CSKH, ban quản lý, bộ phận kỹ thuật và cư dân.

**Workflow thủ công hiện tại (5 bước):**

1. Cư dân gửi phản ánh qua ứng dụng hoặc tổng đài.
2. Nhân viên đọc nội dung và xác định tòa/khu vực.
3. Nhân viên phân loại chủ đề, mức độ khẩn cấp và phòng ban.
4. Nhân viên chuyển ticket, bổ sung ghi chú và soạn phản hồi.
5. Bộ phận phụ trách xử lý rồi cập nhật trạng thái cho cư dân.

**Bước tốn thời gian/lỗi nhất:** Bước 2-4, khoảng **10 phút/ticket**; nội dung viết tắt, thiếu thông tin hoặc có nhiều vấn đề dễ bị phân loại sai.

**AI có thể hỗ trợ ở bước nào?** Bước 2-4: trích xuất tòa nhà, loại sự cố, mức độ ưu tiên, phòng ban nhận ticket và tạo bản nháp phản hồi.

**Đo thành công bằng gì?**

- Phân loại đúng ít nhất **85% ticket trong dưới 10 giây**.
- Giảm thời gian soạn phản hồi từ **10 phút xuống dưới 2 phút/ticket**.
- Giảm tỷ lệ chuyển nhầm ticket ít nhất **30%** so với baseline.

**Quick Architecture:** [ ] No AI  [x] Rule  [x] LLM  [ ] Agent

**Ranh giới sơ bộ:** AI không tự hứa thời hạn, bồi thường, miễn phí, thay đổi phí quản lý hoặc kết luận tranh chấp. Ticket không đủ tự tin phải chuyển cho nhân viên review.

---

### Quick Problem Card #3 — Tạo bản nháp tóm tắt hồ sơ xuất viện

**Bài toán (1 câu):** Bác sĩ Vinmec mất nhiều thời gian tổng hợp thông tin từ bệnh án, xét nghiệm và ghi chú để viết tóm tắt xuất viện cho từng bệnh nhân.

**Công ty thành viên:** [x] Vinmec

**Ai đang đau (Actor)?** Bác sĩ, nhân viên hành chính y tế và bệnh nhân chờ hồ sơ.

**Workflow thủ công hiện tại (5 bước):**

1. Bác sĩ mở bệnh án điện tử, kết quả xét nghiệm và ghi chú điều trị.
2. Bác sĩ đọc và chọn thông tin lâm sàng liên quan.
3. Bác sĩ viết tóm tắt chẩn đoán, điều trị, thuốc và hướng dẫn theo dõi.
4. Nhân viên kiểm tra trường bắt buộc và định dạng hồ sơ.
5. Bác sĩ review, chỉnh sửa và ký phát hành.

**Bước tốn thời gian/lỗi nhất:** Bước 1-3, khoảng **20-30 phút/bệnh nhân**; có nguy cơ bỏ sót thông tin hoặc diễn đạt khó hiểu.

**AI có thể hỗ trợ ở bước nào?** Bước 2-3: trích xuất thông tin đã có trong hồ sơ và tạo bản nháp theo mẫu, đồng thời đánh dấu trường thiếu hoặc mâu thuẫn.

**Đo thành công bằng gì?**

- Giảm thời gian soạn từ **20-30 phút xuống dưới 10 phút/bệnh nhân**.
- Đạt ít nhất **95%** độ đầy đủ các trường bắt buộc.
- **100%** hồ sơ được bác sĩ review và ký trước khi phát hành.

**Quick Architecture:** [ ] No AI  [ ] Rule  [x] LLM Feature  [ ] Agent

**Ranh giới sơ bộ:** AI chỉ tóm tắt dữ liệu có trong hồ sơ; không chẩn đoán, kê đơn, thay đổi dữ liệu y tế hoặc tự phát hành giấy tờ. Thiếu dữ liệu hoặc không chắc chắn thì chuyển bác sĩ xử lý.

## Xếp hạng top 5

1. **VinFast - trợ lý hướng dẫn trạm sạc:** lựa chọn tốt nhất để làm deep-dive vì có dữ liệu đầu vào, tiêu chí tương thích và metric rõ.
2. **Vinhomes - phân loại phản ánh cư dân:** phù hợp với rule router kết hợp LLM; cần dữ liệu ticket và chính sách phản hồi thực tế.
3. **Vinmec - tóm tắt xuất viện:** giá trị cao nhưng rủi ro y tế và bảo mật lớn; bắt buộc có bác sĩ duyệt.
4. **VinUni - phản hồi bài lab:** dễ thử nghiệm trên dữ liệu autograder, nhưng cần bảo đảm phản hồi giúp học chứ không làm bài thay sinh viên.
5. **Vinpearl - tổng hợp review:** dễ làm prototype, nhưng tác động vận hành trực tiếp và độ khẩn cấp thấp hơn ba lựa chọn đầu.

## Xác nhận hoàn thành Phase 1-2

- **Phase 1:** Hoàn thành vì đã liệt kê 5 vấn đề thực tế, có công ty, lens và mô tả bottleneck.
- **Phase 2:** Hoàn thành vì đã hoàn thiện 3 Quick Problem Cards; mỗi thẻ có bài toán, actor, workflow 3-5 bước, bottleneck kèm thời gian, điểm AI, metric định lượng, kiến trúc và ranh giới sơ bộ.
