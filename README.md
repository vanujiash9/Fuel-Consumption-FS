<div align="center">

# FluxMare

### Hệ thống dự đoán tiêu thụ nhiên liệu tàu thủy kết hợp Machine Learning, RAG và LLM

FluxMare dự đoán mức tiêu thụ nhiên liệu theo thông số vận hành, đồng thời sử dụng RAG và mô hình ngôn ngữ để giải thích kết quả bằng tiếng Việt.

[Demo](#demo-giao-diện) · [Kiến trúc](#kiến-trúc-hệ-thống) · [Kết quả](#kết-quả-thực-nghiệm) · [Cài đặt](#cài-đặt) · [Hướng phát triển](#hướng-phát-triển)

<br>

![Python](https://img.shields.io/badge/Python-3.10%2B-3776AB?logo=python&logoColor=white)
![Flask](https://img.shields.io/badge/Backend-Flask-000000?logo=flask&logoColor=white)
![React](https://img.shields.io/badge/Frontend-React%20%2B%20TypeScript-61DAFB?logo=react&logoColor=black)
![PostgreSQL](https://img.shields.io/badge/Database-PostgreSQL-4169E1?logo=postgresql&logoColor=white)
![Supabase](https://img.shields.io/badge/Vector%20DB-Supabase-3ECF8E?logo=supabase&logoColor=white)
![XGBoost](https://img.shields.io/badge/ML-XGBoost-FF6600)
![LightGBM](https://img.shields.io/badge/ML-LightGBM-02569B)
![CatBoost](https://img.shields.io/badge/ML-CatBoost-FFCC00)
![RAG](https://img.shields.io/badge/Architecture-RAG-7B61FF)
![LLM](https://img.shields.io/badge/LLM-Qwen%20%7C%20Llama-8A2BE2)

</div>

---

## Tổng quan

**FluxMare** là hệ thống AI hỗ trợ dự đoán và phân tích mức tiêu thụ nhiên liệu tàu thủy.

Hệ thống kết hợp ba thành phần chính:

- **Machine Learning** để dự đoán mức tiêu hao nhiên liệu từ thông số vận hành
- **RAG** để truy xuất kiến thức chuyên ngành từ cơ sở dữ liệu vector
- **LLM** để giải thích kết quả và trả lời câu hỏi bằng tiếng Việt

FluxMare được xây dựng theo hướng end-to-end: từ nhập dữ liệu, dự đoán, giải thích kết quả đến trực quan hóa trên dashboard.

---

## Bài toán

Mức tiêu thụ nhiên liệu của tàu chịu ảnh hưởng bởi nhiều yếu tố như:

- Loại tàu
- Tốc độ vận hành
- Hướng và tốc độ gió
- Chiều cao sóng
- Mớn nước
- Tải trọng
- Điều kiện môi trường

Việc ước lượng không chính xác có thể làm tăng chi phí vận hành và gây khó khăn cho việc tối ưu hành trình.

FluxMare hướng đến hai mục tiêu:

1. Dự đoán mức tiêu thụ nhiên liệu theo thời gian thực
2. Giải thích kết quả bằng ngôn ngữ tự nhiên để người dùng dễ hiểu và sử dụng

---

## Demo giao diện

<div align="center">

<img src="Website_interface.jpg" alt="Giao diện chat và dashboard FluxMare" width="48%"/>
<img src="Website_interface2.jpg" alt="Form nhập thông số vận hành" width="48%"/>

</div>

Giao diện gồm ba khu vực chính:

- **Bên trái:** lịch sử và tìm kiếm cuộc hội thoại
- **Ở giữa:** chatbot và form nhập thông số chuyến đi
- **Bên phải:** dashboard kết quả dự đoán, thông tin tàu và biểu đồ dữ liệu đầu vào

---

## Tính năng chính

- Dự đoán mức tiêu thụ nhiên liệu cho nhiều loại tàu
- Tự động chọn model phù hợp theo tàu
- Nhận dữ liệu từ form hoặc câu hỏi ngôn ngữ tự nhiên
- Truy xuất kiến thức chuyên ngành bằng vector search
- Giải thích kết quả dự đoán bằng tiếng Việt
- Chatbot hỏi đáp về vận hành và nhiên liệu tàu thủy
- Dashboard trực quan hóa dữ liệu đầu vào
- Quản lý lịch sử hội thoại
- So sánh nhiều mô hình Machine Learning
- Đánh giá tự động chất lượng câu trả lời của LLM

---

## Kiến trúc hệ thống

<div align="center">

![FluxMare System Pipeline](pipeline%20ml_ai.drawio.png)

</div>

Hệ thống sử dụng **Intent Classification** để định tuyến yêu cầu theo hai luồng.

### Luồng dự đoán

```text
Thông số vận hành
        ↓
Kiểm tra dữ liệu đầu vào
        ↓
Xác định loại tàu
        ↓
Chọn model tương ứng
        ↓
Stacked Ensemble
        ↓
Dự đoán mức tiêu thụ nhiên liệu
        ↓
LLM giải thích kết quả
        ↓
Dashboard và phản hồi tiếng Việt
```

### Luồng RAG

```text
Câu hỏi người dùng
        ↓
Nomic Embed Text v1.5
        ↓
Semantic Search
        ↓
Supabase / pgvector
        ↓
Lọc context theo similarity
        ↓
LLM sinh câu trả lời
```

---

## Pipeline Machine Learning

FluxMare benchmark nhiều nhóm mô hình:

- Linear models
- Tree-based models
- Boosting models
- Deep Learning
- Meta-Ensemble

Mô hình được chọn là **Stacked Ensemble**, kết hợp:

```text
XGBoost
+ LightGBM
+ CatBoost
        ↓
Ridge Meta-Learner
        ↓
Final Prediction
```

Cách tiếp cận này tận dụng ưu điểm của nhiều mô hình và cải thiện độ ổn định trên các loại tàu khác nhau.

---

## Kết quả thực nghiệm

### Benchmark mô hình

Dữ liệu thực nghiệm gồm khoảng **174.000 bản ghi** của ba tàu:

- CPS Poseidon
- CPS Triton
- OSS Ceto

| Nhóm | Mô hình | R² trung bình | MAE (kg/s) | RMSE (kg/s) | Độ lệch chuẩn R² |
|---|---|---:|---:|---:|---:|
| Meta-Ensemble | **Stacked Ensemble** | **0,970** | **0,013** | **0,023** | **0,027** |
| Tree-based | Random Forest | 0,968 | 0,013 | 0,024 | 0,032 |
| Boosting | XGBoost | 0,963 | 0,017 | 0,026 | 0,035 |
| Boosting | LightGBM | 0,954 | 0,021 | 0,031 | 0,042 |
| Boosting | CatBoost | 0,948 | 0,023 | 0,034 | 0,044 |
| Deep Learning | Transformer | 0,940 | 0,025 | 0,037 | 0,050 |
| Deep Learning | MLP | 0,925 | 0,033 | 0,044 | 0,050 |
| Linear | Ridge | 0,765 | 0,066 | 0,089 | 0,097 |

### Kết quả Stacked Ensemble theo từng tàu

| Tàu | R² | MAE (kg/s) | RMSE (kg/s) |
|---|---:|---:|---:|
| CPS Poseidon | 0,9919 | 0,0231 | 0,0401 |
| CPS Triton | 0,9314 | 0,0102 | 0,0170 |
| OSS Ceto | 0,9864 | 0,0063 | 0,0124 |

Stacked Ensemble đạt R² trên 0,93 ở cả ba tàu và có độ lệch chuẩn thấp nhất trong nhóm benchmark.

---

## Đánh giá LLM

Hệ thống đánh giá Qwen 2.5 VL 7B và Llama 3.1 8B trên 25 test case bằng 9 chỉ số tự động.

| Chỉ số | Qwen 2.5 VL 7B | Llama 3.1 8B |
|---|---:|---:|
| Điểm tổng quát trung bình | **62,34 ± 13,06** | 61,61 ± 15,14 |
| Tỷ lệ tiếng Việt | 100% | 100% |
| Độ phủ từ khóa | **90,0 ± 25,0%** | 88,7 ± 25,3% |
| Tỷ lệ không có ảo giác | **68,0%** | 64,0% |
| Thời gian phản hồi | 48,03 ± 20,45 giây | **46,56 ± 22,53 giây** |

Kết quả kiểm định cho thấy hai model không khác biệt đáng kể về tổng điểm. Qwen nhỉnh hơn về độ phủ từ khóa và tỷ lệ không có ảo giác.

---

## Công nghệ sử dụng

### Machine Learning và AI

| Thành phần | Công nghệ |
|---|---|
| Ensemble | XGBoost, LightGBM, CatBoost, Ridge |
| Model benchmark | Random Forest, Ridge, MLP, Transformer và các boosting models |
| Embedding | Nomic Embed Text v1.5 |
| Vector search | pgvector, cosine similarity |
| LLM | Qwen 2.5 VL 7B, Llama 3.1 8B |
| Local inference | LM Studio |
| Evaluation | SciPy, Pandas, OpenPyXL |

### Backend

| Thành phần | Công nghệ |
|---|---|
| Ngôn ngữ | Python |
| Framework | Flask |
| ORM | SQLAlchemy |
| Validation | Pydantic |
| Database | PostgreSQL / Supabase |
| HTTP client | HTTPX |
| Model serialization | Joblib, Dill |

### Frontend

| Thành phần | Công nghệ |
|---|---|
| Framework | React |
| Ngôn ngữ | TypeScript |
| Build tool | Vite |
| Biểu đồ | Recharts |
| UI components | Radix UI |
| Styling | CSS |

---

## Cấu trúc dự án

```text
Fuel-Consumption-FS/
├── api/                    # API endpoints
├── core/                   # Cấu hình và khởi tạo ứng dụng
├── evaluation/             # Đánh giá tự động LLM
├── evaluation_results/     # Kết quả đánh giá
├── frontend/               # React + TypeScript application
├── models/                 # Model dự đoán theo từng tàu
├── services/               # Prediction, RAG và LLM services
├── eval_llms.xlsx          # Bộ test đánh giá LLM
├── main.py                 # Entry point backend
├── requirements.txt        # Python dependencies
├── SETUP_GUIDE.md          # Hướng dẫn cài đặt chi tiết
├── Website_interface.jpg
├── Website_interface2.jpg
├── pipeline ml_ai.drawio.png
└── README.md
```

---

## Yêu cầu hệ thống

- Python 3.10 trở lên
- Node.js 18 trở lên
- PostgreSQL hoặc Supabase
- LM Studio hoặc một OpenAI-compatible local LLM server
- Các model `.pkl` đã được huấn luyện
- RAM tối thiểu 16 GB
- GPU được khuyến nghị nếu chạy LLM cục bộ

---

## Cài đặt

### 1. Clone repository

```bash
git clone https://github.com/vanujiash9/Fuel-Consumption-FS.git
cd Fuel-Consumption-FS
```

### 2. Tạo môi trường Python

```bash
python -m venv .venv
```

Windows:

```bash
.venv\Scripts\activate
```

Linux hoặc macOS:

```bash
source .venv/bin/activate
```

### 3. Cài đặt backend

```bash
python -m pip install --upgrade pip
pip install -r requirements.txt
```

### 4. Cấu hình môi trường

Tạo file `.env` trong thư mục gốc:

```env
SUPABASE_DB_URL=postgresql://user:password@host:port/database
SECRET_KEY=replace-with-a-secure-random-value

LLM_API_URL=http://localhost:1234/v1/chat/completions
LLM_MODEL_NAME=qwen2.5-7b-instruct
LLM_TEMPERATURE=0.7
LLM_MAX_TOKENS=1024
```

Không commit file `.env` lên GitHub.

### 5. Chuẩn bị model

Đảm bảo thư mục `models/` chứa:

```text
models/
├── ceto_best_ml_model.pkl
├── poseidon_best_ml_model.pkl
└── triton_best_ml_model.pkl
```

### 6. Chạy backend

```bash
python main.py
```

Backend mặc định chạy tại:

```text
http://localhost:5000
```

### 7. Cài đặt frontend

```bash
cd frontend
npm install
npm run dev
```

Frontend sẽ hiển thị URL local trong terminal sau khi khởi động.

---

## Chạy đánh giá LLM

```bash
python evaluation/llm_evaluator.py eval_llms.xlsx evaluation_results 25
```

Kết quả được lưu trong thư mục:

```text
evaluation_results/
```

---

## Training Notebook

Toàn bộ quá trình huấn luyện và benchmark mô hình:

[![Mở bằng Google Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/drive/1PUn6jtCQY9mNzvFyDioRM4AH0RlD9i3l?usp=sharing)

---

## Điểm nổi bật kỹ thuật

- Benchmark 11 mô hình thuộc nhiều nhóm kiến trúc
- Stacked Ensemble ổn định trên ba loại tàu
- Kết hợp prediction, RAG và LLM trong cùng một hệ thống
- Vector search bằng Supabase và pgvector
- Đánh giá LLM bằng test case và metric tự động
- Sử dụng kiểm định thống kê để so sánh model
- Backend Flask kết nối frontend React/TypeScript
- Dashboard trực quan hóa dữ liệu vận hành
- Pipeline end-to-end từ input đến dự đoán và giải thích

---

## Hạn chế hiện tại

- Chất lượng dự đoán phụ thuộc vào phạm vi dữ liệu FuelCast
- Model hiện được xây dựng riêng cho ba loại tàu
- Thời gian phản hồi LLM còn cao khi chạy local
- Tỷ lệ không có ảo giác vẫn cần cải thiện
- Cần chuẩn hóa similarity threshold giữa tài liệu và cấu hình thực tế
- Chưa có Dockerfile và quy trình deployment hoàn chỉnh
- Chưa có CI/CD và automated integration tests
- Cần tách credentials khỏi source code và tài liệu public

---

## Hướng phát triển

- [ ] Docker hóa backend, frontend và LLM service
- [ ] Bổ sung CI/CD
- [ ] Chuẩn hóa cấu hình similarity threshold
- [ ] Hiển thị nguồn RAG trong câu trả lời
- [ ] Mở rộng dữ liệu cho nhiều loại tàu hơn
- [ ] Thêm drift monitoring cho model
- [ ] Tối ưu thời gian phản hồi LLM
- [ ] Bổ sung authentication và phân quyền an toàn
- [ ] Thêm unit test và integration test
- [ ] Triển khai production trên cloud
- [ ] Theo dõi model metrics theo thời gian
- [ ] Bổ sung API documentation bằng OpenAPI

---

## Nghiên cứu khoa học

- **Mã đề tài:** KHSV014
- **Lĩnh vực:** Công nghệ thông tin – Khoa học dữ liệu
- **Đơn vị:** Trường Đại học Giao thông Vận tải TP.HCM
- **Giảng viên hướng dẫn:** TS. Lê Văn Quốc Anh

---

## Đóng góp cá nhân

Repository này được phát triển từ một dự án nhóm/fork. Khi sử dụng trong portfolio, nên mô tả rõ các phần trực tiếp thực hiện, ví dụ:

- Tiền xử lý và phân tích dữ liệu
- Benchmark mô hình Machine Learning
- Xây dựng Stacked Ensemble
- Xây dựng pipeline RAG
- Đánh giá Qwen và Llama
- Tích hợp backend hoặc frontend
- Thiết kế dashboard
- Viết báo cáo nghiên cứu

Chỉ giữ lại những nội dung phản ánh đúng đóng góp thực tế.

---

## Tác giả

**Bùi Thị Thanh Vân**

- GitHub: [@vanujiash9](https://github.com/vanujiash9)
- Email: thanh.van19062004@gmail.com

---

<div align="center">

Được xây dựng bằng **Python, Flask, React, TypeScript, Supabase, Machine Learning, RAG và LLM**.

Nếu dự án hữu ích, hãy để lại một ⭐ để ủng hộ.

</div>
