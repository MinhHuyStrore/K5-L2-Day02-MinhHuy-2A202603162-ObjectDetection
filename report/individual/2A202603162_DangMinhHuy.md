# Báo cáo cá nhân — Ngày 2 Object Detection

## 1. Thông tin học viên

- **Họ tên:** Đặng Minh Huy
- **MSSV:** 2A202603162
- **Email:** huyd26566@gmail.com
- **Lớp:** C204
- **Link repo bài làm:** https://github.com/MinhHuyStrore/K5-L2-Day02-MinhHuy-2A202603162-ObjectDetection.git
- **Link Colab đã chạy:** https://colab.research.google.com/github/MinhHuyStrore/K5-L2-Day02-MinhHuy-2A202603162-ObjectDetection/blob/main/notebooks/day2_annotation_to_yolo.ipynb#scrollTo=oozizbf9WAth

## 2. Schema và mini guideline

Chi tiết đầy đủ xem tại: [GUIDELINE_MINI.md](../../GUIDELINE_MINI.md)

Schema sử dụng format YOLO, mỗi box gồm `class_id x_center y_center width height`, tọa độ đã chuẩn hóa về 0–1. Box phải ôm sát vật thể, không lấy thừa nền, không cắt mất phần vật thể còn nhìn thấy rõ.

Ba ca mơ hồ đã quyết định khi gán nhãn:

1. **Truck và car:** Chỉ gán `truck` khi xe có thùng chở hàng / cabin tách biệt rõ với thùng, kích thước lớn hơn car rõ rệt. Xe bán tải nhỏ, xe con có cốp dài không gán thành truck.
2. **Bus và van / minivan:** Gán `bus` khi xe dài, nhiều cửa sổ liên tiếp, chở khách theo tuyến. Van / minivan ngắn hơn, ít cửa sổ hơn thì gán `van` hoặc `car` tùy schema, không gán thành bus.
3. **Vật bị che khuất / ở rìa ảnh:** Nếu vẫn nhận diện được vật thể qua phần còn lại, vẫn vẽ box theo phần nhìn thấy. Nếu vật quá mờ, chỉ còn vài pixel, không chắc chắn là vật gì thì không vẽ để tránh tạo FP.

Phần cần giảng viên làm rõ: ranh giới cụ thể giữa `truck` nhỏ và `van` lớn, và trường hợp xe bị che hơn 50% thì có tính là một box hay không.

## 3. Annotation và self-QC

- **Số file nhãn train:** 50 file, từ `drive_001.txt` đến `drive_050.txt`
- **Kết quả checker:** Chạy `tools/check_yolo_labels.py` đạt **50/50 file nhãn hợp lệ** về cấu trúc. Lỗi cấu trúc đã xử lý: chuẩn hóa lại định dạng YOLO, kiểm tra tọa độ nằm trong khoảng 0–1, loại bỏ dòng trống / dòng thừa.
- **Danh sách ít nhất 10 ảnh đã rà bằng mắt:** `drive_041`, `drive_027`, `drive_031`, `drive_038`, `drive_045`, `drive_007`, `drive_036`, `drive_037`, `drive_019`, `drive_026`
- **Lỗi bỏ sót / box / class phát hiện khi rà và cách sửa:**
  - **Bỏ sót (FN):** Hay sót vật nhỏ, vật ở xa, vật ở rìa ảnh và vật bị che một phần. Cách sửa: zoom ảnh khi rà, kiểm tra kỹ 4 góc và rìa ảnh trước khi chốt.
  - **Thừa box (FP):** Vẽ trùng 2 box cho 1 vật, vẽ box cho vật quá mờ hoặc không thuộc lớp cần gán. Cách sửa: chỉ giữ box chắc chắn, xóa box nghi ngờ.
  - **Sai class:** Nhầm nhiều nhất ở cặp `truck ↔ car` và `bus ↔ van`. Cách sửa: đối chiếu lại mini guideline, kiểm tra thùng xe, chiều dài xe và số cửa sổ trước khi gán.
  - **Box chưa sát:** Một số box còn lỏng, lấy thừa nền. Cách sửa: kéo sát mép vật thể, không lấy thừa bóng hoặc nền xung quanh.
- **Commit khóa nhãn ban đầu:** Commit `8fe76ed` ("Day02: nhan 50 anh train, ket qua danh gia, sua tool doc nhan de quy") trên repo GitHub, trước khi rework theo gold.

## 4. Gold QC và rework

Kết quả chi tiết xem tại: `outputs/annotation_evaluation.json`

| Chỉ số | Trước sửa | Sau sửa |
|---|---:|---:|
| TP | 403 | 516 |
| FP | 299 | 164 |
| FN | 250 | 137 |
| Precision | 0.5741 | 0.7588 |
| Recall | 0.6172 | 0.7902 |
| F1 | 0.5948 | 0.7742 |
| mean_iou_of_matches | 0.8584 | 0.8556 |
| passed | false | false |

