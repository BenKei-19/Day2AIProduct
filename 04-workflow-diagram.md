# 04 — Workflow Diagram: Xanh SM Xử Lý Sự Cố Pin Tài Xế

> **Bài toán:** Tài xế Xanh SM báo sự cố pin giữa đường, điều phối viên phải tìm trạm sạc phù hợp

---

## Sơ đồ 1: Current-State Workflow (Quy trình hiện tại — Thủ công)

```mermaid
flowchart TD
    Start(["Tài xế gặp sự cố pin giữa đường"]) --> B1

    B1["Bước 1: Nhận cuộc gọi sự cố
    Ai: Điều phối viên
    Thời gian: 2 phút
    Input: Cuộc gọi điện thoại
    Output: Log sự cố"]

    B1 -->|"Handoff: Tài xế --> Điều phối viên"| B2

    B2["Bước 2: Tra cứu vị trí GPS xe
    Ai: Điều phối viên
    Thời gian: 2 phút
    Input: Biển số xe
    Output: Toạ độ GPS"]

    B2 --> B3

    B3["Bước 3: Tra cứu trạm sạc VinFast còn trụ trống
    Ai: Điều phối viên
    Thời gian: 5 phút
    Input: Vị trí GPS + Loại cổng sạc
    Output: Địa chỉ trạm sạc phù hợp"]

    B3 --> B4

    B4["Bước 4: Soạn tin nhắn hướng dẫn đường đi
    Ai: Điều phối viên
    Thời gian: 5 phút
    Input: Địa chỉ trạm sạc + Vị trí xe
    Output: Tin nhắn SMS/App"]

    B4 --> Check{"Pin dưới 5%?"}

    Check -->|"Không"| Send["Gửi tin nhắn hướng dẫn
    cho tài xế qua App"]
    Check -->|"Có"| B5

    B5["Bước 5: Gọi xe cứu hộ pin di động
    Ai: Điều phối viên
    Thời gian: 1 phút"]

    Send --> Done(["Kết thúc — Tổng: 15 phút/lượt"])
    B5 --> Done

    style B3 fill:#ff6b6b,color:#fff,stroke:#c0392b
    style B4 fill:#ff6b6b,color:#fff,stroke:#c0392b
    style B1 fill:#74b9ff,color:#fff,stroke:#0984e3
    style B2 fill:#74b9ff,color:#fff,stroke:#0984e3
    style B5 fill:#fdcb6e,color:#333,stroke:#f39c12
    style Start fill:#2d3436,color:#fff
    style Done fill:#2d3436,color:#fff
```

**Chú thích:**
- Đỏ = BOTTLENECK (bước tốn thời gian nhất)
- Xanh = Bước bình thường
- Vàng = Bước có điều kiện

---

## Sơ đồ 2: Future-State Workflow (Quy trình tương lai — Có AI hỗ trợ)

```mermaid
flowchart TD
    Start(["Tài xế gặp sự cố pin giữa đường"]) --> B1

    B1["Bước 1: Nhận cuộc gọi sự cố
    Ai: Điều phối viên
    Thời gian: 2 phút"]

    B1 --> B2

    B2["Bước 2: AI tự động tra cứu
    vị trí xe + trạm sạc trống
    Ai: AI + API VinFast
    Thời gian: 5 giây"]

    B2 --> PinCheck{"AI kiểm tra: Pin dưới 5%
    và trạm gần nhất > 5km?"}

    PinCheck -->|"Không"| B3
    PinCheck -->|"Có"| Rescue

    Rescue["AI tự động đề xuất:
    Điều xe cứu hộ pin di động
    KHÔNG chỉ dẫn đến trạm xa"]

    B3["Bước 3: AI soạn nháp tin nhắn
    hướng dẫn đường đi chi tiết
    Ai: LLM Gemini 2.5 Flash
    Thời gian: 10 giây
    Output bắt buộc: [DRAFT_ONLY]"]

    B3 --> B4

    Rescue --> B4

    B4{"Bước 4: Điều phối viên
    xem lại bản nháp"}

    B4 -->|"Đồng ý"| Send["Click Duyệt và Gửi
    tin nhắn cho tài xế
    Thời gian: 30 giây"]

    B4 -->|"Từ chối"| Fallback["FALLBACK: Điều phối viên
    tự soạn tin nhắn thủ công
    như quy trình cũ"]

    Send --> Done(["Kết thúc — Tổng: ~3 phút"])
    Fallback --> Done

    style B2 fill:#0984e3,color:#fff,stroke:#0652DD
    style B3 fill:#0984e3,color:#fff,stroke:#0652DD
    style B4 fill:#00b894,color:#fff,stroke:#00a885
    style Rescue fill:#fdcb6e,color:#333,stroke:#f39c12
    style Fallback fill:#e17055,color:#fff,stroke:#d63031
    style B1 fill:#74b9ff,color:#fff,stroke:#0984e3
    style Send fill:#55efc4,color:#333,stroke:#00b894
    style Start fill:#2d3436,color:#fff
    style Done fill:#2d3436,color:#fff
```

**Chú thích:**
- Xanh đậm = AI Step (tác vụ AI xử lý tự động)
- Xanh lá = HUMAN Step (con người phê duyệt — Human-in-the-loop)
- Vàng = Fallback pin dưới 5%
- Đỏ = Fallback khi AI lỗi

---

## So sánh hiệu suất

```mermaid
graph LR
    subgraph Current["HIỆN TẠI — Thủ công"]
        C1["15 phút / lượt xử lý"]
        C2["Điều phối viên làm 100% thủ công"]
        C3["Dễ sai khi cao điểm"]
    end

    subgraph Future["TƯƠNG LAI — Có AI"]
        F1["3 phút / lượt xử lý"]
        F2["AI xử lý 80%, người duyệt 20%"]
        F3["Fallback an toàn khi AI lỗi"]
    end

    Current -->|"Giảm 80% thời gian"| Future

    style Current fill:#ff7675,color:#fff
    style Future fill:#55efc4,color:#333
```
