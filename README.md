# Lab Day 19 — Camera degradation health score

Lab của nhóm **Phronesis** khảo sát câu hỏi: điểm chất lượng ảnh MUSIQ và độ sắc nét có phản ánh mức suy giảm khả năng phát hiện vật thể khi ảnh bị mờ, nhiễu hoặc che khuất không?

Notebook chỉ chạy suy luận với mô hình đã huấn luyện, không huấn luyện lại. Thí nghiệm sử dụng COCO128, bộ phát hiện YOLO11n và MUSIQ qua `pyiqa`.

## Nội dung lab

1. Chọn ảnh có nhãn và đo mốc so sánh trên ảnh sạch.
2. Tạo ba loại lỗi tổng hợp, mỗi loại có ba mức độ.
3. Đo chất lượng ảnh và kết quả phát hiện trên từng biến thể.
4. Tổng hợp recall, tỷ lệ giữ lại phát hiện đúng, tương quan Spearman và xu hướng theo mức lỗi.
5. So sánh che nền với che mục tiêu bằng mảng che cùng kích thước để tìm trường hợp điểm toàn ảnh ít thay đổi nhưng mất phát hiện mục tiêu.

| Lỗi | Mức 1 | Mức 2 | Mức 3 |
| --- | --- | --- | --- |
| Mờ Gaussian | σ = 1 pixel | σ = 2 pixel | σ = 4 pixel |
| Nhiễu Gaussian | σ = 5 | σ = 15 | σ = 30 |
| Che khuất bằng mảng xám | 5% diện tích | 15% diện tích | 30% diện tích |

σ của nhiễu tính trên thang cường độ pixel 0–255. Diện tích che là tỷ lệ dự kiến; tỷ lệ thực tế sau làm tròn được lưu trong kết quả từng ảnh.

## Chạy trên Kaggle

1. Import [camera-health-kaggle.ipynb](camera-health-kaggle.ipynb) vào Kaggle Notebook.
2. Bật GPU T4/P100 và Internet trong Settings. Lần chạy đầu cần tải thư viện, COCO128 và trọng số mô hình.
3. Trong cell cấu hình, đặt `MODE = 'smoke'` để kiểm tra môi trường với 5 ảnh, sau đó chọn **Run All**.
4. Khi kiểm tra thành công, đổi `MODE = 'benchmark'` và chạy lại từ cell cấu hình đến hết để đánh giá 50 ảnh, tương ứng 500 lượt đánh giá.
5. Tải tệp ZIP qua liên kết ở cell cuối. Mỗi lần chạy tạo thư mục riêng `/kaggle/working/camera_health_<mode>_<run_id>/`.

Cell cấu hình hiện lưu `MODE = 'benchmark'`; cần đổi sang `smoke` nếu muốn kiểm tra nhanh trước. Cell cài đặt tự cài `ultralytics`, `pyiqa`, `scipy`, `pandas`, `matplotlib` và `tqdm`, sử dụng bộ PyTorch/torchvision có sẵn trên Kaggle.

### Cấu hình chính

| Biến | Giá trị hiện tại | Ý nghĩa |
| --- | --- | --- |
| `SEED` | `42` | Seed chọn ảnh và tạo lỗi |
| `YOLO_WEIGHTS` | `'yolo11n.pt'` | Trọng số detection với 80 lớp COCO |
| `USE_MUSIQ` | `True` | Bật đo MUSIQ |
| `CONF_THRESHOLD` | `0.25` | Ngưỡng độ tin cậy dự đoán |
| `MATCH_IOU` | `0.50` | Ngưỡng ghép dự đoán với nhãn chuẩn |
| `NMS_IOU` | `0.70` | Ngưỡng NMS của detector |
| `YOLO_IMGSZ` | `640` | Kích thước đầu vào YOLO |
| `MAX_IMAGE_SIDE` | `960` | Giới hạn cạnh dài trước khi tạo lỗi và đo |
| `FAILURE_SEARCH_IMAGES` | `10` | Số ảnh tối đa để tìm cặp che khuất |
| `SAVE_CORRUPTED_IMAGES` | `True` | Lưu ảnh biến thể PNG trong thư mục `images/` |

Nếu không có Internet, chuẩn bị sẵn thư viện, dữ liệu và trọng số; đặt `DATASET_ROOT`, `YOLO_WEIGHTS`, `MUSIQ_WEIGHTS` tương ứng. `DATASET_ROOT` phải chứa `images/train2017/` và `labels/train2017/`. Nếu không tải được MUSIQ, có thể đặt `USE_MUSIQ = False` rồi chạy lại từ cấu hình; kết quả khi đó chỉ có chỉ số độ sắc nét.

