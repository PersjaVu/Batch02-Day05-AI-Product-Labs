# Toolkit — Từ Evidence Đến Build Slice

Dùng sau khi nhóm đã có evidence. Mục tiêu là chốt một build slice đủ nhỏ cho Day 06.

## 1. Gom evidence thành cụm (Vũ Duy Bảo - 2A202600565)

Gom theo **workflow/pain**, không gom theo tên feature.

- **Cụm 1: Bối rối trước triệu chứng mơ hồ, chatbot/form đẩy gánh nặng chẩn đoán sơ bộ cho người dùng**
  - *BookingCare AI:* Nhập "hay mệt mỏi, đôi khi đau đầu" → AI liệt kê tràn lan 7 nhóm nguyên nhân thay vì đặt câu hỏi thu hẹp chuyên khoa, khiến user không biết chọn gì.
  - *Vinmec:* Bắt buộc user tự chọn Bệnh viện → Chuyên khoa → Bác sĩ trước khi thấy slot, không gợi ý triệu chứng (burden chẩn đoán đè lên user).
- **Cụm 2: Thiếu cảnh báo an toàn y tế và mâu thuẫn logic khi triệu chứng khẩn cấp (Red Flag handling)**
  - *BookingCare AI:* Nhập "đau ngực, khó thở, tay trái tê" → Vừa cảnh báo "đi cấp cứu ngay" vừa chào mời đặt lịch Tim mạch thường trong cùng một phản hồi.
  - *Vinmec:* Nhập "tôi đau đầu sắp ngất rồi" nhưng không có triage, không cảnh báo khẩn cấp, xử lý như booking thường.
- **Cụm 3: Đứt gãy kết nối On-to-Off (Hệ thống online và offline không đồng bộ)**
  - *App Store Review:* Đăng ký đóng tiền xong đến bệnh viện vẫn bắt bốc số lại, mã đặt chỗ bị vô hiệu hóa tại quầy.
  - *BookingCare Fanpage:* Phòng khám đổi lịch bác sĩ đột xuất nhưng tổng đài không báo trước, tới nơi mới ngã ngửa làm mất công xin nghỉ làm.
  - *Vinmec:* Sau khi submit form đầy đủ, hệ thống hiện thông báo đợi tổng đài viên gọi điện xác nhận lại thời gian khám.
- **Cụm 4: Rào cản lòng tin kỹ thuật số (Digital Trust) & Trải nghiệm kỹ thuật kém**
  - *VOZ:* Người dùng nghi ngờ độ uy tín của bên thứ 3 (BookingCare.vn), lo ngại lịch hẹn không được cơ sở y tế xác nhận chính thức.
  - *Google Play:* App lỗi hiệu năng trên thiết bị Android cũ, không load nổi form chọn ngày tháng.

## 2. Viết insight (Vũ Duy Bảo - 2A202600565)

Người bệnh có triệu chứng bất thường không chỉ cần [một chatbot AI gợi ý nhanh chuyên khoa hoặc trả lời thông tin y khoa chung chung].
Họ thật ra cần [sự định hướng y khoa an toàn và phân tầng nguy cơ chính xác để được hỗ trợ ra quyết định phù hợp (biết rõ nên đi cấp cứu ngay hay nên khám chuyên khoa nào) mà không bị hoang mang hay gặp rủi ro ảnh hưởng đến tính mạng],
vì [evidence thực tế cho thấy chatbot hiện tại bị mâu thuẫn logic nghiêm trọng khi xử lý ca cấp cứu (vừa khuyên đi cấp cứu vừa chào mời đặt lịch thường) và gây bối rối bằng cách đổ tràn lan 7 chuyên khoa khi triệu chứng mơ hồ thay vì tương tác hỏi thêm để thu hẹp].


## 3. Viết opportunity

