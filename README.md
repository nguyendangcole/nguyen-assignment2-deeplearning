# 📁 Project: SUN RGB-D Monocular Depth Estimation (BTL2 - CO3133)

Dự án chuẩn bị và tiền xử lý dữ liệu cho bài toán **Monocular Depth Estimation** tuân thủ chặt chẽ **Section 19.6**.

---

## 📂 Cấu trúc thư mục

```text
sunrgbd-depth-estimation/
├── 01_Dataset_EDA.ipynb               # Phân tích dữ liệu sơ bộ (EDA) & xuất manifest
├── 02_Training_and_Evaluation.ipynb  # Huấn luyện 4 thí nghiệm (E0, E1, E2, E3) & đánh giá benchmark
├── dataset_proposal.md                # Đề cương Proposal hoàn chỉnh (11 mục)
└── README.md                          # Hướng dẫn chi tiết
```

---

## ⚡ Hướng dẫn chạy `02_Training_and_Evaluation.ipynb` trên Kaggle (Khuyên dùng)

Kaggle cung cấp **30 giờ GPU miễn phí/tuần (Tesla P100 16GB / 2x T4 16GB)** và hỗ trợ chạy nền liên tục đến 12 tiếng.

### Cách 1: Nạp trực tiếp từ GitHub (Nhanh nhất)
1. Mở Kaggle: [kaggle.com](https://www.kaggle.com/) $\rightarrow$ Chọn **Create** $\rightarrow$ **New Notebook**.
2. Trên thanh menu trên cùng của Notebook, chọn **File** $\rightarrow$ **Import Notebook** (hoặc **Link to GitHub**).
3. Dán link repository: `https://github.com/nguyendangcole/nguyen-assignment2-deeplearning` $\rightarrow$ Chọn file [`02_Training_and_Evaluation.ipynb`](file:///Users/nguyencolece/Desktop/sunrgbd-depth-estimation/02_Training_and_Evaluation.ipynb).

### Cách 2: Tải lên thủ công (Upload)
1. Bấm **File** $\rightarrow$ **Upload Notebook** $\rightarrow$ Kéo tệp [`02_Training_and_Evaluation.ipynb`](file:///Users/nguyencolece/Desktop/sunrgbd-depth-estimation/02_Training_and_Evaluation.ipynb) vào.

### Cấu hình trên Kaggle (Bắt buộc):
1. **Accelerator:** Ở menu bên phải (*Settings*), chọn **GPU P100** (hoặc **GPU T4 x2**).
2. **Internet:** Chuyển sang **Internet on** (để tải thư viện và pretrained weights).
3. **Thêm dữ liệu:** Bấm **+ Add Input** $\rightarrow$ Tải file `SUNRGBD.zip` lên thành Kaggle Dataset (Kaggle sẽ tự động mount vào `/kaggle/input/`).
4. **Chạy toàn bộ:** Chọn **Run All** (hoặc bấm **Save Version** $\rightarrow$ **Save & Run All (Commit)** để notebook tự động chạy ngầm mà không cần mở trình duyệt).
5. **Tải kết quả:** Khi chạy xong, tải file `sunrgbd_experiment_results.zip` ở tab **Output** bên phải (chứa đầy đủ bảng chỉ số, đồ thị loss và ảnh so sánh trực quan).

---

## 🚀 Hướng dẫn chạy `01_Dataset_EDA.ipynb` trên Google Colab / Kaggle

1. **Mở Google Colab:** Truy cập [colab.research.google.com](https://colab.research.google.com/).
2. **Tải lên Notebook:** Chọn tab **Upload** (Tải lên) $\rightarrow$ Kéo tệp [`01_Dataset_EDA.ipynb`](file:///Users/nguyencolece/Desktop/sunrgbd-depth-estimation/01_Dataset_EDA.ipynb) vào.
3. **Chạy toàn bộ:** Bấm **Runtime** $\rightarrow$ **Run all** (hoặc `Ctrl + F9`).
4. **Kết quả:**
   - Notebook sẽ tự động tải gói mẫu 1.3 MB của SUN RGB-D trong 2 giây (hoặc quét toàn bộ 10,335 cặp từ Drive).
   - Tính toán đầy đủ thống kê cự ly depth ($0.5\text{m} - 8.0\text{m}$) và tỷ lệ invalid pixels.
   - Tự động xuất 3 hình ảnh minh chứng chất lượng cao vào thư mục `eda_outputs/`:
     1. `depth_and_invalid_distribution.png`: Histogram phân bố độ sâu & tỷ lệ invalid pixels.
     2. `scene_distribution.png`: Biểu đồ phân bố các loại phòng (Bedroom, Office, Living Room...).
     3. `sample_triplets_evidence.png`: Bộ 3 hình minh chứng: Input RGB – Ground Truth Depth Map – Validity Mask.
