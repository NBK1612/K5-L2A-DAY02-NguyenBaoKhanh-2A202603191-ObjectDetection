# Báo cáo cá nhân — Ngày 2 Object Detection

## 1. Thông tin học viên

- Họ tên: Nguyen Bao Khanh
- MSSV: 2A202603191
- Email: baokhanh16122003@gmail.com
- Lớp: K5 Level 2A
- Link repo bài làm: https://github.com/NBK1612/K5-L2A-DAY02-NguyenBaoKhanh-2A202603191-ObjectDetection
- Link Colab đã chạy: không có vì Colab bị lỗi, notebook được chạy trên máy cá nhân có GPU. Notebook đã chạy: [day2_annotation_to_yolo.ipynb](../../notebooks/day2_annotation_to_yolo.ipynb)

## 2. Schema và mini guideline

Xem [GUIDELINE_MINI.md](../../GUIDELINE_MINI.md). Ba ca mơ hồ đã quyết định:

1. drive_001: xe trắng vuông vức, chọn car vì xe có capo và không có thùng hàng.
2. Xe đỗ ngoài lòng đường: vẫn gán nhãn, giảng viên đã xác nhận.
3. drive_052 (eval): xe buýt hai tầng nhưng nhãn eval ghi là van. Theo schema đây là bus, giảng viên đã xác nhận.

Phần cần giảng viên làm rõ: thứ tự ID lớp trong gold có vẻ khác data.yaml (xem mục 4).

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

- Runtime, GPU/CPU, phiên bản Ultralytics và Torch: máy cá nhân, GPU RTX 3050 4GB, Python 3.12.1, Ultralytics 8.4.208, Torch 2.5.1 (CUDA 12.1).
- Các tham số thay đổi so với notebook nếu có và lý do: không thay đổi (yolo26n, 50 epoch, imgsz 640). Model train trên bộ nhãn ở commit 8e92029.
- Đường dẫn best checkpoint trong phiên chạy: yolo26_runs/vehicle_annotation/weights/best.pt (epoch 30), không nộp file .pt.

| Chỉ số trên 10 ảnh eval | Giá trị |
| ----------------------- | ------: |
| Precision               |   0.205 |
| Recall                  |   0.306 |
| mAP50                   |   0.302 |
| mAP50–95                |   0.216 |

Ảnh và số liệu: [ảnh dự đoán trên eval](assets/eval_predictions/visuals/), [confusion matrix](assets/yolo26_runs/vehicle_eval/confusion_matrix.png), [results.csv](assets/yolo26_runs/vehicle_annotation/results.csv), [metrics](assets/model_eval_metrics.json).

Ví dụ ảnh [drive_052](assets/eval_predictions/visuals/drive_052.jpg): model nhận đúng các xe con bên phải, nhưng bỏ sót chiếc xe buýt hai tầng ở giữa ảnh và vẽ thừa box cho dãy xe đỗ bên trái.

Trả lời bốn câu hỏi cuối notebook:

1. Lớp nào recall thấp nhất? Ảnh nào minh họa?
   Lớp bus, recall bằng 0 (van đứng sau với 0.083). Ảnh minh họa là drive_052, chiếc xe buýt hai tầng ở giữa ảnh không được phát hiện.
2. Nếu mAP50 cao nhưng mAP50–95 thấp, cần kiểm điều gì trên box?
   Cần kiểm box có ôm sát xe không: cạnh box có chạm đúng mép xe không, có lấy thừa nền hoặc bóng xe không, và các ảnh có vẽ cùng một kiểu không. Ví dụ lớp car có mAP50 là 0.78 nhưng mAP50–95 chỉ 0.54.
3. Lỗi class nào có thể liên quan guideline? Dữ kiện nào hỗ trợ nhận xét?
   Lỗi nhầm giữa bus, van và truck. Confusion matrix có 10 lần model đoán bus cho xe có nhãn van. Ảnh drive_052 có xe buýt hai tầng bị ghi là van. Khi so nhãn train với gold, ba lớp này lệch nhau theo vòng như đã nêu ở mục 4.
4. Vì sao không dùng 10 ảnh eval để train? Vai trò validation của chúng trong notebook là gì?
   Nếu train trên ảnh eval thì model đã thấy đáp án, điểm đánh giá sẽ cao hơn thực tế. Trong notebook, 10 ảnh eval được dùng làm validation để chọn best.pt và tính điểm cuối, nên chúng không phải là tập test độc lập.

## 6. Khó khăn và cách xử lý

| Bước/thời điểm | Lỗi quan sát                                                | Cách đã thử                                         | Kết quả/còn thiếu                          |
| -------------- | ----------------------------------------------------------- | --------------------------------------------------- | ------------------------------------------ |
| CP2            | Gán nhãn tay mất nhiều thời gian                            | Được giảng viên cho phép dùng nhãn nháp từ YOLO11x  | Nhãn nháp không có lớp van và có box trùng |
| CP4            | Chưa khóa nhãn trước khi nhận gold                          | Commit ngay, trước khi sửa nhãn                     | Commit 8e92029                             |
| CP5            | F1 là 0.563, thấp hơn 0.80                                  | Ghép box theo vị trí để tìm nguyên nhân             | Phát hiện gold bị lệch ID lớp, chưa rework |
| CP6            | Colab bị lỗi                                                | Chạy notebook trên máy cá nhân                      | Không có link Colab                        |
| CP6            | Thiếu thư viện yaml, Torch bản CPU, kết quả lưu sai thư mục | Cài lại môi trường, chép kết quả vào thư mục assets | Chạy được trên GPU                         |

## 7. Tự đánh giá theo rubric

Đối chiếu [RUBRIC.md](../../RUBRIC.md). Đã có kết quả: định dạng nhãn đạt checker, độ sát của box (mean IoU 0.85), guideline có ba ca mơ hồ, pipeline YOLO chạy được và không dùng eval để train. Còn thiếu: F1 chỉ 0.563, chưa rework, nhãn còn box trùng và còn sót xe. Model chỉ nhận tốt lớp car.

## 8. Công cụ AI và tài liệu tham khảo

Claude (Claude Code) được dùng để: hỏi khái niệm và cách thao tác, tạo nhãn nháp bằng YOLO11x (706 trên 768 box trong bản nộp giữ nguyên từ nhãn nháp), commit và push, chạy notebook trên máy. Nguồn ngoài repo: mô hình pretrained YOLO11x của Ultralytics, chỉ dùng để tạo nhãn nháp. Không dùng gold hay nguồn bên ngoài để tạo nhãn.
