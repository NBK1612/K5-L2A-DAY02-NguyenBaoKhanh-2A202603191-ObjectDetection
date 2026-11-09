# Báo cáo cá nhân — Ngày 2 Object Detection

## 1. Thông tin học viên

- Họ tên: Nguyen Bao Khanh
- MSSV: 2A202603191
- Email: baokhanh16122003@gmail.com
- Lớp: K5 Level 2A
- Link repo bài làm: https://github.com/NBK1612/K5-L2A-DAY02-NguyenBaoKhanh-2A202603191-ObjectDetection
- Link Colab đã chạy:

## 2. Schema và mini guideline

Xem [GUIDELINE_MINI.md](../../GUIDELINE_MINI.md). Ba ca mơ hồ đã quyết định:

1. drive_001: xe trắng vuông vức, chọn car vì xe có capo và không có thùng hàng.
2. Xe đỗ ngoài lòng đường: vẫn gán nhãn, giảng viên đã xác nhận.
3. drive_052 (eval): xe buýt hai tầng nhưng nhãn eval ghi là van. Theo schema đây là bus. Model train trên Colab cũng dự đoán xe này là bus.

Phần cần giảng viên làm rõ: thứ tự ID lớp trong gold và eval có vẻ khác data.yaml (xem mục 4 và 5).

## 3. Annotation và self-QC

- Số file nhãn train: 50
- Kết quả checker và lỗi đã xử lý: checker báo đạt 50/50, không có lỗi định dạng.
- Danh sách ít nhất 10 ảnh đã rà bằng mắt: drive_001, 008, 018, 019, 024, 027, 028, 030, 031, 033, 038, 041, 042, 043, 049.
- Lỗi bỏ sót/box/class phát hiện khi rà và cách sửa: có 16 cặp box trùng trên cùng một xe do nhãn nháp, và một số xe van vẫn mang nhãn car hoặc truck. Các lỗi này chưa được sửa.
- Commit khóa nhãn ban đầu: 8e92029, tạo sau khi đã nhận gold nhưng trước khi sửa nhãn.

## 4. Gold QC và rework

| Chỉ số              | Trước sửa | Sau sửa nếu có |
| ------------------- | --------: | -------------: |
| TP                  |       400 |                |
| FP                  |       368 |                |
| FN                  |       253 |                |
| Precision           |    0.5208 |                |
| Recall              |    0.6126 |                |
| F1                  |    0.5630 |                |
| mean_iou_of_matches |    0.8546 |                |
| passed              |     false |                |

Kết quả: [annotation_evaluation.json](../../outputs/annotation_evaluation.json). Không rework.

Nguyên nhân F1 thấp, khi ghép box đã gán với gold theo vị trí (IoU từ 0.5 trở lên):

- Lớp trong gold bị lệch theo quy luật: xe gán bus thì gold ghi van (51 box), gán van thì gold ghi truck (31 box), gán truck thì gold ghi bus (18 box). Có thể gold dùng thứ tự ID lớp khác với data.yaml. Xem lại 86 box bus đã gán thì 84 box đúng là xe buýt.
- Nếu không xét lớp thì F1 khoảng 0.72. Phần còn lại là do gold không gán xe đỗ và xe ở xa (255 box), và do bỏ sót xe ở các ảnh đông xe (140 box).

## 5. Kết quả YOLO

- Runtime, GPU/CPU, phiên bản Ultralytics và Torch: Google Colab có GPU, Ultralytics bản mới nhất do notebook tự cài, Torch có sẵn trên Colab. Train 50 epoch mất khoảng 83 giây.
- Các tham số thay đổi so với notebook nếu có và lý do: không thay đổi (yolo26n, 50 epoch, imgsz 640, batch tự động, seed 20260909). Model train trên bộ nhãn khóa, giống commit 8e92029.
- Đường dẫn best checkpoint trong phiên chạy: /content/Day2-Lab/outputs/yolo26_runs/vehicle_annotation/weights/best.pt, không nộp file .pt.

| Chỉ số trên 10 ảnh eval | Giá trị |
| ----------------------- | ------: |
| Precision               |   0.410 |
| Recall                  |   0.230 |
| mAP50                   |   0.272 |
| mAP50–95                |   0.202 |

Theo từng lớp: car có recall 0.636 và mAP50 0.739, truck có recall 0.200 và mAP50 0.239, bus có recall 0 và mAP50 0.008, van có recall 0.083 và mAP50 0.103.

