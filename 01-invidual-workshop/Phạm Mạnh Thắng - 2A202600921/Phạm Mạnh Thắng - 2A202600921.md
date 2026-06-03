# Báo cáo Phân tích & Tối ưu hóa Luồng Trải nghiệm Người dùng (UX)
## Hệ thống Trợ lý AI Moni — Siêu ứng dụng MoMo

| | |
|---|---|
| **Người thực hiện** | Phạm Mạnh Thắng — 2A202600921 |
| **Đối tượng phân tích** | Chatbot Moni (Phiên bản 2026) |
| **Ngày lập báo cáo** | 03/06/2026 |

---

## 1. Tổng quan & bối cảnh phân tích

Báo cáo dựa trên dữ liệu tương tác thực tế từ ảnh chụp màn hình luồng hội thoại giữa người dùng và **Trợ thủ AI Moni** của MoMo vào đầu tháng 06/2026. Mục tiêu là nhận diện các rào cản trải nghiệm (UX Friction), phân tích điểm nghẽn trong khả năng hiểu ngôn ngữ tự nhiên (NLU), từ đó đề xuất giải pháp thiết kế hội thoại (Conversation Design) để gia tăng tỷ lệ chuyển đổi và mức độ hài lòng (CSAT).

> **Hiện trạng cốt lõi:** Moni xử lý tốt các truy vấn tra cứu dữ liệu tĩnh/lịch sử có cấu trúc, nhưng rơi vào lỗi phản hồi lặp rập khuôn (**Hard-coded Fallback Loop**) khi gặp yêu cầu hành động hoặc câu hỏi nằm ngoài phạm vi hẹp được định nghĩa trước.

---

## 2. Phân tích chi tiết các luồng trải nghiệm (UX Breakdown)

### Luồng 1 — Tra cứu biến động số dư và chi tiêu

**Truy vấn thực tế:** *"hãy kiểm tra lần gần nhất phát sinh giao dịch"* và *"hãy kiểm tra lần tôi chi tiêu gần nhất"*

**Điểm tích cực:**
- Bot nhận diện đúng intent, tự xác định khoảng thời gian (01/06/2026 → 03/06/2026).
- Tốc độ truy vấn thời gian thực tốt, phản hồi tường minh và chính xác về số dư.

**Điểm hạn chế (UX Gap):**
- Hệ thống hardcode phạm vi tìm kiếm chỉ trong 3 ngày đầu tháng, không tự mở rộng nếu khoảng thời gian đó trống dữ liệu.
- Hiển thị *"bạn chưa có khoản chi tiêu nào"* làm đứt gãy luồng — user phải tự nhập thêm khoảng thời gian thay vì bot gợi ý *"Trong 30 ngày qua..."*.

---

### Luồng 2 — Thực hiện tác vụ và yêu cầu tính năng

**Truy vấn thực tế (5 câu liên tiếp):** Gửi tin nhắn / Lì xì cho mẹ / Hỏi tính năng lì xì / Tạo lệnh chuyển tiền / Hỏi danh sách tính năng chung.

> **Lỗi nghiêm trọng — Fallback Loop:** Với cả 5 câu hỏi có ngữ cảnh hoàn toàn khác nhau, Moni chỉ trả về duy nhất 1 mẫu câu:
> *"Mình là Moni, trợ lý của MoMo. Mình chỉ có thể hỗ trợ trong phạm vi sản phẩm MoMo. Bạn cần giúp gì khác không?"*

Việc lặp 100% nội dung trong 5 lượt liên tiếp gây **User Frustration** cao và sụt giảm niềm tin vào AI. Nghịch lý: "Lì xì" và "Chuyển tiền" là dịch vụ **cốt lõi** của MoMo, nhưng bot phản hồi như thể chúng nằm ngoài phạm vi.

---

## 3. Đánh giá và Định vị Vấn đề

| Chỉ số | Giá trị |
|---|---|
| Lượt lỗi Fallback Loop ở Luồng Tác vụ | **5 / 5** |
| Tỷ lệ chuyển đổi sang Deeplink | **0%** |
| Chỉ số Giữ chân Khách hàng (Retention) | **Thấp** |