Notebook cũng hỗ trợ chạy bằng Jupyter trên máy cá nhân có PyTorch/torchvision tương thích; khi không có thư mục `/kaggle`, đầu ra được lưu trong `camera_work/` tại thư mục làm việc. CPU được hỗ trợ nhưng có thể chạy chậm.

## Tệp trong repository

| Tệp | Nội dung |
| --- | --- |
| [camera-health-kaggle.ipynb](camera-health-kaggle.ipynb) | Toàn bộ mã cấu hình, kiểm tra, benchmark và xuất kết quả |
| [report.md](report.md) | Báo cáo phân tích chung, hạn chế và đề xuất cải tiến |
| [TEAMMATES.md](TEAMMATES.md) | Thành viên và phân công công việc |
| [reports/README.md](reports/README.md) | Danh mục báo cáo theo thành viên |
| [config.json](config.json), [selected_images.json](selected_images.json) | Cấu hình và danh sách ảnh của lần chạy đã lưu |
| [versions.json](versions.json), [pip-freeze.txt](pip-freeze.txt) | Phiên bản thư viện trong môi trường đã chạy |
| [per_image_results.csv](per_image_results.csv) | Chỉ số từng ảnh và từng điều kiện |
| [summary.csv](summary.csv) | Kết quả tổng hợp theo loại lỗi và mức độ |
| [correlations.csv](correlations.csv) | Tương quan giữa chất lượng ảnh với recall/tỷ lệ giữ lại |
| [monotonicity.csv](monotonicity.csv) | Tỷ lệ bước tăng mức lỗi mà điểm chất lượng không tăng |
| [occlusion_pairs.csv](occlusion_pairs.csv) | Kết quả phép thử che nền và che mục tiêu |
| [quality_vs_severity.png](quality_vs_severity.png) | Chất lượng ảnh theo mức lỗi |
| [recall_vs_severity.png](recall_vs_severity.png) | Recall theo mức lỗi |
| [quality_vs_recall.png](quality_vs_recall.png) | Quan hệ giữa điểm chất lượng và recall |
| [failure_case.png](failure_case.png) | So sánh ảnh sạch, che nền và che mục tiêu |
| `failure_clean.png`, `failure_background.png`, `failure_target.png` | Các ảnh riêng của phép thử che khuất |

Các tệp ở thư mục gốc là kết quả đã lưu. `config.json` ghi lại lần chạy, không phải tệp mà notebook đọc để cấu hình; muốn chạy lại cần sửa các biến trong cell cấu hình.

## Chỉ số và kết quả đã lưu

- **Micro recall:** tổng số phát hiện đúng chia tổng số vật thể có nhãn; ghép một–một, cùng lớp và IoU ≥ 0,5.
- **Retention:** tỷ lệ vật thể vẫn được phát hiện đúng khi có lỗi trong số vật thể được phát hiện đúng trên ảnh sạch.
- **MUSIQ:** điểm chất lượng cảm nhận do mô hình trả về.
- **Độ sắc nét:** phương sai Laplacian của ảnh xám; nhiễu có thể làm chỉ số này tăng.

Lần benchmark đã lưu sử dụng 50 ảnh gốc, 500 lượt đánh giá và bật MUSIQ. Số liệu dưới đây lấy từ [báo cáo chung](report.md) và [bảng tổng hợp](summary.csv).

| Điều kiện | Micro recall | Retention | MUSIQ trung bình |
| --- | ---: | ---: | ---: |
| Ảnh sạch | 0,5061 | 1,0000 | 69,958 |
| Mờ, σ = 4 pixel | 0,2482 | 0,4808 | 17,683 |
| Nhiễu, σ = 30 | 0,2749 | 0,5385 | 46,454 |
| Che khuất, tỷ lệ dự kiến 30% | 0,2920 | 0,5288 | 65,499 |

Trong phép thử theo cặp với ảnh `000000000472.jpg`, máy bay vẫn được phát hiện khi che nền nhưng mất phát hiện khi che mục tiêu; chênh lệch MUSIQ giữa hai biến thể là 0,725 điểm.

![Phép thử che nền và che mục tiêu](failure_case.png)

## Giới hạn khi diễn giải

Đây là benchmark thăm dò với lỗi tổng hợp, một detector và tập dữ liệu nhỏ. COCO128 thuộc tập huấn luyện nên có thể trùng dữ liệu mà detector đã thấy. Nhãn gốc được giữ cả khi vật thể bị che; các biến thể của cùng ảnh không độc lập. MUSIQ và độ sắc nét chưa được hiệu chuẩn thành điểm sức khỏe camera vật lý hoặc xác suất phát hiện đúng.

Xem [report.md](report.md) để đọc phân tích đầy đủ và hướng kiểm chứng điểm chất lượng theo vùng trên tập dữ liệu độc lập.
