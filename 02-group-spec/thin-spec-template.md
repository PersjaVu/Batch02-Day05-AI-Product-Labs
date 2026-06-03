# Template — Thin SPEC Cuối Day 05

Thin SPEC không phải PRD đầy đủ. Đây là bản cam kết đủ rõ để sáng Day 06 nhóm build ngay.

## 1. Track, product/app và user

**Track:**  
**Product/app thật:**  
**User cụ thể:**  
**Nhóm có phải user thật không? Nếu không, khác ở đâu?**  

## 2. Evidence summary (Vũ Duy Bảo - 2A202600565)

| Evidence | Nguồn | User/pain nói lên điều gì? | SPEC phải đổi gì? |
|---|---|---|---|
| Nhập "hay mệt mỏi, đôi khi đau đầu" → AI liệt kê 7 nhóm nguyên nhân, không gợi ý được 1 khoa cụ thể, hỏi chung "tìm bác sĩ phù hợp không?" | Self-test BookingCare AI (Screenshot 2) | User không ra được quyết định chọn khoa sau khi đọc xong — đây là cốt lõi của pain: AI tồn tại nhưng không giúp user hành động được. | **Đổi build slice**: bỏ flow "gợi ý chuyên khoa từ triệu chứng rõ" (BookingCare đã làm tốt); thay bằng **conversational low-confidence path** — hỏi thêm 1–2 câu để thu hẹp thay vì dump thông tin. |
| Nhập "đau ngực, khó thở, tay trái tê" → AI cảnh báo "đến cấp cứu ngay" nhưng ngay sau vẫn hỏi "bạn có muốn đặt lịch Tim mạch không?" | Self-test BookingCare AI (Screenshot 3) | User có thể bỏ qua dấu hiệu cấp cứu vì chatbot vẫn offer đặt lịch thường — mâu thuẫn logic trực tiếp gây nguy hiểm tính mạng. | **Đổi failure mode**: failure mode nguy hiểm nhất không phải "AI gợi sai khoa" mà là "AI offer đặt lịch thường khi triệu chứng là red-flag" → phải block đặt lịch + chỉ hiển thị cảnh báo cấp cứu. |
| Form Vinmec yêu cầu user tự chọn Bệnh viện → Chuyên khoa → Bác sĩ trước khi thấy slot; nhập "Tôi đau đầu lắm sắp ngất rồi" → không có triage, không cảnh báo, xử lý như booking thường. | Self-test Vinmec.com (vinmec1.png, vinmec2.png) | Pain là systemic, không chỉ một app — cả BookingCare lẫn Vinmec đều không giải quyết được bước "ra quyết định y tế an toàn từ triệu chứng". | **Đổi pain statement**: pain không còn là "UI đặt lịch phức tạp" mà là "không có ai/gì giúp user biết nên làm gì tiếp theo khi nhập triệu chứng". |
| "Em đăng ký đóng tiền xong tới bệnh viện người ta bắt bốc số lại nè." | App Store Customer Reviews | On-to-Off gap: lịch hẹn online không được bệnh viện công nhận tại quầy. | Không thuộc 3 thay đổi SPEC → **Backlog**. |
| "Có thím nào đặt hẹn khám bệnh qua web Bookingcare.vn chưa… Liệu có uy tín lắm không?" | Diễn đàn VOZ — Thread #87515 | Digital Trust gap: người dùng mới lo lịch hẹn không được cơ sở y tế xác nhận chính thức. | Không thuộc 3 thay đổi SPEC → **Backlog**. |
| August AI: 47% fewer questions, 95.8% accuracy; Symptomate/Ada Health: tách rõ 3 care level, không offer đặt lịch khi output là cấp cứu. | Competitor research (arxiv.org/pdf/2412.12538, Symptomate, Ada Health) | Có validated solution pattern — conversational triage và red-flag block đã được quốc tế chứng minh, feasible trong 1 ngày với 1 LLM call. | **Đổi build slice**: adopt pattern này thay vì tự thiết kế — thu hẹp còn 2 gap (low-conf + red-flag), không làm lại happy path. |

## 3. Pain statement (Vũ Duy Bảo - 2A202600565)


Người dùng lần đầu nhập triệu chứng vào chatbot đặt lịch khám online
đang gặp khó ở bước ra quyết định: nên đi cấp cứu ngay hay đặt lịch khám chuyên khoa nào,
vì AI hiện tại (BookingCare) hoặc dump tràn lan 7 nhóm nguyên nhân không dẫn đến quyết định
(triệu chứng mơ hồ), hoặc mâu thuẫn logic nguy hiểm — vừa cảnh báo cấp cứu vừa offer
đặt lịch thường trong cùng 1 response (triệu chứng nguy cấp);
dẫn tới user bị hoang mang, không biết hành động tiếp theo là gì,
hoặc tệ hơn — bỏ qua dấu hiệu cấp cứu vì chatbot đã offer đặt lịch thường.
Bằng chứng chính là:
  • Screenshot 2: nhập "hay mệt mỏi, đôi khi đau đầu" → AI trả về 7 nhóm nguyên nhân,
    không gợi ý được 1 khoa cụ thể.
  • Screenshot 3: nhập "đau ngực, khó thở, tay trái tê" → AI cảnh báo "đến cấp cứu ngay"
    nhưng ngay sau vẫn hỏi "bạn có muốn đặt lịch Tim mạch không?".
  • Vinmec: nhập "Tôi đau đầu lắm sắp ngất rồi" → không triage, không cảnh báo,
    xử lý như booking thường.


## 4. Build slice

```text
Cho [user] đang [task/workflow],
prototype sẽ dùng AI để [augment/automate hành động hẹp],
tạo ra [output],
và xử lý [failure mode] bằng [mitigation].
```

## 5. Auto/Aug decision

Chọn một:

- [ ] **Augmentation:** AI gợi ý/draft/phân loại, user quyết cuối.
- [ ] **Conditional automation:** AI tự làm trong case hẹp; case mơ hồ/rủi ro chuyển người.
- [ ] **Automation:** AI tự quyết và tự hành động.

**Lý do chọn:**  
**Human role:** reviewer / decider / trainer / rescuer / none  

## 6. Four paths

| Path | Prototype phải thể hiện gì? |
|---|---|
| Happy |  |
| Low-confidence |  |
| Failure |  |
| Correction |  |

## 7. Failure mode nguy hiểm nhất

```text
Nếu user [trigger],
AI có thể [failure],
hậu quả là [impact].
Prototype sẽ xử lý bằng [ask again / show source / human review / undo / fallback].
Owner kiểm thử path này là [tên thành viên].
```

## 8. Owner plan cho sáng Day 06

| Thành viên | Việc phụ trách | Bằng chứng cần có trong repo |
|---|---|---|
|  | Research / evidence |  |
|  | SPEC |  |
|  | Prototype |  |
|  | Test / failure path |  |
|  | Demo script / repo |  |
