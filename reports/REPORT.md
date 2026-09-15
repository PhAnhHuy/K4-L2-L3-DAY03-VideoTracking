# Báo cáo Ngày 3 — Tracking Annotation

Chép file này thành `reports/REPORT.md` rồi điền. Giữ nguyên các tiêu đề.

Họ tên / nhóm: `Phạm Anh Huy`
Ngày: `15/9/2026`

---

## 1. Quá trình gán nhãn

| Mục | Giá trị |
| --- | --- |
| Công cụ | CVAT |
| Thời gian gán `clip_02` (warm-up) | `15` phút |
| Thời gian gán `clip_01` | `40` phút |
| Số track đã vẽ trong `clip_01` | `8` |
| Số keyframe trung bình mỗi track | `...` |

Ba tình huống khó nhất khi gán clip này, và bạn xử lý thế nào:

1. `clip_01: Chiến xe bị khuất tầm nhìn hoàn toàn ở sau chiếc xe bus frame 76, mình đã tuân theo guideline chỉ đánh dấu những chỗ chiếc xe xuất hiện`
2. `clip_01: Chiếc xe đi ngang qua cột biển báo giao thông vào frame 15, phần che của xe không nhiều nên giữ nguyên và tiếp tục track chiếc xe`
3. `clip_01: Chiếc xe đang đỗ bị che vào frame 134, mình đánh dấu occluded`

## 2. Tự kiểm và kiểm chéo

Ba lượt tua bắt được gì (lượt 1 nhìn ID, lượt 2 frame đầu/cuối, lượt 3 frame giữa):

- Lượt 1: `...`
- Lượt 2: `...`
- Lượt 3: `...`

Kiểm chéo với: `...`. Chi tiết ở `reports/review_partner.md`.
Số lỗi bạn tìm được trong bản của bạn ấy: `...`. Số lỗi bạn ấy tìm được trong bản của bạn: `...`.

Ca nào hai người quyết khác nhau, và luật nào còn thiếu trong `GUIDELINE_MINI.md`?

`...`

## 3. Pre-gold lock và chấm trước/sau rework

| Evidence | Giá trị |
| --- | --- |
| SHA-256 từ `evidence/pre-gold/clip_01/manifest.json` | `7a5d0d00be9f00fd4ff9d3b0f64e130001709624c1ae4f178f072d4a26d21d44` |
| Thời điểm khóa | `11:15` |
| Số row / frame / track trước khi mở reference | `579 / 190 / 8` |

| | HOTA | DetA | AssA | LocA | IDF1 | MOTA | MOTP | FP | FN | IDSW |
| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Bản pre-gold |0.797|0.779|0.818|0.888|0.941|0.881|0.878|37|31|0|
| Sau rework |0.797|0.779|0.818|0.888|0.941|0.881|0.878|37|31|0|

Qua cổng (`IDF1 >= 0.80`, `MOTA >= 0.75`, `MOTP >= 0.70`): có

Sau khi đọc danh sách lỗi, bạn đã sửa cụ thể những gì? Ghi theo frame và ID:

| Loại lỗi | Frame | ID | Đã sửa thế nào |
| --- | --- | --- | --- |
| Thiếu đánh dấu xe | 109 | clip_01 | Đánh dấu thêm phần xe tải bị thiếu |

## 4. Kết quả model: ByteTrack control vs ReID treatment

Cấu hình từ `outputs/model_run_config.json`:

| Mục | Giá trị |
| --- | --- |
| Python / ultralytics / torch / lap | `3.13.15 / 8.4.145 / 2.11.0+cu128 / 0.5.13` |
| weights / hai tracker | `yolo26n.pt / bytetrack.yaml và /content/Day3-Lab/configs/trackers/botsort-reid.yaml` |
| conf / IoU / imgsz / classes | `0.25 / 0.7 / 960 / [2, 5, 7]` |
| device | `0` |

| So sánh | HOTA | DetA | AssA | LocA | IDF1 | MOTA | MOTP | FP | FN | IDSW |
| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| bạn vs gold |0.797|0.779|0.818|0.888|0.941|0.881|0.878|37|31|0|
| ByteTrack control vs gold |0.709|0.649|0.776|0.846|0.875|0.749|0.823|88|54|2|
| BoT-SORT + ReID vs gold |0.763|0.711|0.820|0.872|0.900|0.792|0.860|91|26|2|
| ReID vs bạn |0.735|0.677|0.800|0.903|0.851|0.689|0.897|119|60|1|

