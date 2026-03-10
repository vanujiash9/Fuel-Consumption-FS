# ⛴️ FluxMare — Dự Đoán Tiêu Thụ Nhiên Liệu Tàu Thủy với RAG & LLM

**Nghiên cứu khoa học sinh viên · Mã số: KHSV014** · Bùi Thị Thanh Vân (MSSV: 2251320039)  
Ngành Công nghệ Thông tin – Khoa học Dữ liệu · ĐH Giao thông Vận tải TP.HCM  
GVHD: TS. Lê Văn Quốc Anh

---

## 🎯 Bài toán & Giải pháp

Tối ưu hóa nhiên liệu tàu thủy là bài toán phức tạp, ảnh hưởng trực tiếp đến chi phí vận hành và yêu cầu giảm phát thải của IMO (CII). Hệ thống kết hợp **ML dự đoán chính xác + RAG + LLM** để vừa cho ra con số tức thời, vừa giải thích bằng ngôn ngữ tự nhiên tiếng Việt cho thuyền trưởng và kỹ sư vận hành.

---

## 🏗️ Kiến trúc hệ thống

```
User Input (text tự nhiên / form 7 features)
              │
   Parameter Extraction (regex NLP)
              │
     ┌────────┴────────┐
  Đủ params         Thiếu / Câu hỏi chung
     │                 │
  ML Model           RAG Pipeline
  (Stacked           → Embedding 768-dim
  Ensemble)          → Cosine Similarity ≥ 0.7
  → kg/s             → Top-3 chunks
     │                 │
     └────────┬────────┘
              ▼
         LLM Local (Qwen 2.5 / Llama 3.1)
         → Giải thích tiếng Việt
              ▼
       Dashboard + Chat Response
```

---

## 📊 Kết quả ML — Benchmark 11 mô hình (~174.000 bản ghi, 3 tàu)

| Nhóm | Mô hình | R² TB | MAE (kg/s) | RMSE (kg/s) | Std R² |
|---|---|---|---|---|---|
| **Meta-Ensemble** | **Stacked Ensemble** | **0,970** | **0,013** | **0,023** | **0,027** |
| Tree-based | Random Forest | 0,968 | 0,013 | 0,024 | 0,032 |
| Boosting | XGBoost | 0,963 | 0,017 | 0,026 | 0,035 |
| Boosting | LightGBM | 0,954 | 0,021 | 0,031 | 0,042 |
| Boosting | CatBoost | 0,948 | 0,023 | 0,034 | 0,044 |
| Deep Learning | Transformer | 0,940 | 0,025 | 0,037 | 0,050 |
| Deep Learning | MLP | 0,925 | 0,033 | 0,044 | 0,050 |
| Linear | Ridge | 0,765 | 0,066 | 0,089 | 0,097 |

### Stacked Ensemble theo từng tàu

| Tàu | R² | MAE (kg/s) | RMSE (kg/s) |
|---|---|---|---|
| CPS Poseidon | 0,9919 | 0,0231 | 0,0401 |
| CPS Triton | 0,9314 | 0,0102 | 0,0170 |
| OSS Ceto | 0,9864 | 0,0063 | 0,0124 |

> **Stacked Ensemble** (XGBoost + CatBoost + LightGBM + Ridge meta-learner) vượt trội nhất về độ ổn định đa tàu (std R² thấp nhất: 0,027), phù hợp triển khai thực tế trên đội tàu đa dạng.

---

## 🤖 Kết quả đánh giá LLM (25 test cases, 9 metrics tự động)

| Chỉ số | Qwen 2.5 VL 7B | Llama 3.1 8B |
|---|---|---|
| Điểm tổng quát TB | **62,34 ± 13,06** | 61,61 ± 15,14 |
| Tỷ lệ tiếng Việt | 100% | 100% |
| Độ phủ từ khóa | **90,0 ± 25,0%** | 88,7 ± 25,3% |
| Không có ảo giác | **68,0%** | 64,0% |
| Thời gian phản hồi (s) | 48,03 ± 20,45 | **46,56 ± 22,53** |

> T-test p = 0,855 → hai model tương đương về tổng điểm; Qwen nhỉnh hơn về keyword coverage và hallucination rate.

---

## 🔧 Công nghệ sử dụng

| Hạng mục | Chi tiết |
|---|---|
| **ML Models** | Stacked Ensemble: XGBoost + CatBoost + LightGBM + Ridge meta-learner |
| **Benchmark** | 11 mô hình: Linear · Tree-based · Boosting · Deep Learning · Meta-Ensemble |
| **RAG** | Vector search · Cosine similarity ≥ 0.7 · nomic-embed-text-v1.5 (768-dim) |
| **LLM** | Qwen 2.5 VL 7B · Llama 3.1 8B · chạy local qua LM Studio |
| **Backend** | Python · Flask · SQLAlchemy · Supabase (pgvector) |
| **Frontend** | React + TypeScript · Vite · Recharts · Radix UI |
| **Data** | FuelCast dataset · ~174.000 bản ghi · 3 tàu: Poseidon, Triton, Ceto |

---

## ✨ Điểm nổi bật

- **Benchmark có hệ thống** — so sánh 11 mô hình trên 5 nhóm kiến trúc, không chỉ dùng 1-2 mô hình như đa số nghiên cứu liên quan
- **Ổn định đa tàu** — Stacked Ensemble duy trì R² > 0,93 trên cả 3 tàu với cấu hình động lực hoàn toàn khác nhau
- **RAG với ngưỡng chất lượng** — chỉ inject context similarity ≥ 0.7, giảm hallucination LLM trong domain chuyên ngành
- **Automated Evaluation** — 9 metrics tự động + T-test + Cohen's d, xuất Excel để phục vụ báo cáo khoa học
- **End-to-end pipeline** — từ nhập liệu text tự nhiên đến dashboard trực quan và giải thích tiếng Việt

---

## 🚀 Chạy nhanh

```bash
# Backend
pip install -r requirements.txt && python main.py

# Frontend
cd frontend && npm install && npm run dev

# Evaluation
python evaluation/llm_evaluator.py eval_llms.xlsx evaluation_results 25
```

---

## 📬 Liên hệ

**Bùi Thị Thanh Vân** · thanh.van19062004@gmail.com
