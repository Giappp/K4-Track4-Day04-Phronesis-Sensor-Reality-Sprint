# Báo cáo thử nghiệm suy giảm chất lượng ảnh camera

## Thiết lập thử nghiệm

- Chế độ chạy: `benchmark`; 50 ảnh gốc và 500 lượt đánh giá (50 ảnh sạch, 450 ảnh có lỗi mô phỏng).
- Bộ phát hiện vật thể: `yolo11n.pt`; ngưỡng độ tin cậy 0,25; ngưỡng IoU để ghép dự đoán với nhãn chuẩn 0,5.
- MUSIQ được bật. MUSIQ và độ sắc nét là chỉ số đại diện cho chất lượng ảnh, chưa được hiệu chuẩn để đo tình trạng vật lý của sensor.
- Recall gộp trên ảnh sạch: 0,5061, tương ứng phát hiện đúng 208/411 vật thể có nhãn.
- Cấu hình và bằng chứng phép thử: [config.json](config.json), [notebook thực nghiệm](camera-health-kaggle.ipynb), [kết quả từng ảnh](per_image_results.csv), [kết quả tổng hợp](summary.csv).

## Câu hỏi cần trả lời và bằng chứng cần đưa ra

| Câu cần trả lời | Bằng chứng nhóm đưa ra |
| --- | --- |
| Sensor gặp lỗi gì, ở mức nào? | Tham số lỗi, mẫu/clip, ảnh hoặc log |
| Metric thay đổi ra sao? | Số baseline, số khi lỗi, đơn vị |
| Thuật toán/tính năng bị ảnh hưởng thế nào? | Kết quả đo trực tiếp hoặc suy luận được gắn nhãn |
| Phương pháp còn hạn chế ở đâu? | Limitation từ paper/repo hoặc từ phép thử lớp học |
| Nên làm gì tiếp? | Cải tiến/fallback và cách kiểm chứng |

### 1. Loại lỗi và mức độ

Thử nghiệm tạo lỗi trên ảnh đầu vào; chưa chứng minh sensor vật lý bị hỏng. Mỗi loại lỗi có ba mức tăng dần:

| Lỗi mô phỏng | Mức 1 | Mức 2 | Mức 3 | Đơn vị/ý nghĩa tham số |
| --- | ---: | ---: | ---: | --- |
| Mờ Gaussian | 1 | 2 | 4 | Độ lệch chuẩn σ, tính theo pixel trên ảnh đưa vào phép tạo lỗi |
| Nhiễu Gaussian | 5 | 15 | 30 | Độ lệch chuẩn σ của nhiễu theo mức cường độ pixel 0–255 |
| Che khuất bằng mảng xám | 0,05 | 0,15 | 0,30 | Tỷ lệ diện tích ảnh dự kiến bị che: 5%, 15%, 30% |

Tham số từng mẫu và tỷ lệ che khuất thực tế sau làm tròn kích thước được lưu trong `per_image_results.csv`. Mã tạo lỗi nằm trong notebook; đây là bằng chứng tái lập phép thử.

### 2. Thay đổi các chỉ số ở mức lỗi nặng nhất

| Điều kiện | Recall gộp | Giảm so với ảnh sạch (điểm phần trăm) | Tỷ lệ giữ lại phát hiện đúng | MUSIQ trung bình | Độ sắc nét trung bình |
| --- | ---: | ---: | ---: | ---: | ---: |
| Ảnh sạch — mốc so sánh | 0,5061 | 0,00 | 1,0000 | 69,958 | 2395,019 |
| Mờ, σ = 4 pixel | 0,2482 | 25,79 | 0,4808 | 17,683 | 2,341 |
| Nhiễu, σ = 30 mức cường độ | 0,2749 | 23,11 | 0,5385 | 46,454 | 9330,933 |
| Che khuất, tỷ lệ dự kiến 30% | 0,2920 | 21,41 | 0,5288 | 65,499 | 1630,729 |

Recall gộp là tổng số vật thể phát hiện đúng chia tổng số vật thể có nhãn. Tỷ lệ giữ lại là số vật thể vẫn được phát hiện đúng khi có lỗi chia số vật thể được phát hiện đúng trên ảnh sạch. Hai tỷ lệ này không có đơn vị. MUSIQ được báo cáo theo điểm đầu ra của mô hình; độ sắc nét là phương sai Laplacian trên ảnh xám, theo bình phương mức cường độ với toán tử được dùng trong notebook, không phải đơn vị vật lý của sensor. Bảng dùng số liệu từ `summary.csv`; mức giảm được tính từ số chưa làm tròn.

### 3. Ảnh hưởng đến thuật toán và tính năng

**Kết quả đo trực tiếp:** ở mức lỗi nặng nhất, recall của bộ phát hiện giảm trong cả ba loại lỗi. Nhiễu làm phương sai Laplacian tăng dù recall giảm; vì vậy độ sắc nét cao trong phép đo này không đủ để kết luận ảnh tốt cho phát hiện vật thể.

**Phép thử che khuất theo cặp:** với ảnh `000000000472.jpg`, vật thể mục tiêu là máy bay. Khi mảng che nằm ở nền, máy bay vẫn được phát hiện; khi mảng che nằm trên mục tiêu, máy bay không được phát hiện. Chênh lệch MUSIQ giữa hai biến thể là 0,725 điểm; trường hợp này được đánh dấu là ứng viên thất bại trong [occlusion_pairs.csv](occlusion_pairs.csv).

