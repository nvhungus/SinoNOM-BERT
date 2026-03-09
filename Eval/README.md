# Đánh giá Mô hình 

## 1. Giới thiệu chung
Bộ mã nguồn này được thiết kế để đánh giá so sánh định lượng hiệu năng của các biến thể mô hình Nom-BERT (Masked Language Model) thông qua các tác vụ thăm dò (Probing Tasks). Mục tiêu chính là đo lường sự khác biệt về khả năng hiểu ngữ cảnh, quy luật âm vần và khôi phục văn bản chữ Nôm giữa các phương pháp huấn luyện khác nhau (WWM, CLM, Hybrid).

## 2. Các tác vụ đánh giá (Evaluation Tasks)
Hệ thống thực hiện đánh giá trên hai nhóm văn bản chính:

### Nhóm 1: Văn xuôi (Prose Tasks)
- **Tác vụ Insertion (Điền từ):** Giả lập việc văn bản bị mất ký tự ngẫu nhiên để kiểm tra khả năng khôi phục dựa trên ngữ cảnh.
- **Tác vụ Replacement (Sửa lỗi):** Thay thế ký tự gốc bằng ký tự đồng âm/gần hình (SinoNom Similar Dictionary) để tạo các trường hợp "Hard Negative", thử thách khả năng phân biệt ngữ nghĩa sâu của mô hình.

### Nhóm 2: Thơ (Poetry Tasks)
- **Tác vụ Poetry Matching:** Sử dụng Cosine Similarity để tìm câu thơ tiếp theo phù hợp nhất trong không gian vector.
- **Tác vụ Tonal Compliance:** Kiểm tra từ dự đoán có tuân thủ đúng luật Bằng/Trắc của câu thơ gốc thông qua từ điển phiên âm.

## 3. Methodology
Để đánh giá thực chất khả năng của mô hình, script không mask từ ngẫu nhiên mà sử dụng phương pháp:
- Sử dụng từ điển `SinoNom_similar_Dic.xlsx` để xác định các vị trí chứa từ dễ nhầm lẫn (đồng âm/gần hình).
- Ưu tiên chọn các vị trí này làm ứng viên để tạo ra các trường hợp "Hard Negative", buộc mô hình phải hiểu sâu ngữ nghĩa mới có thể phân biệt và dự đoán đúng.
- Thực hiện đánh giá lặp qua danh sách MODEL_PATHS để so sánh hiệu năng giữa các mô hình trên cùng một tập dữ liệu thử nghiệm.
## 4. Yêu cầu cài đặt (Requirements)
Chạy trên môi trường colab hoặc kaggle. 

## 5. Cấu trúc dữ liệu đầu vào
Script yêu cầu các file đầu vào:
1. **File Corpus (Test set):** Định dạng Excel (.xlsx), bắt buộc có cột 'Text' chứa văn bản chữ Nôm sạch.
2. **File Dictionary:** Định dạng Excel chứa danh sách các ký tự tương đồng (dùng cho tác vụ Replacement).
3. **Đường dẫn tới folder chứa các file cần thiết của model**
## 6. Chỉ số đánh giá 
Kết quả được đo lường bằng:
- **P@1 (Precision at 1):** Tỷ lệ mô hình dự đoán chính xác tuyệt đối ký tự gốc ở vị trí đầu tiên (Top-1).
- **P@10 (Precision at 10):** Tỷ lệ ký tự gốc xuất hiện trong danh sách 10 gợi ý đầu tiên của mô hình.
- **Matching Accuracy:** Tỷ lệ mô hình chọn đúng câu thơ tiếp theo từ tập các câu.
- **Tonal Accuracy:** Tỷ lệ ký tự dự đoán tuân thủ đúng luật thanh điệu (Bằng/Trắc) của ký tự gốc.

## 7. Kết quả đầu ra
Sau khi chạy, script sẽ xuất ra:
- Bảng tổng hợp hiệu năng giữa các mô hình (WWM, CLM, WWM + CLM) cho tất cả các tác vụ.