| Triệu chứng UX | Nguyên nhân kỹ thuật gốc (Root Cause) | Hệ quả với người dùng |
|---|---|---|
| **Lỗi Fallback lặp** | Intent chưa được ánh xạ vào bộ quy tắc hội thoại; hoặc mô hình NLU bị chặn ngưỡng confidence quá cao mà không có kịch bản rẽ nhánh nhỏ. | User cảm thấy chatbot bị lỗi/đơ → thoát app hoặc gọi tổng đài (tăng OpEx). |
| **Thiếu chuyển hướng (Deeplink)** | Phản hồi chỉ plain text, chưa tích hợp Rich Component (Button, Quick Reply, Deeplink). | User bị bỏ rơi trong hội thoại — biết app có tính năng nhưng không được chỉ lối tắt. |
| **Tra cứu giới hạn ngày** | Hardcode ngày từ 01 đến ngày hiện tại của tháng, không quét động theo lịch sử giao dịch của User ID. | User phải gõ thêm lệnh thủ công nhiều bước để tìm đúng dữ liệu. |

---

## 4. Đề xuất Chiến lược Tối ưu hóa (UX Action Plan)

### Giải pháp 1 — Tiered Fallback (Phá vỡ vòng lặp Fallback)

Thay 1 câu từ chối duy nhất bằng quy tắc 3 tầng:

- **Lần 1:** Khẳng định phạm vi hỗ trợ + gợi ý ngay các nhóm tính năng bằng nút bấm ("Tra cứu chi tiêu", "Xem hạn mức thẻ"...).
- **Lần 2:** Thay đổi văn phong, chủ động đặt câu hỏi lựa chọn để thu hẹp phạm vi.
- **Lần 3:** Hiển thị nút kết nối trực tiếp tổng đài viên (**Human-Agent Handoff**).

---

### Giải pháp 2 — Deeplink & Actionable Quick Replies

Với truy vấn liên quan đến dịch vụ nội tại MoMo (chuyển tiền, lì xì), hệ thống bắt keyword/Intent Slot để hiển thị Rich Card dẫn user đến tính năng:

**Ví dụ luồng sau tối ưu:**

> **User:** Bạn giúp tôi lì xì cho mẹ tôi 2k được không?
>
> **Moni:** Moni chưa thể tự động thực hiện lệnh chuyển tiền trực tiếp từ đoạn chat này nhằm bảo mật tài khoản của bạn.
> Tuy nhiên, MoMo có tính năng **Lì xì nhanh** rất tiện lợi!
> → `[🧧 Chuyển tiền Lì xì ngay]` `[Xem hướng dẫn]`

---

### Giải pháp 3 — Smart Date Range Query

Sửa logic: nếu đầu tháng đến ngày hiện tại không có dữ liệu giao dịch, tự động mở rộng về 30 ngày gần nhất và phản hồi:

> *"Trong tháng này bạn chưa chi tiêu, tuy nhiên giao dịch gần nhất của bạn là vào ngày [DD/MM] với số tiền [X VNĐ]..."*

Giúp giảm số bước tương tác tối đa, không buộc user tự nhập lại khoảng thời gian.

---

## 5. Lộ trình Triển khai (Roadmap)

| Giai đoạn | Thời gian | Nội dung |
|---|---|---|
| **Giai đoạn 1** | Tuần 1–2 | **Tối ưu khẩn cấp Fallback:** Cấu hình lại hệ thống để không lặp câu từ chối quá 2 lần trong cùng một session. |
| **Giai đoạn 2** | Tuần 3–4 | **Ánh xạ Intent Cốt lõi:** Bổ sung các cụm Intent của MoMo (Lì xì, Chuyển tiền, Nạp tiền điện thoại) vào NLU để kích hoạt nút Deeplink. |
| **Giai đoạn 3** | Tuần 5–6 | **A/B Testing:** Thử nghiệm luồng mới trên 10% khách hàng để đo cải thiện CSAT trước khi rollout toàn hệ thống. |

---

## 6. Đóng góp vào bài tập nhóm (Group Contributions)

| Commit | Nội dung |
|---|---|
| `83847f5` — *update evidence with case vinmec* | Thực hiện self-test trực tiếp trên Vinmec.com, chụp và thêm 2 screenshot (`vinmec1.png`, `vinmec2.png`) vào evidence pack; bổ sung 2 observation case Vinmec vào bảng evidence nhóm (form bắt user tự chọn Bệnh viện → Khoa → Bác sĩ, và case "đau đầu sắp ngất" không có triage). |
| `5c7b2ac` — *update synthesis* | Hoàn thiện mục 3 (Opportunity), mục 4 (Build slice — 5 câu hỏi kiểm tra), mục 5 (Quyết định giữ scope), mục 6 (Câu chốt cuối) và mục 7 (Backlog) trong `synthesis-decide-toolkit.md`. |
