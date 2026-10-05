# Thành viên nhóm và phân công công việc

## Thông tin nhóm

- Tên nhóm: Phronesis
- Chủ đề: Camera degradation health score
- Ngày cập nhật: 2026-10-05
- Báo cáo chung: [report.md](report.md)

## Danh sách thành viên

| STT | Họ và tên | Mã sinh viên | Vai trò | Phần việc phụ trách |
| --- | --- | --- | --- | --- |
| 1 | Nguyễn Thái Anh | 2A202602810 | Research, baseline | Nghiên cứu phương pháp; xác định thiết lập và mốc so sánh trên ảnh sạch |
| 2 | Nguyễn Văn Giáp | 2A202602903 | code_runner, chốt metrics | Chạy benchmark; kiểm tra, tổng hợp và chốt số liệu báo cáo |
| 3 | Trần Ngọc Khuyến | 2A202602682 | Research | Nghiên cứu các loại lỗi camera, mức độ và cách mô phỏng |
| 4 | Đoàn Quang Minh | 2A202602711] | Research | Nghiên cứu hạn chế, ảnh hưởng đến nhiệm vụ và hướng cải tiến/fallback |
| 5 | Vũ Tiến Linh | 2A202602657 | code_runner, failure case | Chạy phép thử theo cặp; tìm và kiểm chứng trường hợp mất phát hiện mục tiêu |

## Phân công và tiến độ

Các nhiệm vụ dưới đây là phân công; trạng thái và đóng góp thực tế cần được thành viên cập nhật. Không mặc định công việc đã hoàn thành chỉ vì tệp kết quả đã tồn tại.

Trạng thái sử dụng: Chưa bắt đầu / Đang làm / Cần hỗ trợ / Hoàn thành.

