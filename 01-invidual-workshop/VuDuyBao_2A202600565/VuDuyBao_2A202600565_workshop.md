# Workshop — Mổ App AI Thật
**Vũ Duy Bảo · 2A202600565 · Block 3 · Day 05 · Slide 32/53**
# Link Deploy: https://vin-steel.vercel.app/ #
> **Thời gian:** 35–45 phút | **Hình thức:** Cá nhân → Chia sẻ nhóm | **Output:** Finding note + Sketch as-is / to-be

---

> **Mục tiêu không phải chấm "UI đẹp hay xấu".** Mục tiêu là dùng sản phẩm thật như một bài needfinding: tìm chỗ product gãy trong workflow thật, rồi viết finding đó thành **quyết định product**.

---

## 1. Chọn Một Sản Phẩm Để Dùng Thử

| Sản phẩm | AI Feature | Cách truy cập |
|---|---|---|
| **MoMo — Moni** ← `ĐÃ CHỌN` | Trợ thủ tài chính · Phân tích chi tiêu · Chatbot | App MoMo |
| Vietnam Airlines — NEO | Chatbot hỗ trợ vé · Hành lý · Khiếu nại | Website / Zalo VNA |
| V-App — V-AI | Trợ lý voice/text · Gợi ý theo ngữ cảnh | App V-App |

---

## 2. Dùng Thử: Promise vs Reality

### Ghi Nhanh

**Product hứa / Kỳ vọng:**
- AI tổng hợp chính xác toàn bộ giao dịch trong tháng, không bỏ sót.
- Tổng số tiền chi ra khớp với biến động số dư thực tế trong ví.
- Khi có sai lệch, AI chủ động cảnh báo và đề xuất hành động tiếp theo.
- Giao diện cung cấp công cụ tra cứu/xác minh nếu người dùng nghi ngờ kết quả.

**Thực tế khi dùng thật:**
- Danh sách giao dịch trả về bị thiếu — chênh lệch **~97.218đ** so với số dư thực.
- AI không phát hiện sai lệch, im lặng xuất báo cáo thiếu dữ liệu.
- Không có cơ chế cảnh báo hay hỏi lại người dùng khi dữ liệu bị miss.
- UI không có nút hoặc luồng nào cho phép người dùng kích hoạt quét lại.

### Evidence Đã Thu Thập

- ✓ Screenshot từ app (3 ảnh chụp màn hình MoMo — xem phần Evidence)
- ✓ Prompt / Input đã thử
- ✓ Hành vi quan sát được
- ✓ Số liệu cụ thể (97.218đ)

---

## 3. Vẽ 4 Paths

### ✅ Happy Path
> *Khi AI đúng và tự tin, user thấy gì?*

AI Moni quét và phân loại chính xác **5 giao dịch lớn nhìn thấy được** (300k, 1.279k, 1.161k, 619k, 200k). Bảng đối soát hiển thị đúng danh mục: Nhà cửa, Hóa đơn (×2), Giải trí, Chưa phân loại. User thấy tổng **3.560.148đ** và tin vào kết quả — không biết còn thiếu.

---

### ⚠️ Low-Confidence Path
> *Khi AI không chắc, hệ thống có hỏi lại, show options hoặc chuyển người không?*

Moni hỏi *"Bạn muốn xem tiếp các giao dịch còn lại không?"* — cho thấy hệ thống nhận ra còn dữ liệu chưa hiển thị. **Tuy nhiên**, không có cảnh báo về sai lệch số dư, không đề xuất quét sâu, không chuyển sang human support.

> *Path tồn tại nhưng không hoàn chỉnh — thiếu trigger phát hiện miss data.*

---

### ❌ Failure Path
> *Khi AI sai, user biết bằng cách nào và sửa thế nào?*

Thuật toán quét **bỏ sót ngầm ~97.218đ** (giao dịch CTCP FUNTEK VIỆT NAM ngày 04/05/2026). AI im lặng xuất báo cáo thiếu thông tin. User chỉ phát hiện khi tự đối chiếu với lịch sử giao dịch gốc — **không có cơ chế recovery**.

---

### 🔵 Correction Path
> *Khi user sửa, correction có được lưu/log/học lại không hay biến mất?*

**Path này chưa tồn tại trong product.** Không có nút "Quét lại", không có input để user báo cáo sai lệch, không có log để AI học từ lỗi. User không có cách nào trigger correction trong giao diện hiện tại.

> *Path hoàn toàn thiếu — đây là điểm gãy chính cần vá.*

---

## 4. Viết Finding Thành Quyết Định

> **Không viết:** "Bot ngu, trả lời sai."
>
> **Viết theo format:**
> Khi user [trigger], AI/product [failure], hậu quả là [impact].
> Lỗi thuộc layer [promise / intent / data-tool / safety / UX recovery].
> Nên sửa bằng [requirement / UX / fallback / human role / test case].

---

### Finding #1 — Miss Data ngầm trong luồng đối soát tự động

