# AIR_GUARD  
## Semi-supervised Learning for AQI Classification & PM2.5 Analysis

### 📌 Môn học
**Data Mining**

**GVHD:** ThS. Lê Thị Thùy Trang  
**Nhóm:** …  

---

## 1. Bài toán & Động cơ

Dự án tập trung vào bài toán **phân loại chất lượng không khí (AQI) theo giờ** dựa trên nồng độ **PM2.5** tại các trạm đo.

- AQI gồm **6 mức**: Good → Hazardous  
- Dữ liệu thực tế:
  - Chỉ **một phần nhỏ có nhãn**
  - Gán nhãn AQI theo giờ **tốn chi phí và thời gian**

👉 Mục tiêu của dự án là **tận dụng dữ liệu chưa gán nhãn** bằng các phương pháp **học bán giám sát (semi-supervised learning)**.

---

## 2. Dữ liệu

- Nguồn: PRSA Beijing Air Quality Dataset  
- Dạng dữ liệu: **Chuỗi thời gian theo giờ, theo trạm**
- Chia theo thời gian:
  - **Train + Unlabeled:** trước năm 2017
  - **Test:** sau năm 2017 (tránh data leakage)

---

## 3. Pipeline tổng thể

Pipeline được thiết kế **cố định và tự động**, gồm các bước:

1. Preprocessing & EDA  
2. Feature Engineering  
3. Supervised baseline  
4. Self-training  
5. Co-training  
6. Regression & ARIMA (PM2.5)  
7. Tổng hợp kết quả & trực quan hóa

📌 Toàn bộ pipeline được **điều phối bằng Papermill** để đảm bảo:
- Tính tái lập (reproducibility)
- So sánh công bằng giữa các mô hình

---

## 4. Các phương pháp chính

### 4.1 Supervised Baseline
- Huấn luyện trên tập dữ liệu có nhãn ban đầu
- Dùng làm mốc so sánh

### 4.2 Self-training
- Huấn luyện mô hình ban đầu
- Gán pseudo-label cho dữ liệu chưa nhãn với ngưỡng tin cậy τ
- Lặp lại nhiều vòng

### 4.3 Co-training
- Hai mô hình với hai nhóm đặc trưng (2 views)
- Hai mô hình trao đổi pseudo-label cho nhau
- Kỳ vọng học ổn định hơn

---

## 5. Kết quả thực nghiệm

### 5.1 So sánh hiệu năng

| Phương pháp      | Accuracy | F1-macro |
|------------------|----------|-----------|
| Self-training    | ~0.59    | ~0.53     |
| Co-training      | ~0.53    | ~0.40     |

📌 **F1-macro** được ưu tiên do dữ liệu mất cân bằng lớp.

**Nhận xét chính:**
- Self-training cho kết quả tốt hơn co-training
- Co-training chưa phát huy hiệu quả do hai view chưa đủ độc lập

---

### 5.2 Diễn biến qua các vòng lặp

- **Self-training**
  - Vòng đầu gán nhiều pseudo-label
  - Các vòng sau số nhãn mới giảm mạnh
  - F1-macro dao động và dần bão hòa

- **Co-training**
  - Số pseudo-label ổn định qua các vòng
  - F1-macro cải thiện chậm, không đột phá

---

### 5.3 Phân tích theo trạm

- Một số trạm có tần suất AQI xấu cao:
  - Aotizhongxin
  - Changping
  - Dingling

- Self-training phát hiện nhiều alert hơn trên cùng trạm so với co-training

---

### 5.4 Phân tích theo thời gian

- AQI biến động mạnh theo giờ
- Xuất hiện các cụm ô nhiễm liên tiếp
- Self-training phản ứng nhanh với các đợt ô nhiễm ngắn
- Co-training dự báo mượt hơn nhưng phản ứng chậm hơn

---

## 6. Kết luận

- Học bán giám sát giúp khai thác hiệu quả dữ liệu chưa nhãn
- Trong bài toán này:
  - **Self-training phù hợp hơn**
  - **Co-training phụ thuộc mạnh vào thiết kế view**
- Pipeline có thể mở rộng cho hệ thống cảnh báo AQI theo thời gian thực

---

## 7. Hướng mở rộng

- Thiết kế view đặc trưng tốt hơn cho co-training
- Điều chỉnh ngưỡng τ động theo vòng lặp
- Kết hợp thêm dữ liệu khí tượng
- Xây dựng dashboard cảnh báo AQI

---

## 8. Cách chạy pipeline

```bash
conda activate beijing_env
python run_papermill.py