**Rework đã thực hiện:**

Sau lần chấm đầu tiên, đã rà lại các ảnh lỗi nặng nhất. Kết quả cải thiện rõ: TP tăng từ 403 lên 516, FP giảm từ 299 xuống 164, FN giảm từ 250 xuống 137, F1 tăng từ 0.5948 lên 0.7742.

Các ảnh đã kiểm và quyết định:

- **drive_041:** FN = 14, FP = 9. Đây là ảnh lỗi nặng nhất. Đã kiểm lại toàn bộ ảnh, vẽ thêm box bị sót và xóa box thừa.
- **drive_027:** FN = 13, FP = 9. Đã kiểm lại, tập trung vào vật ở xa và vật ở rìa ảnh.
- **drive_031:** FN = 0, FP = 11. Không cần vẽ thêm, chỉ cần xóa 11 box thừa.
- **drive_038:** FN = 4, FP = 9. Chủ yếu xóa box thừa.
- **drive_045:** FN = 8, FP = 2. Chủ yếu vẽ thêm box bị sót.
- **drive_007, drive_036, drive_037:** Vừa có FP vừa có FN, đã rà lại và sửa theo từng box cụ thể.

**Kết luận:** Đã rework và cải thiện đáng kể, nhưng **chưa đạt**. F1 sau sửa là 0.7742, vẫn dưới yêu cầu 0.8. Mean IoU đạt 0.8556, vượt yêu cầu 0.7, nên lỗi chính còn lại là thiếu box và thừa box, không phải box vẽ lỏng. Không suy nguyên nhân chỉ từ FP/FN, đã đối chiếu thêm với ảnh thực tế và confusion matrix ở mục 5.

## 5. Kết quả YOLO

- **Runtime:** Google Colab, chạy bằng GPU
- **Phiên bản Ultralytics và Torch:** Theo log trong notebook Colab đã chạy
- **Các tham số thay đổi so với notebook nếu có và lý do:** Không thay đổi; giữ cấu hình mặc định của notebook (model `yolo26n.pt`, `epochs: 50`, `imgsz: 640` — xem `assets/yolo26_runs/vehicle_annotation/args.yaml`) để kết quả so sánh được nhất quán.
- **Đường dẫn best checkpoint trong phiên chạy:** `/content/Day2-Lab/outputs/yolo26_runs/vehicle_annotation/weights/best.pt`

| Chỉ số trên 10 ảnh eval | Giá trị |
|---|---:|
| Precision | 0.4212 |
| Recall | 0.5406 |
| mAP50 | 0.5049 |
| mAP50–95 | 0.3143 |

Ảnh dự đoán và log chạy xem tại:

- `assets/eval_predictions/`
- `assets/yolo26_runs/`

### Trả lời bốn câu hỏi cuối notebook

**1. Lớp nào recall thấp nhất? Ảnh nào minh họa?**

Lớp **truck** có recall thấp nhất, chỉ **0.20**. Phần lớn truck bị model nhận nhầm thành **car**, số còn lại bị bỏ sót hẳn. Minh họa ở `assets/eval_predictions/visuals/` trong các ảnh `drive_051–drive_060`, tìm ảnh có xe tải bị gán nhãn `car` hoặc không có box nào.

**2. Nếu mAP50 cao nhưng mAP50–95 thấp, cần kiểm điều gì trên box?**

mAP50 = **0.50** nhưng mAP50–95 chỉ **0.31**, chênh lệch khá lớn. Nghĩa là ở ngưỡng IoU 0.5 model vẫn nhận diện được vật, nhưng khi yêu cầu IoU cao hơn như 0.75 hay 0.9 thì rớt nhiều. Vấn đề nằm ở **độ sát của box**: box dự đoán còn lỏng, lệch tâm hoặc sai tỉ lệ. Cần kiểm lại chất lượng box ở tập train, vì box train lỏng thì model sẽ học ra box lỏng. Điều này khớp với lỗi annotation ở mục 4.

**3. Lỗi class nào có thể liên quan guideline? Dữ kiện nào hỗ trợ nhận xét?**

Có, chủ yếu là 2 cặp nhầm: **truck ↔ car** và **bus ↔ truck/van**. Đây đúng là ca mơ hồ mà guideline đã cảnh báo, như trường hợp van/minivan dễ nhầm với bus hoặc car. Dữ kiện hỗ trợ là confusion matrix trong kết quả eval, khớp với lỗi ở phần rework: **truck bị gán nhầm thành car: 18 box; bus ↔ van: 51 box**. Nói cách khác, model đang học lại đúng lỗi nhầm có sẵn trong nhãn train.

