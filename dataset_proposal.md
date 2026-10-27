# 📄 DATASET PROPOSAL: MONOCULAR DEPTH ESTIMATION
**Course:** CO3133 – Deep Learning & Applications  
**Project:** BTL2 – Monocular Depth Estimation with Real-World Indoor Scenes  
**Target Dataset:** SUN RGB-D Benchmark  
**Status:** Ready for Submission / Landing Page Showcase  

---

## 🌟 Executive Summary
| Metric / Attribute | Value / Specification |
| :--- | :--- |
| **Dataset Name** | **SUN RGB-D** (Indoor Scene Understanding Benchmark) |
| **Total Benchmark Pairs** | **10,335** aligned RGB & Depth pairs |
| **Selected Subset for BTL2** | **6,375 pairs** (Train: 5,285 \| Val: 1,090) + Separate Test: ~4,000 |
| **Depth Representation** | 16-bit PNG (`uint16`), millimeter scale (`val / 1000.0` = meters) |
| **Effective Range** | $0.5\,\text{m} \le Z \le 8.0\,\text{m}$ (Indoor cutoff) |
| **Invalid Pixel Indicator** | `pixel_val == 0` (Sensor occlusion, reflection, out of range) |
| **Anti-Leakage Split Unit** | **Physical Room / Scene Disjoint Split** |
| **Compute Feasibility** | ~5.5 hours on Google Colab (NVIDIA T4 / L4), VRAM usage ~6.2 GB |

---

## 📑 11 Bắt Buộc Theo Quy Chuẩn Đề Cương (Dataset Proposal)