| Công việc | Người phụ trách | Người kiểm tra | Đầu ra cần bàn giao | Hạn hoàn thành | Trạng thái | Tệp liên quan / nơi lưu bằng chứng |
| --- | --- | --- | --- | --- | --- | --- |
| Nghiên cứu MUSIQ, YOLO và xác định baseline | Nguyễn Thái Anh | Nguyễn Văn Giáp | Ghi chú phương pháp; thiết lập ảnh sạch; định nghĩa metric và số baseline | 05/09/2026 | Hoàn Thành | [report.md](report.md), [summary.csv](summary.csv) |
| Nghiên cứu mờ, nhiễu, che khuất và các mức lỗi | Trần Ngọc Khuyến | Nguyễn Thái Anh | Bảng tham số, đơn vị, nguồn tham khảo; phân biệt lỗi mô phỏng và lỗi sensor thực tế | 05/09/2026 | Hoàn Thành | [config.json](config.json), [report.md](report.md) |
| Chuẩn bị dữ liệu, cấu hình và chạy baseline | Nguyễn Văn Giáp | Nguyễn Thái Anh | Danh sách ảnh; cấu hình tái lập; kết quả ảnh sạch và log chạy | 05/09/2026 | Hoàn Thành | [selected_images.json](selected_images.json), [config.json](config.json), [camera-health-kaggle.ipynb](camera-health-kaggle.ipynb) |
| Chạy benchmark mờ, nhiễu và che khuất | Nguyễn Văn Giáp | Vũ Tiến Linh | Kết quả từng ảnh và tổng hợp cho từng mức lỗi | 05/09/2026 | Hoàn Thành | [per_image_results.csv](per_image_results.csv), [summary.csv](summary.csv) |
| Chốt metrics và đối chiếu số liệu báo cáo | Nguyễn Văn Giáp | Nguyễn Thái Anh | Baseline, số khi lỗi, chênh lệch, đơn vị; bảng tương quan và kiểm tra xu hướng | 05/09/2026 | Hoàn Thành | [summary.csv](summary.csv), [correlations.csv](correlations.csv), [monotonicity.csv](monotonicity.csv), [report.md](report.md) |
| Chạy phép thử che khuất theo cặp và kiểm chứng failure case | Vũ Tiến Linh | Nguyễn Văn Giáp | Mã ảnh, lớp mục tiêu, biến thể nền/mục tiêu, trạng thái phát hiện, chênh lệch MUSIQ; ảnh hoặc log minh họa | 05/09/2026 | Hoàn Thành | [occlusion_pairs.csv](occlusion_pairs.csv), [camera-health-kaggle.ipynb](camera-health-kaggle.ipynb) |
| Phân tích hạn chế và ảnh hưởng đến nhiệm vụ | Đoàn Quang Minh | Trần Ngọc Khuyến | Hạn chế có nguồn; phân biệt kết quả đo trực tiếp với suy luận | 05/09/2026 | Hoàn Thành | [report.md](report.md), [Paper](https://openaccess.thecvf.com/content/ICCV2021/html/Ke_MUSIQ_Multi-Scale_Image_Quality_Transformer_ICCV_2021_paper.html), [Github Repository](https://github.com/chaofengc/IQA-PyTorch?utm_source=chatgpt.com) |
| Đề xuất cải tiến/fallback và cách kiểm chứng | Đoàn Quang Minh | Vũ Tiến Linh | Phương án cải tiến; thiết kế thử nghiệm và tiêu chí đánh giá | 05/09/2026 | Hoàn Thành | [report.md](report.md) |
| Tổng hợp nội dung nghiên cứu vào báo cáo | Nguyễn Thái Anh | Đoàn Quang Minh | Báo cáo có đủ 5 câu hỏi–bằng chứng; số liệu được Giáp đối chiếu và failure case được Linh kiểm chứng | 05/09/2026 | Hoàn Thành | [report.md](report.md) |

## Trách nhiệm theo câu hỏi của báo cáo

| Câu cần trả lời | Người phụ trách chính | Người phối hợp | Bằng chứng cần chuẩn bị |
| --- | --- | --- | --- |
| Sensor gặp lỗi gì, ở mức nào? | Trần Ngọc Khuyến | Nguyễn Văn Giáp | Tham số lỗi và nguồn nghiên cứu; cấu hình, mẫu ảnh hoặc log mô phỏng |
| Metric thay đổi ra sao? | Nguyễn Văn Giáp | Nguyễn Thái Anh | Baseline, số khi lỗi, chênh lệch và đơn vị |
| Thuật toán/tính năng bị ảnh hưởng thế nào? | Vũ Tiến Linh | Đoàn Quang Minh | Failure case đo trực tiếp; suy luận về tính năng được gắn nhãn |
| Phương pháp còn hạn chế ở đâu? | Đoàn Quang Minh | Trần Ngọc Khuyến, Nguyễn Thái Anh | Hạn chế từ paper/repo và thiết kế phép thử |
| Nên làm gì tiếp? | Đoàn Quang Minh | Vũ Tiến Linh, Nguyễn Văn Giáp | Cải tiến/fallback, cách chạy thử và tiêu chí kiểm chứng |

## Kiểm tra trước khi nộp

- [x] Thông tin và phần đóng góp của từng thành viên đã được điền.
- [x] Các phần việc hoàn thành có sản phẩm và liên kết bằng chứng.
- [x] Số liệu thống nhất với `report.md` và các tệp kết quả.
- [x] Metric có baseline, kết quả khi lỗi và đơn vị rõ ràng.
- [x] Kết quả đo trực tiếp, suy luận và đề xuất được phân biệt.
- [x] Hạn chế và cách kiểm chứng cải tiến được ghi rõ.
- [x] Nhóm đã rà soát báo cáo và thống nhất nội dung nộp.

## Báo cáo kết quả theo thành viên

Các báo cáo tổng hợp từ kết quả chạy chung; xem [danh mục báo cáo](reports/README.md).

| Thành viên | Báo cáo |
| --- | --- |
| Nguyễn Thái Anh | [Báo cáo kết quả](reports/2A202602810_NguyenThaiAnh.md) |
| Nguyễn Văn Giáp | [Báo cáo kết quả](reports/2A202602903_NguyenVanGiap.md) |
| Trần Ngọc Khuyến | [Báo cáo kết quả](reports/2A202602682_TranNgocKhuyen.md) |
| Đoàn Quang Minh | [Báo cáo kết quả](reports/2A202602711_DoanQuangMinh.md) |
| Vũ Tiến Linh | [Báo cáo kết quả](reports/2A202602657_VuTienLinh.md) |
