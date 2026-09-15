# Mini annotation guideline — Ngày 3 (tracking)

> Điền file này **trong lúc gán nhãn**, không phải sau khi xong. Mỗi lần bạn dừng
> lại nghĩ "cái này tính sao nhỉ?" thì đó là một dòng phải ghi vào đây.
>
> Đây là tài liệu mà người gán nhãn tiếp theo sẽ đọc để làm giống bạn. Nếu hai
> người trong nhóm gán khác nhau, gần như luôn là vì file này chưa nói rõ — chứ
> không phải vì ai kém.

Nhóm / tên: `Phạm Anh Huy`
Clip: `clip_01`, `clip_02`

---

## 1. Phạm vi: gán cái gì, không gán cái gì

Một lớp duy nhất: **`vehicle`** — xe bốn bánh (xe con, van, xe buýt, xe tải).

| Gán | Không gán |
| --- | --- |
| xe con, SUV, taxi, xe bán tải | người đi bộ |
| van, minivan | xe đạp |
| xe buýt, minibus | **xe máy / mô tô** |
| xe tải, xe đầu kéo | xe trong ảnh quảng cáo, trong gương, dưới bóng nước |

Bổ sung của nhóm (nếu có): `...`

## 2. Luật ID — phần quan trọng nhất

| Tình huống | Luật của nhóm | Vì sao |
| --- | --- | --- |
| Xe bị che một phần rồi hiện lại | giữ nguyên ID nếu bị che dưới 25 frame | `Chiếc xe chắc chắn vẫn là chiếc xe cũ` |
| Xe bị che lâu hơn ngưỡng trên | `Tạo track mới` | `Xe có thể là một chiếc xe khác hoặc người lái khác` |
| Xe rời khung hình rồi quay lại | `Tạo track mới` | `Xe có thể là một chiếc xe khác nhưng giống kiểu dáng` |
| Hai xe cắt nhau / chồng lên nhau | `Vẫn đánh nhãn và track từng xe` | `Mỗi cá thể xe vẫn cần được track` |

## 3. Luật bbox

| Tình huống | Luật của nhóm |
| --- | --- |
| Xe bị cắt bởi rìa ảnh | bbox chạm đúng rìa, không đoán phần ngoài ảnh |
| Xe bị xe khác che một phần | bbox ôm phần **nhìn thấy được** |
| Xe vừa xuất hiện, còn rất nhỏ / rất mờ | bắt đầu track từ frame đầu tiên xác định được là xe bốn bánh; ngưỡng nhóm chọn: `50%` |
| Xe đang đỗ, không di chuyển | Tạo track cho xe đang đỗ |
| Keyframe đặt dày ở đâu | Khoảng khắc nhiều xe giao nhau hoặc tiến vào hoặc ra khỏi khung hình |

## 4. Ít nhất ba ca mơ hồ đã gặp thật

Ghi **frame cụ thể** và **ID cụ thể**, không ghi chung chung.

### Ca 1
- Clip / frame / ID: `Clip_01 / frame 76`
- Tình huống: `Chiến xe bị khuất tầm nhìn hoàn toàn ở sau chiếc xe bus`
- Quyết định: `Vẫn track chiếc xe đó`
- Lý do: `Một phần của chiếc xe vẫn bị lòi ra`

### Ca 2
- Clip / frame / ID: `Clip_01 / frame 15`
- Tình huống: `Chiếc xe đi ngang qua cột biển báo giao thông`
- Quyết định: `Tiếp tục track chiếc xe`
- Lý do: `Phần che của xe không nhiều nên giữ nguyên`

### Ca 3
- Clip / frame / ID: `Clip_01 / frame 134`
- Tình huống: `Chiếc xe đang đỗ bị che`
- Quyết định: `Đánh dấu occluded`
- Lý do: `Phần chiếc xe bị che không nhiều nhưng vẫn đánh dấu để biết là có chiếc xe khác đã che`

## 5. Sửa gì sau khi chấm với gold và sau khi kiểm chéo

Luật nào trong file này hoá ra còn thiếu hoặc còn mơ hồ? Viết lại cho rõ:

- `...`
- `...`
