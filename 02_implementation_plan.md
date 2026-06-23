# Kế Hoạch Triển Khai Dự Án (Implementation Plan)

Dự án được lên kế hoạch triển khai theo 5 giai đoạn chính nhằm đảm bảo tính liền mạch từ việc xử lý dữ liệu, huấn luyện các model cơ sở (Vision) đến xây dựng mô hình đa phương thức phức tạp (Multi-modal VQA).

## Giai đoạn 1: Thu thập, Tiền xử lý dữ liệu và Thiết lập môi trường
**Mục tiêu**: Có sẵn tập dữ liệu chuẩn hóa cho tất cả các bài toán và môi trường sẵn sàng.
- **Tác vụ**:
  1. Tải và phân chia (Train/Val/Test) các bộ dữ liệu: NEU Surface Defect, DAGM, MVTec AD.
  2. Chuẩn hóa nhãn (Bounding boxes format YOLO/COCO, Segmentation Mask 2D arrays).
  3. **Tạo lập bộ dữ liệu VQA/Captioning**: Do dữ liệu công nghiệp thường không có sẵn cặp QA, cần tiến hành tổng hợp. Dùng các nhãn có sẵn (loại lỗi, kích thước box/mask) làm prompt để nhờ các mô hình ngôn ngữ lớn (như GPT-4) sinh ra các cặp câu hỏi - trả lời hoặc mô tả lỗi tương ứng cho từng ảnh.
  4. Cấu hình môi trường phát triển: PyTorch, HuggingFace, Ultralytics, môi trường GPU.

## Giai đoạn 2: Phát triển các Module Computer Vision Độc lập
**Mục tiêu**: Xây dựng thành công bộ trích xuất đặc trưng hình ảnh mạnh mẽ thông qua việc huấn luyện mô hình Detection/Segmentation.
- **Tác vụ**:
  1. **Bài toán Detection**:
     - Huấn luyện YOLOv8 hoặc Faster R-CNN.
     - Tối ưu hóa hyper-parameters, đánh giá mAP (Mean Average Precision).
  2. **Bài toán Segmentation**:
     - Huấn luyện U-Net / SegFormer tập trung vào các vùng ranh giới lỗi phức tạp.
     - Đánh giá bằng IoU (Intersection over Union) và Dice Coefficient.
  3. **Trích xuất đặc trưng**: Lấy ra các bộ mã hóa (Image Encoders - CNN/ViT) từ các model trên hoặc sử dụng mô hình pre-trained chuyên biệt để tạo ra **Visual Embeddings**.

## Giai đoạn 3: Phát triển Module Xử lý Ngôn ngữ và Multi-modal Fusion
**Mục tiêu**: Xây dựng "não bộ" có khả năng suy luận (Reasoning) từ Hình ảnh và Văn bản.
- **Tác vụ**:
  1. **Text Encoding**: Tích hợp mô hình Transformer (e.g., BERT, RoBERTa) để chuyển đổi câu hỏi/text prompt thành **Text Embeddings**.
  2. **Xây dựng Fusion Module**:
     - Phát triển mạng liên kết (VD: Cross-Attention layers) để mô hình có thể đối chiếu các từ khóa trong câu hỏi (ví dụ "vết nứt", "ở đâu") với các vùng tương ứng trên bức ảnh (Visual features).
  3. **Xây dựng đầu ra (Head Architecture)**:
     - Output head cho Captioning (Auto-regressive sinh văn bản).
     - Output head cho VQA (Classification layer để chọn câu trả lời hoặc Generator để sinh câu trả lời tự nhiên).
  4. Huấn luyện toàn bộ pipeline Multi-modal (có thể áp dụng phương pháp fine-tuning trên các kiến trúc Vision-Language Models nhỏ gọn gọn như BLIP, LLaVA cỡ nhỏ để rút ngắn thời gian).

## Giai đoạn 4: Tích hợp, Kiểm thử và Tối ưu
**Mục tiêu**: Đánh giá tổng thể hiệu năng của hệ thống dưới góc nhìn toàn trình (End-to-End).
- **Tác vụ**:
  1. Xây dựng luồng pipeline hoàn chỉnh: Image $\rightarrow$ (Detection + Segmentation + Visual Embedding) $\rightarrow$ Text Input $\rightarrow$ Fusion $\rightarrow$ Output (Quality Inspection result + VQA Answer).
  2. Kiểm thử với các "edge cases" (ca khó): Ảnh nhiễu, lỗi siêu nhỏ, hình thái lỗi chưa từng xuất hiện.
  3. Đánh giá chất lượng sinh văn bản (Sử dụng độ đo BLEU, ROUGE, METEOR) và VQA Accuracy.
  4. Tối ưu thời gian suy luận (Inference time).

## Giai đoạn 5: Phát triển Giao diện và Đóng gói Dự án
**Mục tiêu**: Đưa sản phẩm thành dạng ứng dụng demo trực quan.
- **Tác vụ**:
  1. Xây dựng Web App/Demo UI bằng **Gradio** hoặc **Streamlit**. Tính năng: Upload ảnh, hiển thị Bounding Box, Segmentation Mask và tích hợp khung Chatbot để thực hiện VQA.
  2. Viết tài liệu kỹ thuật, hướng dẫn cài đặt và sử dụng (README, API docs).
  3. (Tùy chọn) Đóng gói ứng dụng sử dụng Docker.
  4. Viết báo cáo tổng kết và nghiệm thu dự án.
