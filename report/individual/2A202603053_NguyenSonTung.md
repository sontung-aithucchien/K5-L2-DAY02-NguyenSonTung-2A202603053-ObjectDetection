# Báo cáo cá nhân — Ngày 2 Object Detection

Đổi tên file theo `MSSV_HoTen.md`, ví dụ `20241234_NguyenVanAn.md`. Điền kết quả thật, xóa dòng hướng dẫn khi nộp. Xem [quy định nộp](../../SUBMISSION.md).

## 1. Thông tin học viên

- Họ tên: Nguyễn Sơn Tùng
- MSSV: 2A202603053
- Email: 26ai.tungns2@vinuni.edu.vn
- Lớp: C401
- Link repo bài làm: https://github.com/sontung-aithucchien/K5-L2-DAY02-NguyenSonTung-2A202603053-ObjectDetection
- Link Colab đã chạy: https://colab.research.google.com/github/sontung-aithucchien/K5-L2-DAY02-NguyenSonTung-2A202603053-ObjectDetection/blob/main/notebooks/day2_annotation_to_yolo.ipynb

## 2. Schema và mini guideline

Dẫn tới [GUIDELINE_MINI.md](../../GUIDELINE_MINI.md). Tóm tắt ba ca mơ hồ đã quyết định, cách áp dụng schema và phần cần giảng viên làm rõ.

## 3. Annotation và self-QC

- Số file nhãn train:
- Kết quả checker và lỗi đã xử lý:
- Danh sách ít nhất 10 ảnh đã rà bằng mắt:
- Lỗi bỏ sót/box/class phát hiện khi rà và cách sửa:
- Commit khóa nhãn ban đầu:

## 4. Gold QC và rework

| Chỉ số | Trước sửa | Sau sửa nếu có |
|---|---:|---:|
| TP | 452 | |
| FP | 375 | |
| FN | 201 | |
| Precision | 0.5466 | |
| Recall | 0.6922 | |
| F1 | 0.6108 | |
| mean_iou_of_matches | 0.8602 | |
| passed | false | |

Dẫn tới `outputs/annotation_evaluation.json` trong repo. Nếu rework, ghi ảnh, lỗi đã kiểm, quyết định sửa/giữ và lý do. Không suy nguyên nhân chỉ từ FP/FN. Không có rework thì ghi rõ.

## 5. Kết quả YOLO

- Runtime, GPU/CPU, phiên bản Ultralytics và Torch: Google Colab (`/content/Day2-Lab`); CPU AMD Ryzen 7 6800H with Radeon Graphics (3.20 GHz, `device: cpu`); Ultralytics 8.4.208, Torch 2.14.1.
- Các tham số thay đổi so với notebook nếu có và lý do: Không đổi `epochs=50`, `imgsz=640`, `batch=-1`; notebook tự chọn CPU cho phiên chạy này. Các giá trị đối chiếu trong [args.yaml](assets/yolo26_runs/vehicle_annotation/args.yaml).
- Đường dẫn best checkpoint trong phiên chạy: [best.pt](assets/yolo26_runs/vehicle_annotation/weights/best.pt) (`outputs/yolo26_runs/vehicle_annotation/weights/best.pt` trong Colab).

| Chỉ số trên 10 ảnh eval | Giá trị |
|---|---:|
| Precision | 0.45844 |
| Recall | 0.23839 |
| mAP50 | 0.27764 |
| mAP50–95 | 0.20818 |

Đặt cả 2 folder `assets/eval_predictions/` và `assets/yolo26_runs/` (giải nén từ `day2_colab_outputs.zip`) cạnh báo cáo và dẫn link tương đối. Không chỉ ghi số; chỉ ra ít nhất một ảnh và vật thể cụ thể.

Trả lời bốn câu hỏi cuối notebook:

1. **`bus` có recall thấp nhất**: theo [đường cong recall-confidence](assets/yolo26_runs/vehicle_eval/BoxR_curve.png), recall của lớp này giảm về 0 sớm hơn các lớp còn lại; PR curve cũng cho AP thấp nhất (`0.018`). [drive_051.jpg](assets/eval_predictions/visuals/drive_051.jpg) minh họa một bus có trong nhãn eval nhưng không có dự đoán `bus` tương ứng. Lớp `van` cũng yếu (AP `0.077`).
2. Nếu mAP50 cao nhưng mAP50–95 thấp, model tìm được vật thể nhưng box chưa đủ khít ở các ngưỡng IoU cao. Kiểm tra box có ôm sát phần thân xe nhìn thấy không, có lấy thừa nền/lấn sang xe khác không, và cách xử lý xe bị che khuất hoặc cắt ở mép ảnh theo guideline.
3. Có dấu hiệu cần rà lại ranh giới `van`/`bus`: guideline định nghĩa `bus` là xe khách/xe buýt cỡ lớn, còn `van` là xe thân hộp nhỏ hơn (minivan 7–16 chỗ). Trong [drive_053.jpg](assets/eval_predictions/visuals/drive_053.jpg), một xe khách lớn được dự đoán là `bus`; nhãn tương ứng trong [drive_053.txt](../../dataset/labels/eval/drive_053.txt) lại là `van`. Confusion matrix cũng ghi nhận nhiều nhãn `van` bị dự đoán thành `bus` ([confusion_matrix.png](assets/yolo26_runs/vehicle_eval/confusion_matrix.png)). Đây là dấu hiệu để rà lại theo guideline, không đủ để tự kết luận nhãn sai vì nhầm lẫn của model cũng có thể là nguyên nhân.
4. Không dùng 10 ảnh eval để train để tránh rò rỉ dữ liệu và giữ được dữ liệu ngoài tập train để đánh giá. Theo notebook, 10 ảnh này được dùng làm validation trong quá trình train, theo dõi metric và chọn `best.pt`; vì vậy chúng không phải test set độc lập.

## 6. Khó khăn và cách xử lý

| Bước/thời điểm | Lỗi quan sát | Cách đã thử | Kết quả/còn thiếu |
|---|---|---|---|
| | | | |

## 7. Tự đánh giá theo rubric

Đối chiếu [RUBRIC.md](../../RUBRIC.md): phần nào đã có kết quả, phần nào còn thiếu hoặc chưa kiểm được. Không tự nhận hoàn thành bước chưa chạy.

## 8. Công cụ AI và tài liệu tham khảo

Ghi công cụ đã dùng, dùng ở bước nào và phần đã tự kiểm tra; không dùng thì ghi rõ. Liệt kê nguồn ngoài repo được sử dụng. Không ghi mật khẩu/token hoặc đính kèm gold train.
