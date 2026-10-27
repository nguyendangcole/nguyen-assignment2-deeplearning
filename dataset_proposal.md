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
| **Analyzed Dataset Corpus** | **7,001 pairs** (chiếm ~68% full benchmark, thỏa mãn vượt trội yêu cầu $\ge 5{,}000$) |
| **Selected Split Plan** | Train: 5,285 pairs \| Val: 1,090 pairs \| Test: 626 pairs |
| **Depth Ground Truth Format** | 16-bit PNG (Lossless uint16), giải mã: $Z = ((D \gg 3) \mid (D \ll 13)) / 1000.0$ (mét) |
| **Effective Evaluation Range** | $0.50\,\text{m} \le Z \le 8.00\,\text{m}$ (Indoor envelope Section 19.6) |
| **Empirical Distribution** | Mean: $2.32\,\text{m}$ \| Median: $2.07\,\text{m}$ \| Std: $1.04\,\text{m}$ |
| **Invalid Pixel Indicator** | `valid_mask = (Z > 0.5) & (Z <= 8.0)` (Mean missing: $10.34\%$, Max: $64.53\%$) |
| **Anti-Leakage Split Unit** | **Physical Room / Scene Disjoint Split** |
| **Hardware & Compute** | Google Colab Pro (NVIDIA L4 24GB / T4 16GB), VRAM usage ~6.0 GB |

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
  Ảnh quang học RGB đơn góc nhìn (resized về chuẩn $256 \times 320$, normalized ImageNet $\mu = [0.485, 0.456, 0.406]$, $\sigma = [0.229, 0.224, 0.225]$).
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
- **Kiểu nhãn (Annotation) & Giải mã phần cứng:**
  - Ảnh depth chuẩn **16-bit PNG** không nén mất mát (lossless uint16).
  - Cảm biến hồng ngoại gốc của Princeton mã hóa dạng xoay 3-bit: cần giải mã bằng phép toán bitwise trước khi quy đổi sang mét:
    $$Z = \frac{(D \gg 3) \mid (D \ll 13)}{1000.0} \quad (\text{mét})$$

---

### 4. Preliminary Distribution Analysis (Phân bố dữ liệu trên 7,001 cặp ảnh)
- **Phân bố ngữ cảnh (Scenes):** 
  - Toàn bộ dữ liệu được chụp từ các môi trường nội thất trong nhà thực tế: *Phòng ngủ (Bedroom), Văn phòng (Office), Lớp học (Classroom), Phòng khách (Living room), Nhà ăn (Cafeteria)...*
- **Phân bố cự ly độ sâu thực nghiệm ($Z$):**
  - **Mean Valid Depth:** $2.32\,\text{m}$, **Median:** $2.07\,\text{m}$, **Std:** $1.04\,\text{m}$.
  - $Z \in [0.5\,\text{m}, 3.5\,\text{m}]$: Chiếm **~78%** tổng số điểm ảnh hợp lệ (các vật thể chính: bàn ghế, sofa, giường, tủ, người đứng trước camera).
  - $Z \in [3.5\,\text{m}, 8.0\,\text{m}]$: Chiếm **~22%** (tường đối diện, trần nhà, cuối hành lang).
- **Thống kê Invalid Pixels:**
  - Trung bình **10.34%** tổng số pixel trong mỗi frame là invalid (`depth == 0`). Max ghi nhận: $64.53\%$.
  - Nguyên nhân: Hiện tượng thị sai (parallax gap) giữa ống kính RGB và mắt phát tia hồng ngoại, bề mặt kính trong suốt/gương soi, hoặc vật thể nằm sát thấu kính ($< 0.5\,\text{m}$).

---

### 5. Data-Splitting Plan (Kế hoạch phân chia dữ liệu)
Nhóm thiết lập phân vùng dữ liệu thỏa mãn chặt chẽ yêu cầu $\ge 5,000$ mẫu:

| Tập dữ liệu (Split) | Số lượng cặp ảnh (Pairs) | Tỷ lệ (%) | Mục đích sử dụng |
| :--- | :--- | :--- | :--- |
| **Train Set** | **5,285** | ~75.5% | Tối ưu gradient, cập nhật trọng số mô hình |
| **Validation Set** | **1,090** | ~15.6% | Điều chỉnh hyperparameter, Early Stopping |
| **Test Set** | **626** | ~8.9% | Đánh giá tổng kết độc lập cuối cùng |
| **Tổng cộng** | **7,001** | **100%** | Phân hoạch từ tập dữ liệu đầy đủ đã trích xuất |

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
   $$\delta_n = \frac{1}{|\Omega|} \sum_{i \in \Omega} \mathbb{I} \left( \max\left(\frac{y_i}{\hat{y}_i}, \frac{\hat{y}_i}{y_i}\right) < 1.25^n \right), \quad n \in \{1, 2, 3\}$$

---

### 8 & 9. Model Architecture & Planned Experiments (E1, E2, E3)
Để nghiên cứu sâu sự chênh lệch giữa mô hình CNN cổ điển và Vision Transformer nền tảng, đồ án thiết lập 3 thí nghiệm có đối chứng chặt chẽ:

| Thí nghiệm (Exp) | Kiến trúc mô hình | Chiến lược huấn luyện | Mục tiêu khoa học |
| :--- | :--- | :--- | :--- |
| **E1** | **Simple CNN Encoder–Decoder (UNet)** | **Trained from scratch** (khởi tạo trọng số ngẫu nhiên) | Thiết lập mốc hiệu năng cơ sở (baseline) không dùng pretraining. |
| **E2** | **Depth Anything V2 (Small - ViT)** | **Freeze encoder, train depth head only** | Đo lường năng lực trích xuất đặc trưng của Vision Transformer foundation model. |
| **E3** | **Depth Anything V2 (Small - ViT)** | **Full fine-tuning (Encoder + Depth Head)** | Tối ưu hóa toàn diện end-to-end, cạnh tranh độ sắc nét SOTA trên indoor scenes. |

- **Hàm mất mát chung:** Kết hợp hàm **Scale-Invariant Loss (SILog)** và **Gradient Edge Loss**:
  $$\mathcal{L}_{\text{total}} = \mathcal{L}_{\text{SILog}} + 0.5 \cdot \mathcal{L}_{\text{grad}}$$
  *(Được tính có trọng số chỉ trên vùng `valid_mask`).*

---

### 10. Compute Estimate (Ước lượng tài nguyên tính toán)
- **Môi trường:** Google Colab Pro (GPU NVIDIA L4 24GB VRAM / Tesla T4 16GB VRAM).
- **Lưu trữ dữ liệu:** File nén gốc `SUNRGBD.zip` (6.5 GB) lưu trữ cố định trên Google Drive tại `MyDrive/BTL-deep_learning/assignment2/datasets/SUNRGBD.zip`.
- **Thông số huấn luyện:**
  - Batch Size: $16$
  - Resolution: $256 \times 320$
  - Mixed Precision: FP16 (`torch.cuda.amp.autocast`)
  - VRAM chiếm dụng: $\approx 5.8 - 6.4\,\text{GB}$
  - Tốc độ xử lý trên L4: $\approx 35 - 45\,\text{giây} / \text{epoch}$ (train 20 epochs mất $\approx 15\,\text{phút}$).

---

### 11. Subset Selection Rules & Section 19.6 Specifications
- **Zero-Drop Strategy:** Bảo toàn 100% các cặp ảnh trong tập dữ liệu. Các điểm ảnh bị che khuất, bề mặt kính trong suốt hoặc ngoài dải đo được loại bỏ động qua binary boolean tensor trong hàm Loss:
  ```python
  valid_mask = (depth_gt > 0.5) & (depth_gt <= 8.0) & np.isfinite(depth_gt)
  ```
  Tất cả các hàm loss và metrics đều chỉ tính gradient trên vùng `valid_mask == True`.