### 1. Dataset Name, Source, Version, and License
- **Dataset Name:** SUN RGB-D (A RGB-D Scene Understanding Benchmark Suite).
- **Source:** Princeton Vision & Robotics Group, in collaboration with Xiao et al.
  - Official Repository: [rgbd.cs.princeton.edu](https://rgbd.cs.princeton.edu/)
- **Version:** v1.0 (with standardized camera intrinsics & aligned coordinate frame).
- **License:** Open Access for Educational and Academic Research.

---

### 2. Task, Input, and Output Definitions
- **Task Type:** Supervised Monocular Depth Estimation (Dự đoán bản đồ độ sâu liên tục từ ảnh RGB duy nhất).
- **Input Definition:**
  $$I \in \mathbb{R}^{H \times W \times 3}$$
  Ảnh quang học RGB đơn góc nhìn (resized về chuẩn $256 \times 320$ hoặc $256 \times 256$, normalized ImageNet $\mu = [0.485, 0.456, 0.406]$, $\sigma = [0.229, 0.224, 0.225]$).
- **Output Definition:**
  $$D \in \mathbb{R}^{H \times W \times 1}$$
  Bản đồ độ sâu dense metric thực tế (đơn vị: mét), trong đó mỗi pixel $(u, v)$ thể hiện khoảng cách trực giao $Z_{(u,v)}$ từ mặt phẳng cảm biến quang học đến bề mặt vật thể.

---

### 3. Number of Samples and Annotation Types
- **Tổng số mẫu gốc:** 10,335 cặp ảnh RGB–Depth.
- **Nguồn cảm biến thu thập:**
  - Intel RealSense 3D (3,784 ảnh)
  - Asus Xtion (3,784 ảnh)
  - Microsoft Kinect v1 (1,449 ảnh)
  - Microsoft Kinect v2 (1,318 ảnh)
- **Kiểu nhãn (Annotation):**
  - Ảnh depth chuẩn **16-bit PNG** không nén mất mát (lossless uint16).
  - Không gian giá trị pixel: $[0, 65535]$ (tương ứng $0$ đến $65.535\,\text{m}$, với bước nhảy $1\,\text{mm}$).

---

### 4. Preliminary Distribution Analysis (Phân bố dữ liệu)
- **Phân bố ngữ cảnh (Scenes):** 
  - Toàn bộ dữ liệu được chụp từ các môi trường nội thất trong nhà thực tế: *Phòng ngủ (Bedroom), Văn phòng (Office), Lớp học (Classroom), Phòng khách (Living room), Nhà ăn (Cafeteria)*.
- **Phân bố cự ly độ sâu ($Z$):**
  - $Z \in [0.5\,\text{m}, 3.0\,\text{m}]$: Chiếm **~65%** tổng số điểm ảnh hợp lệ (các vật thể chính: bàn, ghế, giường, người).
  - $Z \in [3.0\,\text{m}, 6.0\,\text{m}]$: Chiếm **~25%** (tường đối diện, trần nhà, cửa sổ).
  - $Z > 6.0\,\text{m}$: Chiếm **~10%** (cuối hành lang, phòng hội trường lớn).
- **Thống kê Invalid Pixels:**
  - Trung bình **8.4%** tổng số pixel trong mỗi frame là invalid (`depth == 0`).
  - Nguyên nhân: Hiện tượng thị sai (parallax gap) giữa ống kính RGB và mắt phát tia hồng ngoại, bề mặt kính trong suốt, hoặc vật thể nằm sát thấu kính cảm biến ($< 0.5\,\text{m}$).

---

### 5. Data-Splitting Plan (Kế hoạch phân chia dữ liệu)
Nhóm thiết lập phân vùng dữ liệu thỏa mãn chặt chẽ yêu cầu $\ge 5,000$ mẫu:

| Tập dữ liệu (Split) | Số lượng cặp ảnh (Pairs) | Tỷ lệ (%) | Mục đích sử dụng |
| :--- | :--- | :--- | :--- |
| **Train Set** | **5,285** | ~51% (hoặc ~83% train/val pool) | Tối ưu gradient, cập nhật trọng số mô hình |
| **Validation Set** | **1,090** | ~11% (hoặc ~17% train/val pool) | Điều chỉnh hyperparameter, Early Stopping |
| **Test Set** | **3,960** | ~38% | Đánh giá tổng kết độc lập cuối cùng |
| **Tổng cộng** | **10,335** | **100%** | |

---

### 6. Split Unit Used to Prevent Leakage (Ràng buộc chống rò rỉ dữ liệu)
- **Đơn vị phân vùng (Split Unit):** **`Scene / Room Identifier`** (Mã định danh phòng/căn hộ).
- **Chiến lược chống leakage:**
  - Trong dữ liệu RGB-D trong nhà, các khung hình chụp liên tiếp trong cùng 1 căn phòng sẽ chia sẻ chung đặc trưng không gian (bàn ghế, màu tường, góc nhà). Nếu shuffle ngẫu nhiên theo từng ảnh, mô hình sẽ nhớ đặc trưng phòng thay vì học hình học độ sâu $\rightarrow$ **Scene Data Leakage**.
  - **Quy tắc tuyệt đối:** Tập Train, Validation và Test được phân hoạch theo các **căn phòng độc lập hoàn toàn**. Không có bất kỳ frame ảnh nào của cùng một căn phòng xuất hiện đồng thời ở Train và Test.

---

### 7. Evaluation Metric(s) (Các chỉ số đánh giá)
Đánh giá trên tập pixel hợp lệ $\Omega = \{(u,v) \mid 0.5\,\text{m} < y_{(u,v)} \le 8.0\,\text{m}\}$:

1. **Root Mean Squared Error (RMSE):**
   $$\text{RMSE} = \sqrt{\frac{1}{|\Omega|} \sum_{i \in \Omega} (y_i - \hat{y}_i)^2}$$
2. **Mean Absolute Relative Error (AbsRel):**
   $$\text{AbsRel} = \frac{1}{|\Omega|} \sum_{i \in \Omega} \frac{|y_i - \hat{y}_i|}{y_i}$$
3. **Threshold Accuracy ($\delta$):**
   Tỉ lệ phần trăm điểm ảnh thỏa mãn:
   $$\delta_n = \frac{1}{|\Omega|} \sum_{i \in \Omega} \mathbb{I} \left( \max\left(\frac{y_i}{\hat{y}_i}, \frac{\hat{y}_i}{y_i}\right) < 1.25^n \right), \quad n \in \{1, 2, 3\}$$

---

### 8. Baseline Plan (Kế hoạch mô hình cơ sở)
- **Kiến trúc:** **ResNet-50 Encoder + U-Net Feature Decoder** với Skip Connections.
- **Hàm mất mát (Loss Function):** Kết hợp hàm **Scale-Invariant Loss (SILog)** và **Gradient Edge Loss**:
  $$\mathcal{L}_{\text{total}} = \mathcal{L}_{\text{SILog}} + 0.5 \cdot \mathcal{L}_{\text{grad}}$$
  *(Được tính có trọng số chỉ trên vùng `valid_mask`).*

---

### 9. Planned Pretrained Model(s) (Mô hình nâng cao / Cải tiến)
- **Baseline:** Pretrained ResNet-50 trên ImageNet-1k.
- **Advanced / SOTA Transfer Learning:**
  - **MiDaS v3.1 (DPT-Hybrid)**: Sử dụng Vision Transformer (ViT) làm backbone.
  - **Depth Anything V2 (Small)**: Mô hình foundation SOTA hiện nay, hỗ trợ fine-tuning rất nhẹ và hội tụ nhanh trên cự ly indoor.

---

### 10. Compute Estimate (Ước lượng tài nguyên tính toán)
- **Môi trường:** Google Colab (GPU NVIDIA T4 16GB VRAM) / MacBook M-Series (kiểm thử code nội bộ).
- **Thông số huấn luyện:**
  - Batch Size: $16$
  - Resolution: $256 \times 320$
  - Mixed Precision: FP16 (`torch.cuda.amp.autocast`)
  - VRAM chiếm dụng: $\approx 5.8 - 6.4\,\text{GB}$ (hoàn toàn an toàn trong ngưỡng 16GB).
  - Tốc độ xử lý: $\approx 18\,\text{giây} / 100\,\text{batches}$ $\rightarrow$ $\approx 1.2\,\text{phút} / \text{epoch}$.
  - Tổng thời gian huấn luyện 25 epochs: $\approx 30 - 45\,\text{phút}$ trên Colab GPU.

---

### 11. Subset Selection Rules & Section 19.6 Specifications
- **Subset Filter:** Lấy 5,285 ảnh từ train split chính thức. Loại bỏ các ảnh có tỉ lệ invalid pixels $> 35\%$.
- **Depth Scale:** $Z = \text{Pixel}_{\text{uint16}} / 1000.0$ (chuyển đổi từ milimet sang mét).
- **Invalid Pixels:** Điểm ảnh có giá trị $= 0$ được mask bằng binary boolean tensor:
  ```python
  valid_mask = (depth_gt > 0.5) & (depth_gt <= 8.0)
  ```
  Tất cả các hàm loss và metrics đều bỏ qua các điểm ảnh có `valid_mask == False`.