**Suy luận từ phép thử:** điểm chất lượng toàn ảnh có thể thay đổi ít trong khi khả năng phát hiện một mục tiêu quan trọng bị mất. Chưa đo trực tiếp tác động đến theo dõi vật thể, lập kế hoạch hay các tính năng phía sau bộ phát hiện; không xem các tác động đó là kết quả đã được xác nhận.

## Tương quan thăm dò

Các hệ số ρ dưới đây được báo cáo trong [correlations.csv](correlations.csv). Mỗi nhóm dùng nhiều phiên bản lỗi của cùng ảnh gốc, nên các mẫu không độc lập.

| Nhóm | MUSIQ với recall | MUSIQ với tỷ lệ giữ lại | Độ sắc nét với recall | Độ sắc nét với tỷ lệ giữ lại | Số mẫu |
| --- | ---: | ---: | ---: | ---: | ---: |
| Tất cả ảnh có lỗi | 0,078 | 0,131 | −0,105 | −0,081 | 450 |
| Mờ | 0,359 | 0,487 | 0,223 | 0,393 | 150 |
| Nhiễu | 0,243 | 0,430 | −0,299 | −0,308 | 150 |
| Che khuất | 0,038 | 0,209 | −0,231 | −0,106 | 150 |

## Các điểm cần kiểm tra khi diễn giải

- Với từng loại lỗi, điểm chất lượng có giảm khi mức lỗi tăng không? [monotonicity.csv](monotonicity.csv) ghi nhận tỷ lệ bước MUSIQ không tăng là 100% với mờ, 98,67% với nhiễu và 80% với che khuất.
- Nhiễu có làm phương sai Laplacian tăng dù chất lượng phát hiện giảm không? Kết quả ở bảng tổng hợp cho thấy hiện tượng này.
- Chênh lệch MUSIQ nhỏ có đồng thời xuất hiện với việc mất phát hiện mục tiêu trong phép thử theo cặp không? Trường hợp máy bay nêu trên là một ví dụ đo được.
- Nếu lần chạy khác không tìm được ứng viên thất bại, cần báo cáo đúng kết quả đó; không tự tạo trường hợp thất bại.

## Hạn chế của phương pháp

Các hạn chế sau thuộc thiết kế và phép thử hiện tại:

- COCO128 là tập con dùng cho huấn luyện; bộ phát hiện đã huấn luyện sẵn có thể từng thấy các ảnh này.
- Số ảnh ít, chỉ dùng một bộ phát hiện và lỗi tổng hợp; chưa thể chẩn đoán lỗi camera vật lý từ kết quả này.
- Vật thể bị che vẫn giữ nhãn chuẩn gốc; recall phản ánh mất khả năng quan sát, kể cả khi vật thể không còn đủ thông tin để nhận biết.
- Ngưỡng độ tin cậy cố định; kết quả phụ thuộc ngưỡng được chọn.
- Tương quan có thể chịu ảnh hưởng của độ khó nội dung ảnh; cần bổ sung mức giảm recall theo cặp và tỷ lệ giữ lại phát hiện đúng.
- Không khẳng định ý nghĩa thống kê từ các phiên bản lỗi lặp lại trên cùng ảnh gốc.

## Đề xuất cải tiến và cách kiểm chứng

Đánh giá chất lượng theo vùng ảnh, với trọng số gắn với nhiệm vụ phát hiện vật thể, trên một tập kiểm định độc lập. Khi triển khai, cần cơ chế xác định vùng quan tâm độc lập; không dùng vùng lấy từ nhãn chuẩn.

Các bước tiếp theo là **đề xuất, chưa được kiểm chứng**:

- So sánh điểm toàn ảnh với điểm theo vùng trên cùng mẫu; kiểm tra khả năng nhận biết trường hợp mất mục tiêu bằng recall, tỷ lệ giữ lại và tỷ lệ cảnh báo sai.
- Thử cơ chế dự phòng: cảnh báo ảnh suy giảm, yêu cầu chụp lại hoặc chuyển sang nguồn quan sát khác nếu hệ thống có hỗ trợ. Đo tỷ lệ bỏ sót cảnh báo, cảnh báo sai và mức phục hồi phát hiện sau khi kích hoạt.
- Kiểm tra trên ảnh chưa dùng để chọn ngưỡng, nhiều bộ phát hiện và dữ liệu lỗi camera thực tế; tổng hợp độ bất định theo ảnh gốc để tránh coi các biến thể của một ảnh là mẫu độc lập.

## Tài liệu tham khảo

Các liên kết dưới đây được giữ từ báo cáo gốc:

- [MUSIQ: Multi-Scale Image Quality Transformer](https://openaccess.thecvf.com/content/ICCV2021/html/Ke_MUSIQ_Multi-Scale_Image_Quality_Transformer_ICCV_2021_paper.html)
- [Kho mã IQA-PyTorch](https://github.com/chaofengc/IQA-PyTorch)
- [Tài liệu bộ dữ liệu COCO128](https://docs.ultralytics.com/datasets/detect/coco128/)
- [API dự đoán Ultralytics](https://docs.ultralytics.com/modes/predict/)