Ảnh và số liệu: [ảnh dự đoán trên eval](assets/eval_predictions/visuals/), [confusion matrix](assets/yolo26_runs/vehicle_eval/confusion_matrix.png), [results.csv](assets/yolo26_runs/vehicle_annotation/results.csv), [metrics](assets/model_eval_metrics.json).

Ví dụ ảnh [drive_052](assets/eval_predictions/visuals/drive_052.jpg): model nhận đúng các xe con bên phải và dự đoán chiếc xe buýt hai tầng ở giữa ảnh là bus (độ tin cậy 0.50). Theo schema dự đoán này đúng, nhưng nhãn eval ghi xe này là van nên vẫn bị tính là sai. Model cũng vẽ box cho dãy xe đỗ bên trái mà nhãn eval không có.

Trả lời bốn câu hỏi cuối notebook:

1. Lớp nào recall thấp nhất? Ảnh nào minh họa?
   Lớp bus, recall bằng 0 trên 2 vật thể, sau đó là van với 0.083. Ảnh minh họa là drive_052: model đoán chiếc xe buýt hai tầng là bus, nhưng nhãn eval ghi van, nên xe này bị tính là bỏ sót van.
2. Nếu mAP50 cao nhưng mAP50–95 thấp, cần kiểm điều gì trên box?
   Cần kiểm box có ôm sát xe không: cạnh box có chạm đúng mép xe không, có lấy thừa nền hoặc bóng xe không, và các ảnh có vẽ cùng một kiểu không. Ví dụ lớp car có mAP50 là 0.739 nhưng mAP50–95 chỉ 0.523.
3. Lỗi class nào có thể liên quan guideline? Dữ kiện nào hỗ trợ nhận xét?
   Lỗi nhầm giữa bus, van và truck. Confusion matrix có 11 lần model đoán bus cho xe có nhãn van. Ảnh drive_052 có xe buýt hai tầng bị ghi là van. Khi so nhãn train với gold, ba lớp này lệch nhau theo vòng như đã nêu ở mục 4.
4. Vì sao không dùng 10 ảnh eval để train? Vai trò validation của chúng trong notebook là gì?
   Nếu train trên ảnh eval thì model đã thấy đáp án, điểm đánh giá sẽ cao hơn thực tế. Trong notebook, 10 ảnh eval được dùng làm validation để chọn best.pt và tính điểm cuối, nên chúng không phải là tập test độc lập.

## 6. Khó khăn và cách xử lý

| Bước/thời điểm | Lỗi quan sát                       | Cách đã thử                                                    | Kết quả/còn thiếu                          |
| -------------- | ---------------------------------- | -------------------------------------------------------------- | ------------------------------------------ |
| CP2            | Gán nhãn tay mất nhiều thời gian   | Được giảng viên cho phép dùng nhãn nháp từ YOLO11x             | Nhãn nháp không có lớp van và có box trùng |
| CP4            | Chưa khóa nhãn trước khi nhận gold | Commit ngay, trước khi sửa nhãn                                | Commit 8e92029                             |
| CP5            | F1 là 0.563, thấp hơn 0.80         | Ghép box theo vị trí để tìm nguyên nhân                        | Phát hiện gold bị lệch ID lớp, chưa rework |
| CP6            | Colab bị lỗi lúc đầu               | Chạy tạm notebook trên máy cá nhân, sau đó chạy lại trên Colab | Kết quả nộp lấy từ lần chạy trên Colab     |

## 7. Tự đánh giá theo rubric

Đối chiếu [RUBRIC.md](../../RUBRIC.md). Đã có kết quả: định dạng nhãn đạt checker, độ sát của box (mean IoU 0.85), guideline có ba ca mơ hồ, pipeline YOLO chạy được trên Colab và không dùng eval để train. Còn thiếu: F1 chỉ 0.563, chưa rework, nhãn còn box trùng và còn sót xe. Model chỉ nhận tốt lớp car.

## 8. Công cụ AI và tài liệu tham khảo

Claude (Claude Code) được dùng để: hỏi khái niệm và cách thao tác, tạo nhãn nháp bằng YOLO11x (706 trên 768 box trong bản nộp giữ nguyên từ nhãn nháp), commit và push, chạy notebook trên máy khi Colab lỗi, và chạy lại đánh giá file best.pt từ Colab để lấy số liệu theo từng lớp. Nguồn ngoài repo: mô hình pretrained YOLO11x của Ultralytics, chỉ dùng để tạo nhãn nháp. Không dùng gold hay nguồn bên ngoài để tạo nhãn.
