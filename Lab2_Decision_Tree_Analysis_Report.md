# Báo cáo phần Phân tích cây (Tree Analysis), Các phương pháp cải thiện, So sánh kết quả và Dự thảo kết luận

## 1. Phân tích cây (Analysis of the Tree)
- **Cấu trúc cây:** Cây baseline (cây gốc) khá phức tạp với độ sâu (depth) lên đến 7 tầng, bao gồm tổng cộng 19 lá (leaves) và 37 nút (nodes). Sự phân nhánh sâu và rườm rà này thường là nguyên nhân khiến cây dễ bị phụ thuộc quá mức vào dữ liệu huấn luyện.
- **Root/Key Splits:** Đặc trưng (feature) được chọn ở nút gốc (root) và những lần chia cắt đầu tiên đóng vai trò quan trọng nhất trong việc phân tách ranh giới giữa khối u ác tính và lành tính. (Cần kiểm tra hình ảnh cây để nêu rõ tên feature - ví dụ như mean concave points, worst perimeter...).
- **Dấu hiệu Overfitting / Underfitting:** Bằng chứng rõ rệt về hiện tượng overfitting (học vẹt) thể hiện qua việc mô hình Baseline đạt độ chính xác tuyệt đối 100% trên tập huấn luyện (Train Accuracy = 1.0), nhưng độ chính xác trên tập kiểm tra (Test Accuracy) lại tụt xuống chỉ còn 91.23%. Sự chênh lệch lớn này, kết hợp với cấu trúc phức tạp 19 lá và độ sâu 7 tầng, cho thấy mô hình đang ghi nhớ cả những yếu tố nhiễu của tập train, làm giảm đi khả năng tổng quát hóa.

## 2. Các phương pháp cải thiện (Improvement Methods)

**Phương pháp 1: Giới hạn độ sâu tối đa (Max Depth)**
- **WHAT:** Thiết lập tham số `max_depth = 4` để giới hạn cây không được vượt quá độ sâu 4 tầng (thay vì 7 tầng ở baseline).
- **RESULT:** Test Accuracy tăng từ 91.23% lên 93.86%, cấu trúc cây tinh gọn hơn rõ rệt với số lá giảm từ 19 xuống 11.
- **WHY:** Việc giới hạn độ sâu ép cây dừng phân nhánh sớm, ngăn chặn việc tạo ra các node lá mang quá ít mẫu dữ liệu. Điều này giúp loại bỏ sự phụ thuộc vào nhiễu nhỏ nhặt, cải thiện đáng kể khả năng tổng quát hóa (generalization) của mô hình.

**Phương pháp 2: Số lượng mẫu tối thiểu ở node lá (Min Samples Leaf)**
- **WHAT:** Sử dụng tham số `min_samples_leaf = 3`, yêu cầu mỗi lá phải chứa ít nhất 3 mẫu, thay vì mặc định.
- **RESULT:** Test Accuracy duy trì mức 91.23%, Train Accuracy giảm còn 98.02%. Cấu trúc cây có sự thu gọn nhất định, giảm xuống còn 6 tầng và 13 lá.
- **WHY:** Buộc mỗi node lá đại diện cho nhiều hơn 1 mẫu giúp làm mượt cây quyết định. Mặc dù nó giúp cây bớt phức tạp hơn một chút, hiệu suất mang lại trên tập test vẫn chưa cải thiện đáng kể so với baseline trong trường hợp này.

**Phương pháp 3: Cắt tỉa (Pruning - Cost Complexity Pruning)**
- **WHAT:** Áp dụng cắt tỉa với ngưỡng `ccp_alpha = 0.005934` để loại bỏ đi các nhánh có ít khả năng mang lại thông tin hữu ích nhưng lại làm tăng độ phức tạp.
- **RESULT:** Test Accuracy nhảy vọt lên mức 93.86%. Cấu trúc cây trở nên vô cùng tối giản với độ sâu chỉ vỏn vẹn 3 tầng và 6 lá.
- **WHY:** Phương pháp pruning loại bỏ trực tiếp các phần phân nhánh không mang lại giá trị gia tăng đáng kể đối với tập validation. Nhờ đó, nó mang lại một cấu trúc cây đơn giản nhất có thể mà vẫn duy trì độ chính xác cao nhất (ngăn ngừa overfitting triệt để).