| | |
|---|---|
| **Trigger** | Khi user yêu cầu Trợ thủ AI Moni **đối soát chi tiêu tháng 05/2026**, hệ thống kích hoạt luồng đồng bộ giữa số dư ví thực tế và dữ liệu giao dịch. |
| **Failure** | Thuật toán quét **bỏ sót ngầm ~97.218đ** (giao dịch "Thanh toán Mua mã thẻ nạp Game – CTCP FUNTEK VIỆT NAM", 21:32, 04/05/2026) do giới hạn phân trang hiển thị hoặc phí ẩn không được đưa vào scope quét. **AI không phát hiện sai lệch và im lặng xuất báo cáo.** |
| **Impact** | User nhận báo cáo thiếu dữ liệu mà không biết. Khi tự phát hiện chênh lệch, UI không cung cấp bất kỳ công cụ nào để tra cứu hoặc kích hoạt quét lại — dẫn đến **bế tắc trải nghiệm và mất tin tưởng vào tính năng AI**. |
| **Layer lỗi** | `Data / Tool` · `Intent Detection` · `UX Recovery` |
| **Nên sửa** | **Audit Layer** + **Low-confidence Path** + **Fallback UI** — Cài đặt lớp kiểm toán số học ở cuối luồng: khi tổng số dư lệch so với danh sách quét, AI hiển thị cảnh báo thông minh kèm nút **"Quét Sâu Lại Nguồn" (Deep-scan Source)**. |

---

## 5. Sketch As-is / To-be

### Luồng Hiện Tại (As-Is) — Điểm Gãy

```
User gọi Trợ thủ AI Moni đối soát chi tiêu tháng 05/2026.
        ↓
Hệ thống kích hoạt luồng đối soát giữa tổng số dư ví thực tế và danh sách giao dịch.
        ↓
[PATH YẾU – FAILURE] Thuật toán quét bị gãy/miss mất ~97.218đ. AI không phát hiện, không cảnh báo.
        ↓
[DROP-OFF] AI hiển thị báo cáo thiếu thông tin. User bế tắc, không có tool để recover.
```

### Luồng Cải Tiến (To-Be) — Path Đã Vá

```
User gọi Trợ thủ AI Moni đối soát chi tiêu tháng 05/2026.
        ↓
Hệ thống chạy luồng đối soát chéo tự động.
        ↓
[AUDIT LAYER – KHẮC PHỤC] Phát hiện lệch số dư → AI hiển thị cảnh báo thông minh
+ nút "Quét Sâu Lại Nguồn". Low-confidence path được kích hoạt, hỏi lại user thay vì im lặng.
        ↓
[RECOVERY] Deep-scan vá dữ liệu bị miss, củng cố minh bạch luồng và giữ chân người dùng.
```

---

## 6. Tự Kiểm Trước Khi Nộp

- [x] Có **ít nhất 1 screenshot hoặc observation cụ thể** — 3 ảnh chụp màn hình MoMo (xem phần Evidence).
- [x] Có **đủ 4 paths**: Happy Path ✓ · Low-confidence ✓ (thiếu trigger) · Failure ✓ · Correction Path chưa có trong product — đã ghi nhận rõ ràng.
- [x] Finding được **viết thành product decision**, không chỉ là nhận xét — xem Finding #1 với format Trigger / Failure / Impact / Layer / Fix.
- [x] Sketch có **as-is và to-be**, đánh dấu điểm gãy và path đã sửa.
- [x] Có câu nói rõ finding này sẽ **đổi gì trong SPEC** — xem Product Decision bên dưới.

---

## Product Decision — Thay Đổi Trong SPEC

> "Thừa nhận hệ thống đã có tính năng đối soát nhưng thuật toán vẫn dính lỗ hổng làm 'miss' dữ liệu ngầm (~97k), chúng tôi quyết định **cài đặt Audit Layer** ở cuối luồng xử lý: khi phát hiện tổng số dư lệch so với danh sách quét, AI lập tức hiển thị cảnh báo thông minh kèm nút hành động **'Quét Sâu Lại Nguồn' (Deep-scan Source)** — vá điểm gãy trải nghiệm ngay tại chỗ thay vì im lặng xuất bảng thiếu thông tin."

---

## Evidence — Bằng Chứng Thực Tế

### Ảnh chụp màn hình từ Trợ thủ AI Moni – MoMo

**[Bước 1] AI Moni Xuất Danh Sách Giao Dịch Tháng 05/2026**
- AI trả về 5 giao dịch (300k, 1.279k, 1.161k, 619k, 200k) kèm bảng chi tiết.
- Moni hỏi *"Bạn muốn xem tiếp các giao dịch còn lại không?"* — hệ thống nhận biết còn dữ liệu chưa hiển thị.
- Tổng hiển thị: **3.560.148đ** — chỉ ghi nhận 5 giao dịch lớn nhìn thấy được.

**[Bước 2] Bảng Đối Soát Đầy Đủ – Vẫn Thiếu Khoản 97k**
- Bảng chi tiết liệt kê 5 giao dịch: Nhà cửa, Hóa đơn (×2), Giải trí, Chưa phân loại.
- Thuật toán phân loại hoạt động đúng nhưng **vẫn không xuất hiện giao dịch 97.000đ** bị miss.
- Lỗ hổng: Dữ liệu bị **cắt ngầm** trước khi AI xử lý — AI không biết và không cảnh báo.

**[Bằng Chứng] Giao Dịch Bị Miss: -97.000đ (CTCP FUNTEK VIỆT NAM)**
- Lịch sử giao dịch MoMo gốc ghi nhận rõ khoản **-97.000đ** — "Thanh toán Mua mã thẻ nạp Game" lúc 21:32, ngày 04/05/2026.
- Khoản này **hoàn toàn vắng mặt** trong báo cáo của AI Moni.
- Đây là **bằng chứng trực tiếp** xác nhận lỗ hổng miss dữ liệu ~97.218đ.

---

*Block 3 · UX Workshop – Mổ App AI Thật · Day 05 · Slide 32/53*