**4. Vì sao không dùng 10 ảnh eval để train? Vai trò validation của chúng trong notebook là gì?**

Vì 10 ảnh eval đóng vai trò **validation**, dùng để chọn checkpoint tốt nhất và đo mAP. Nếu đưa chúng vào train, model đã “thấy trước” ảnh eval nên mAP đo được sẽ cao một cách lạc quan, không phản ánh đúng khả năng tổng quát. Ngoài ra 10 ảnh là quá ít so với 50 ảnh train nên cũng không cải thiện được bao nhiêu. Trong notebook, eval chỉ dùng để chọn `best.pt`, không phải test set độc lập.

## 6. Khó khăn và cách xử lý

| Bước / thời điểm | Lỗi quan sát | Cách đã thử | Kết quả / còn thiếu |
|---|---|---|---|
| Annotation ban đầu | Sót nhiều vật nhỏ, vật ở rìa ảnh | Rà lại bằng mắt, zoom ảnh khi kiểm | Giảm FN nhưng vẫn còn sót ở drive_041 và drive_027 |
| Annotation ban đầu | Vẽ thừa box, vẽ trùng 1 vật thành 2 box | Đối chiếu lại từng box với ảnh gốc | Giảm FP từ 299 xuống 164 |
| Annotation ban đầu | Nhầm class truck ↔ car, bus ↔ van | Đối chiếu lại mini guideline, kiểm tra thùng xe và chiều dài xe | Giảm nhầm lẫn nhưng vẫn còn lỗi trong confusion matrix |
| Gold QC lần 1 | F1 chỉ 0.5948, chưa đạt | Rà lại các ảnh lỗi nặng nhất theo bảng per-image | F1 tăng lên 0.7742 |
| Gold QC sau rework | F1 0.7742, vẫn chưa đạt 0.8 | Xác định cần sửa tiếp drive_041, drive_027, drive_031 | Còn thiếu khoảng 25–30 box sửa đúng nữa là đủ qua |
| Train YOLO | mAP50–95 thấp hơn nhiều so với mAP50 | Kiểm lại box train, nhận thấy box còn lỏng | Đã xác định nguyên nhân, chưa train lại sau khi sửa hết nhãn |
| Train YOLO | Recall lớp truck rất thấp, chỉ 0.20 | Đối chiếu confusion matrix với lỗi annotation | Xác định model học lại lỗi nhầm từ nhãn train |

## 7. Tự đánh giá theo rubric

Đối chiếu [RUBRIC.md](../../RUBRIC.md):

- **Schema và mini guideline:** Đã có. Đã viết guideline, đã nêu 3 ca mơ hồ và phần cần giảng viên làm rõ.
- **Annotation và self-QC:** Đã có. Đủ 50 file nhãn, checker đạt 50/50 về cấu trúc, đã rà ít nhất 10 ảnh bằng mắt.
- **Gold QC và rework:** Đã có, nhưng **chưa đạt**. F1 sau rework là 0.7742, chưa đạt yêu cầu 0.8. Mean IoU đã đạt.
- **Kết quả YOLO:** Đã có. Đã chạy train, đã có best checkpoint, đã có đủ 4 chỉ số Precision, Recall, mAP50, mAP50–95 trên 10 ảnh eval và đã trả lời 4 câu hỏi cuối notebook.
- **Báo cáo và assets:** Đã dẫn link repo, link Colab, file `outputs/annotation_evaluation.json`, folder `assets/eval_predictions/` và `assets/yolo26_runs/`.
- **Phần còn thiếu:** Cần rework thêm nhãn để F1 vượt 0.8, sau đó train lại YOLO để kiểm tra mAP có cải thiện hay không.

Không tự nhận hoàn thành bước chưa chạy. Phần rework F1 vẫn ghi rõ là chưa đạt.

## 8. Công cụ AI và tài liệu tham khảo

- **Công cụ AI đã dùng:** Meta AI. Dùng ở bước phân tích kết quả Gold QC, tính toán FP/FN theo từng ảnh, ưu tiên thứ tự sửa nhãn, và hỗ trợ viết lại câu trả lời ở mục 5 cho ngắn gọn, rõ nghĩa hơn.
- **Phần đã tự kiểm tra:** Tất cả số liệu trong báo cáo đều lấy từ file `outputs/annotation_evaluation.json` và log Colab đã chạy. Các ảnh và lỗi nêu trong báo cáo đều đã đối chiếu lại với ảnh thực tế và kết quả checker.
- **Nguồn ngoài repo được sử dụng:** Tài liệu Ultralytics YOLO, tài liệu hướng dẫn trong repo của khóa học, mini guideline tự viết trong repo.
- Không đính kèm gold train, không ghi mật khẩu hoặc token trong báo cáo.