## 3. So sánh kết quả (Comparison of Results)
- **Bảng tổng hợp:** (Cơ sở so sánh từ bảng `results/model_comparison.csv` do Thành viên A cung cấp)

| Model            | Key Parameter      | Train Accuracy | Test Accuracy | Tree Depth | Leaves |
|------------------|--------------------|----------------|---------------|------------|--------|
| Baseline         | default            | 100.0%         | 91.23%        | 7          | 19     |
| Max Depth        | max_depth=4        | 98.68%         | 93.86%        | 4          | 11     |
| Min Samples Leaf | min_samples_leaf=3 | 98.02%         | 91.23%        | 6          | 13     |
| Pruning          | ccp_alpha=0.005934 | 97.36%         | 93.86%        | 3          | 6      |

- **Xác định Winner (Mô hình tốt nhất):** Mô hình tốt nhất là **Max Depth (`max_depth=4`)**. Dù Max Depth và Pruning chia sẻ chung vị trí dẫn đầu về Test Accuracy (93.86%), Max Depth được chọn do có chỉ số **Malignant Recall (độ nhạy với khối u ác tính)** là 92.86%, cao hơn mức 90.48% của Pruning. Trong lĩnh vực y khoa, việc bỏ lót chẩn đoán một bệnh nhân ung thư nguy hiểm hơn rất nhiều so với chẩn đoán nhầm (đối với lành tính), vì vậy ưu tiên Recall cho lớp Malignant là hợp lý.
- **Đánh giá Trade-off:** Baseline đánh đổi khả năng dự đoán thực tế để đạt 100% trên dữ liệu đã biết (độ phức tạp cao). Trong khi đó, Max Depth và Pruning chấp nhận hy sinh vài phần trăm độ chính xác của tập huấn luyện (Train Acc tụt xuống dưới 100%) để làm giảm mức độ phức tạp. Sự đánh đổi này mang lại lợi ích khổng lồ, khi mô hình thực sự đã cải thiện được khả năng "hiểu" dữ liệu thay vì "học thuộc lòng".

## 4. Dự Thảo Phần Kết Luận (Conclusion Draft)
- **Key findings:** Thuật toán Decision Tree mặc định nếu không thiết lập cẩn thận rất dễ bị overfitting, biểu hiện qua độ phân nhánh phức tạp. Bằng cách can thiệp bằng các kỹ thuật như giới hạn độ sâu hoặc tỉa nhánh, hiệu năng dự đoán trên dữ liệu chưa biết được nâng cao đáng kể.
- **Đánh giá hiệu quả:** Trên bộ dữ liệu Breast Cancer, Decision Tree thể hiện khả năng mạnh mẽ khi không chỉ mang lại kết quả chẩn đoán chính xác (Test Accuracy ~ 94%), mà quan trọng hơn là tính tường minh, có thể vẽ được cây rẽ nhánh chi tiết giúp cho đội ngũ y khoa có thể đưa ra giải thích lý luận hợp lý trong chẩn đoán.
- **Bài học rút ra:** Training accuracy 100% không đồng nghĩa với một mô hình tốt, mà thực chất thường chỉ ra điểm yếu trong việc thiết lập. Các mô hình đơn giản, ít nút, ít nhánh thường khái quát hóa dữ liệu tốt hơn là các mô hình rườm rà. Lựa chọn mô hình không chỉ dựa vào Accuracy mà còn phụ thuộc vào ngữ cảnh thực tế của bài toán (chú trọng Recall đối với bệnh nhân ung thư).