```text
Cơ hội là dùng AI để phân tầng nguy cơ triệu chứng (triage) theo 3 mức —
red flag / low-confidence / clear — rồi route user đúng hành động:
  • red flag   → block đặt lịch thường + cảnh báo đến cơ sở y tế/cấp cứu ngay
  • low-conf   → hỏi thêm 1–2 câu để thu hẹp chuyên khoa (thay vì dump thông tin)
  • clear      → gợi ý chuyên khoa cụ thể + offer đặt lịch

Giúp user ra được quyết định y tế rõ ràng trong 1 lần nhập triệu chứng,
không bị hoang mang và không gặp mâu thuẫn logic nguy hiểm như hiện tại,

trong khi vẫn kiểm soát nguy cơ AI bỏ sót red flag hoặc gợi sai chuyên khoa
bằng disclaimer bắt buộc và fallback "gặp bác sĩ để xác nhận" ở mọi output.
```

## 4. Chọn build slice

Build slice tốt phải qua 5 câu hỏi:

| Câu hỏi | Đạt khi | Nhóm |
|---|---|---|
| User cụ thể chưa? | Nói được ai dùng, trong bối cảnh nào. | ✅ Người dùng lần đầu nhập triệu chứng vào chatbot trước khi đặt lịch online — chưa biết cần khám khoa nào hoặc đang có dấu hiệu khẩn cấp chưa nhận ra. |
| Task đủ hẹp chưa? | Demo được trong 3-5 phút. | ✅ Demo 4 path: (1) **Happy** — nhập "đau mắt đỏ, chảy nước mắt 2 ngày" → AI gợi ý Chuyên khoa Mắt → hiện slot → user đặt lịch được; (2) **Low-confidence** — nhập "hay mệt mỏi, đôi khi đau đầu" → AI hỏi thêm 1 câu → thu hẹp còn 1 chuyên khoa → offer đặt lịch; (3) **Failure** — AI gợi ý sai chuyên khoa → user biết sai → có thể nhập lại triệu chứng hoặc tự chọn chuyên khoa khác; (4) **Correction** — user bổ sung/sửa triệu chứng sau khi đã nhập → input mới chạy lại triage pipeline → AI cập nhật gợi ý chuyên khoa. |
| AI decision rõ chưa? | AI gợi ý/tự làm một việc cụ thể. | ✅ AI classify triệu chứng ra 1 trong 3 mức (clear / low-confidence / red-flag) và tự động chọn action: gợi ý chuyên khoa + offer đặt lịch / hỏi thêm 1 câu / block đặt lịch + cảnh báo cấp cứu. |
| Failure path rõ chưa? | Có một case AI không chắc hoặc sai để test. | ✅ **Failure**: AI gợi ý Nội tổng quát nhưng user biết mình cần Thần kinh — user nhập lại hoặc override bằng tay, flow quay về triage; **Correction**: user nhập "đau bụng" rồi bổ sung "kèm sốt 38.5°" → sửa input → AI re-triage với đủ triệu chứng → chuyên khoa được gợi ý có thể đổi. |
| Có evidence không? | Có bằng chứng từ self-use/review/user/competitor. | ✅ 3 case self-test BookingCare AI (Screenshot 1–3) + 2 case Vinmec + App Store/Play review + VOZ forum + BookingCare Fanpage review. |

## 5. Quyết định: giữ, giảm scope, hay đổi hướng?

| Tình huống | Quyết định |
|---|---|
| Evidence yếu, user mơ hồ | Dừng build sâu; quay lại research 20 phút. |
| Ý tưởng quá rộng | Giữ domain, cắt xuống một flow. |
| AI không cần thiết | Dùng rule/manual prototype; ghi rõ vì sao không dùng AI sâu. |
| Rủi ro cao | Chọn augmentation hoặc conditional automation. |
| Không demo được trong 1 ngày | Đưa phần lớn vào backlog, giữ một path nhỏ. |

## 6. Câu chốt cuối

Điền câu này trước khi rời lớp:

```text
Dựa trên [evidence],
nhóm sẽ build [prototype slice],
cho [user],
để giải quyết [pain],
bằng cách AI [augment/automate task],
và sẽ test failure path [failure mode].
```

## 7. Backlog

Những thứ **không build trong Day 06**:

- 
- 
- 
