# 📁 Project: SUN RGB-D Monocular Depth Estimation (BTL2 - CO3133)

Dự án chuẩn bị và tiền xử lý dữ liệu cho bài toán **Monocular Depth Estimation** tuân thủ chặt chẽ **Section 19.6**.

---

## 📂 Cấu trúc thư mục

```text
sunrgbd-depth-estimation/
├── 01_Dataset_EDA.ipynb     # Notebook Jupyter chạy trên Colab phân tích dữ liệu sơ bộ (EDA)
├── dataset_proposal.md      # Đề cương Proposal hoàn chỉnh (11 mục) để nộp giảng viên
└── README.md                # Hướng dẫn chi tiết
```

---

## 🚀 Hướng dẫn chạy `01_Dataset_EDA.ipynb` trên Google Colab

1. **Mở Google Colab:** Truy cập [colab.research.google.com](https://colab.research.google.com/).
2. **Tải lên Notebook:** Chọn tab **Upload** (Tải lên) $\rightarrow$ Kéo tệp [`01_Dataset_EDA.ipynb`](file:///Users/nguyencolece/.gemini/antigravity-ide/scratch/sunrgbd-depth-estimation/01_Dataset_EDA.ipynb) vào.
3. **Chạy toàn bộ:** Bấm **Runtime** $\rightarrow$ **Run all** (hoặc `Ctrl + F9`).
4. **Kết quả:**
   - Notebook sẽ tự động tải gói mẫu 1.3 MB của SUN RGB-D trong 2 giây.
   - Tính toán đầy đủ thống kê cự ly depth ($0.5\text{m} - 8.0\text{m}$) và tỷ lệ invalid pixels.
   - Tự động xuất 3 hình ảnh minh chứng chất lượng cao vào thư mục `eda_outputs/` để bạn đưa vào Landing Page và báo cáo:
     1. `depth_and_invalid_distribution.png`: Histogram phân bố độ sâu & tỷ lệ invalid pixels.
     2. `scene_distribution.png`: Biểu đồ phân bố các loại phòng (Bedroom, Office, Living Room...).
     3. `sample_triplets_evidence.png`: Bộ 3 hình minh chứng: Input RGB – Ground Truth Depth Map – Validity Mask.
