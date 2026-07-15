# Beyond AUC: Cost-Aware Evaluation of Stacking, Calibration and Feature Selection for Credit Scoring

**Tác giả:** Phạm Thị Thu Vân

Dự án nghiên cứu về mô hình chấm điểm tín dụng (credit scoring), đánh giá các mô hình
không chỉ bằng AUC mà còn theo góc nhìn **chi phí thực tế của quyết định cho vay**
(cost-sensitive). Toàn bộ pipeline được chạy nhất quán trên **3 bộ dữ liệu** để kiểm tra
tính khái quát của kết luận.

---

## Nội dung chính

Notebook trả lời ba câu hỏi nghiên cứu (RQ):

- **RQ1 — Lựa chọn đặc trưng:** So sánh chọn feature bằng **SHAP** với phương pháp truyền
  thống **IV/WOE** (Information Value).
- **RQ2 & RQ3 — Hiệu quả & độ tin cậy:** Xây dựng **Efficiency Frontier** (AUC so với thời
  gian huấn luyện) và ước lượng **khoảng tin cậy Bootstrap** cho các chỉ số.
- **Xuyên suốt:** Đánh giá **Stacking ensemble** và **hiệu chỉnh xác suất** (Calibration:
  Isotonic / Platt) dưới lăng kính chi phí sai lệch (ví dụ tỉ lệ chi phí 3:1, 5:1, 10:1, 15:1).

## Dữ liệu sử dụng

| Ký hiệu | Bộ dữ liệu | Nguồn |
|---------|------------|-------|
| **HC**  | Home Credit Default Risk (7 bảng, gộp lại) | Kaggle competition |
| **LC**  | Lending Club 2015–2018 | Kaggle dataset |
| **GMSC**| Give Me Some Credit | Kaggle competition |

> **Lưu ý:** Notebook được viết để chạy trên môi trường **Kaggle**, đọc dữ liệu từ
> `/kaggle/input/...`. Nếu chạy ở máy cá nhân, bạn cần tải dữ liệu về và chỉnh lại các biến
> đường dẫn (`HC_DIR`, `LC_PATH`, `GMSC_PATH`) ở cell cấu hình đầu tiên.

## Phương pháp

- **Tiền xử lý:** impute giá trị thiếu (median), cắt outlier theo IQR/percentile, mã hóa
  **WOE**, và chuẩn hóa (StandardScaler).
- **Mô hình nền:** Logistic Regression, Decision Tree, Random Forest, Extra Trees,
  Gaussian Naive Bayes, **XGBoost, LightGBM, CatBoost**.
- **Ensemble:** Stacking Classifier.
- **Hiệu chỉnh:** Isotonic & Platt (qua `CalibratedClassifierCV`), đo bằng ECE.
- **Chỉ số đánh giá:** AUC, Gini, KS, Brier score, Precision/Recall/F1 cho nhóm "Bad",
  chi phí kỳ vọng (expected cost), và khoảng tin cậy Bootstrap.
- **Giải thích mô hình:** SHAP.

## Cấu trúc notebook

```
PHẦN 0 — Shared Utilities
  0.1 Evaluate & summarize
  0.2 Preprocessing (impute, outlier cap, WOE + scale)
  0.3 Baseline models & Stacking
  0.4 Calibration (Isotonic/Platt) + ECE
  0.5 Efficiency table & Bootstrap CI
  0.6 Cost-sensitive analysis

PHẦN A — Home Credit Default Risk   (load/merge, lọc feature, train, stacking, calibration)
PHẦN B — Lending Club (2015–2018)
PHẦN C — GiveMeSomeCredit

PHẦN D — RQ1: SHAP vs IV Feature Selection  (chạy trên cả 3 dataset)
PHẦN E — RQ2 & RQ3: Efficiency Frontier + Bootstrap CI  (tổng hợp cross-dataset)

ABLATION         — Stacking gain theo số lượng feature
DATASET OVERVIEW — Bảng mô tả tổng quan dữ liệu
```

## Cài đặt

Yêu cầu **Python 3.12**. Cài các thư viện phụ thuộc:

```bash
pip install -r requirements.txt
```

## Cách chạy

1. Chuẩn bị 3 bộ dữ liệu (xem bảng **Dữ liệu sử dụng**) và cập nhật đường dẫn trong cell
   cấu hình nếu không chạy trên Kaggle.
2. Mở notebook và chạy tuần tự từ trên xuống:

   ```bash
   jupyter notebook 3_PhamThiThuVan.ipynb
   ```

3. Kết quả (bảng tổng hợp, hình vẽ) được lưu trong thư mục `outputs/`, một số bảng được
   xuất ra file Excel (`.xlsx`).

## Kết quả đầu ra

- Thư mục `outputs/` chứa hình và bảng tổng hợp.
- Các bảng kết quả chính xuất ra `.xlsx` (ví dụ bảng ablation, bảng chi phí kỳ vọng).
- Chỉ số được tổng hợp thành các bảng so sánh giữa mô hình raw, đã calibrate và stacking
  trên cả ba dataset.

---

*README này mô tả nội dung của `3_PhamThiThuVan.ipynb`. Cập nhật lại nếu bạn thay đổi cấu
trúc notebook hoặc nguồn dữ liệu.*