## 5. Phân tích — năm câu hỏi

**1. MOTA của bạn cao hơn hay thấp hơn IDF1? Nếu MOTA cao mà IDF1 thấp thì điều đó nói gì, và vì sao MOTA không phạt nặng lỗi ID?**

`MOTA 0.881 thấp hơn IDF1 0.941. Nếu MOTA cao nhưng IDF1 thấp, mô hình có thể phát hiện được phần lớn vật thể nhưng duy trì danh tính không tốt, chẳng hạn đổi ID hoặc làm đứt track. MOTA chủ yếu dựa trên FP, FN và IDSW; mỗi lần đổi ID chỉ được tính như một lỗi IDSW nên không phản ánh đầy đủ thời gian một vật thể bị gán sai danh tính.`

**2. ByteTrack control và BoT-SORT + ReID treatment khác nhau thế nào ở IDF1, AssA và IDSW? Dẫn một frame sequence để giải thích treatment tốt hơn, tệ hơn hoặc không đổi đáng kể. Nhắc rõ đây không cô lập causal effect của ReID vì hai tracker implementation khác.**

`BoT-SORT + ReID có IDF1 tăng từ 0.875 lên 0.900 và AssA tăng từ 0.776 lên 0.820 so với ByteTrack. Điều này cho thấy treatment liên kết danh tính tốt hơn trên toàn chuỗi nhưng chưa làm giảm số lần đổi ID. Có thể kiểm tra chuỗi frame quanh frame 134, nơi chiếc xe đang đỗ bị che khuất.`

**3. DetA, FP và FN đổi thế nào? Lỗi còn lại là detector hay association?**

`Từ ByteTrack sang BoT-SORT + ReID, DetA tăng từ 0.649 lên 0.711. FP tăng nhẹ từ 88 lên 91, tức tăng 3 lỗi, nhưng FN giảm mạnh từ 54 xuống 26, tức giảm 28 lỗi. Như vậy, treatment phát hiện được nhiều vật thể hơn nhưng đồng thời tạo thêm một số dự đoán dư. Lỗi còn lại đến từ cả detector và association. FP và FN là lỗi phát hiện, còn 2 IDSW cùng AssA chưa đạt 1 cho thấy vẫn tồn tại lỗi liên kết danh tính. Xét theo số lượng, lỗi detector, đặc biệt là FP, vẫn chiếm phần đáng kể.`

**4. Một chỗ bạn đúng và ReID sai (frame, ID, vì sao):**

`Ở frame 109 clip_01, ReID đánh dấu cái cửa hàng phía sau là một chiếc xe đang đỗ trong khi nó không phải xe`

**5. Một chỗ ReID làm bạn xem lại annotation (frame, ID, vì sao), hoặc lý do evidence cho thấy model sai:**

`Ở frame 108 clip_01, ReID phát hiện chiếc xe tải sớm hơn mình phát hiện. Trường hợp này cho thấy dự đoán của model có thể được dùng như một tín hiệu để rà soát, nhưng không được mặc định là ground truth; quyết định sửa chỉ được thực hiện sau khi kiểm tra trực tiếp frame gốc và xác nhận vật thể thực sự tồn tại`

## 6. Nếu phải gán thêm 10 clip nữa

Bạn sẽ sửa gì trong `GUIDELINE_MINI.md`, và đổi gì trong quy trình làm việc của mình?

`...`

## 7. Tệp đã nộp

- [x] `annotations/clip_01/gt.txt`
- [x] `annotations/clip_02/gt.txt`
- [x] `evidence/pre-gold/clip_01/gt.txt` và `manifest.json`
- [x] `GUIDELINE_MINI.md` đã điền
- [x] `outputs/eval_vs_gold.json`
- [x] `outputs/model_bytetrack_clip_01.txt`
- [x] `outputs/model_reid_clip_01.txt`
- [x] `outputs/model_run_config.json`
- [x] `outputs/eval_bytetrack_vs_gold.json`, `outputs/eval_reid_vs_gold.json`, `outputs/eval_reid_vs_me.json`
- [x] `reports/review_partner.md`
- [x] `reports/REPORT.md` (file này)
