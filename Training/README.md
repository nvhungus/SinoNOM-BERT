# Script Huấn luyện Mô hình Nom-BERT (Training Pipeline)

## 1. Giới thiệu chung
Bộ mã nguồn này thực hiện quy trình Continued Pretraining mô hình ngôn ngữ BERT trên dữ liệu chữ Nôm. Script được tối ưu hóa cho các văn bản cổ thông qua kỹ thuật Masking (CLM, WWM, CLM+WWM) và mở rộng bộ từ vựng.

## 2. Phương pháp Huấn luyện (Methodology)

### 2.1. Tiền xử lý dữ liệu (Data Preprocessing)
- **Mở rộng ngữ cảnh (Context Concatenation):** Thay vì huấn luyện trên từng câu thơ đơn lẻ (thường rất ngắn, 5-7 tokens), script tự động ghép 5-8 câu thơ thành một mẫu dài (khoảng 64 tokens). Điều này giúp mô hình học được mối quan hệ ngữ nghĩa xa hơn và tận dụng tối đa kiến trúc Attention của BERT.
- **Phân tách từ (Word Segmentation):** Sử dụng `VnCoreNLP` để xác định biên giới từ ghép dựa trên bản dịch Quốc ngữ, phục vụ cho kỹ thuật Whole Word Masking.

### 2.2. Chiến lược Masking (Hybrid Strategy)
Mô hình sử dụng `AggressiveMaskCollator` với cơ chế lai:
- **50% Whole Word Masking (WWM):** Che toàn bộ từ ghép (ví dụ: che cả "Quốc_Gia"). Giúp mô hình học ngữ nghĩa cấp độ từ vựng.
- **50% Character Level Masking (CLM):** Che ký tự đơn lẻ. Giúp mô hình học cấu trúc ký tự và ngữ pháp cục bộ.
- **Tỷ lệ Mask:** Được thiết lập ở mức 15% - 30% tùy vào độ dài chuỗi sau khi ghép.

### 2.3. Khởi tạo Embeddings thông minh
- Mở rộng Vocabulary của BERT gốc bằng cách thêm các ký tự Nôm có trong tập dữ liệu nhưng chưa có trong từ điển gốc.
- Sử dụng từ điển tương đồng (`SinoNom_similar_Dic`) để khởi tạo vector (weights) cho các token mới dựa trên các ký tự có nét tương đồng hoặc đồng âm đã biết, giúp mô hình hội tụ nhanh hơn.

## 3. Cấu hình Hệ thống 

Đây là phần quan trọng nhất để script hoạt động chính xác. Bạn cần thiết lập các biến đường dẫn trong file code chính.

### 3.1. Các đường dẫn dữ liệu (Data Paths)

| Tên biến | Đường dẫn mẫu | Mô tả chi tiết |
| :--- | :--- | :--- |
| **FILE_CORPUS** | `/kaggle/input/final-prj-nlp/train.xlsx` | **File dữ liệu huấn luyện chính.**<br>Định dạng: Excel (.xlsx).<br>Yêu cầu: Phải có cột `Text` (chữ Nôm) và `Translation` (Quốc ngữ - dùng để chạy VnCoreNLP). |
| **FILE_BIG_DICT** | `/kaggle/input/final-prj-nlp/QuocNgu_SinoNom_Dic.xlsx` | **Từ điển Quốc ngữ - Hán Nôm.**<br>Mục đích: Dùng để quét toàn bộ kho từ vựng và thêm các token Nôm còn thiếu vào Tokenizer của BERT. |
| **FILE_SIMILAR** | `/kaggle/input/final-prj-nlp/SinoNom_similar_Dic.xlsx` | **Từ điển Tương đồng (Đồng âm/Dị thể).**<br>Mục đích: Dùng để khởi tạo trọng số (weights) cho các token mới thêm vào. Thay vì khởi tạo ngẫu nhiên (random noise), script sẽ lấy embedding trung bình của các ký tự tương tự để gán cho token mới. |

### 3.2. Cấu hình Mô hình & Đầu ra 

| Tên biến | Giá trị mẫu | Mô tả chi tiết |
| :--- | :--- | :--- |
| **MODEL_NAME** | `Jihuai/bert-ancient-chinese` | **Mô hình nền (Pre-trained Model).**<br>Script sử dụng `bert-ancient-chinese` làm nền tảng vì nó đã học tốt các ký tự Hán cổ, có cấu trúc tương tự chữ Nôm. |
| **OUTPUT_DIR** | `/kaggle/working/final` | **Thư mục lưu kết quả.**<br>Sau khi train xong, script sẽ lưu:<br>1. `pytorch_model.bin`: Trọng số mô hình.<br>2. `vocab.txt` & `config.json`: Bộ tokenizer và cấu hình.<br>3. `loss_curve.png`: Biểu đồ quá trình huấn luyện.<br>4. Các Checkpoints (nếu có). |

## 4. Yêu cầu cài đặt 
Môi trường thực thi trên colab hoặc kaggle
## 5. Kết quả đầu ra 
Sau khi quá trình huấn luyện hoàn tất, tại thư mục `OUTPUT_DIR` sẽ có:
- Một mô hình BERT hoàn chỉnh (Model + Tokenizer) đã được tối ưu cho chữ Nôm.
- Biểu đồ Loss thể hiện sự hội tụ của mô hình qua các Epochs.