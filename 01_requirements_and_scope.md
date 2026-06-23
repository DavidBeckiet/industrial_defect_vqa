# Phân Tích Yêu Cầu và Phân Vùng Phạm Vi Dự Án

## 1. Tổng Quan Dự Án
- **Tên dự án**: Hệ thống Multi-modal AI Phát hiện, Phân vùng và Hỏi đáp về Lỗi Sản phẩm Công nghiệp.
- **Mục tiêu**: Xây dựng một hệ thống AI đa phương thức (Multi-modal) không chỉ có khả năng phát hiện (Detection) và phân vùng chi tiết (Segmentation) các lỗi bề mặt sản phẩm công nghiệp (như vết nứt, trầy xước), mà còn có khả năng giải thích, mô tả (Captioning) và trả lời các câu hỏi liên quan đến lỗi (Visual Question Answering - VQA).
- **Ứng dụng thực tế**: Kiểm tra chất lượng tự động (Quality Inspection) và cung cấp khả năng suy luận (Reasoning) hỗ trợ người vận hành hoặc kỹ sư ra quyết định.

## 2. Phân Tích Yêu Cầu (Requirements Analysis)

### 2.1. Yêu cầu Chức năng (Functional Requirements)
- **Module 1: Phát hiện lỗi (Defect Detection)**
  - Nhận diện và đóng khung (bounding box) các lỗi bề mặt (vết nứt, trầy xước, đốm hỏng...).
- **Module 2: Phân vùng lỗi (Defect Segmentation)**
  - Trích xuất chính xác mặt nạ điểm ảnh (pixel-level mask) của các vùng bị lỗi để đánh giá mức độ nghiêm trọng và diện tích lỗi.
- **Module 3: Mô tả tự động (Captioning)**
  - Sinh ra câu mô tả dạng văn bản về tình trạng của sản phẩm dựa trên hình ảnh (VD: "Sản phẩm có một vết nứt dài và sâu ở góc trái bên dưới, đánh giá rủi ro cao").
- **Module 4: Hỏi đáp trực quan (Visual Question Answering - VQA)**
  - Tiếp nhận hình ảnh sản phẩm và một câu hỏi văn bản từ người dùng (VD: "Lỗi này có thể gây rò rỉ không?", "Có bao nhiêu vết xước trên bề mặt?").
  - Phân tích và đưa ra câu trả lời dựa trên suy luận từ đặc trưng hình ảnh và ngôn ngữ.

### 2.2. Yêu cầu Phi chức năng (Non-functional Requirements)
- **Độ chính xác (Accuracy)**: Mức độ nhận diện và phân vùng phải đạt độ chính xác cao, đáp ứng các tiêu chuẩn khắt khe trong kiểm tra công nghiệp.
- **Tốc độ xử lý (Performance)**: Khả năng inference trong thời gian thực (real-time) hoặc gần thời gian thực để không làm gián đoạn dây chuyền.
- **Khả năng giải thích (Interpretability)**: Câu trả lời và mô tả từ AI phải mang tính giải thích cao (Reasoning), chỉ rõ cơ sở đưa ra kết luận.

## 3. Phân Vùng Phạm Vi Dự Án (Project Scope)

### 3.1. Dữ liệu sử dụng (Datasets)
Dự án sẽ giới hạn và tập trung sử dụng các bộ dữ liệu công nghiệp chuẩn sau:
- **NEU Surface Defect Database**: Dữ liệu lỗi bề mặt kim loại thép tấm cán nóng (6 loại lỗi: cuộn, vệt, nứt, rỗ... ).
- **DAGM**: Dữ liệu lỗi bề mặt công nghiệp với các texture nền phức tạp khác nhau.
- **MVTec AD (Subset Segmentation)**: Sử dụng các tập dữ liệu con có nhãn pixel-level mask để phục vụ bài toán phân vùng bất thường.

### 3.2. Công nghệ và Kiến trúc Mô hình (Technology Stack & Architecture)
Dự án sẽ áp dụng luồng kiến trúc (pipeline) sau:
- **Thị giác máy tính (Computer Vision - CV)**:
  - **Detection**: Triển khai **YOLOv8** (lợi thế tốc độ) hoặc **Faster R-CNN** (lợi thế độ chính xác với vật thể nhỏ).
  - **Segmentation**: Áp dụng **U-Net** hoặc kiến trúc Transformer mới **SegFormer**.
- **Multi-modal AI Pipeline (Captioning & VQA)**:
  - **Visual Processing**: Image $\rightarrow$ CNN (ResNet) / ViT (Vision Transformer) $\rightarrow$ **Visual Embedding**.
  - **Text Processing**: Text (Question/Prompt) $\rightarrow$ Transformer (BERT/RoBERTa) $\rightarrow$ **Text Embedding**.
  - **Fusion Module**: Module kết hợp đa phương thức (thường sử dụng Cross-Attention) để hợp nhất thông tin thị giác và ngôn ngữ.
  - **Generation Head**: Sinh ra Caption hoặc Answer từ thông tin đã Fusion.

### 3.3. Ngoài phạm vi (Out of Scope)
- Không bao gồm việc tích hợp trực tiếp vào hệ thống cơ khí hoặc tự động hóa phần cứng của nhà máy (robot gắp thả sản phẩm).
- Không giải quyết bài toán với hình ảnh chụp từ các góc độ quá dị thường, thiếu sáng hoặc điều kiện môi trường thực tế chưa qua xử lý chuẩn hóa.
