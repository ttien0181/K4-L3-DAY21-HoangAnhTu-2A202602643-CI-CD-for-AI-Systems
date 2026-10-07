# Báo Cáo Lab Day 21 - CI/CD cho AI Systems

| | |
|---|---|
| Họ và tên | Hoàng Anh Tú |
| MSSV | 2A202602643 |
| Lớp / Khóa | K4 |
| Repo GitHub | github.com/ttien0181/K4-L3-DAY21-HoangAnhTu-2A202602643-CI-CD-for-AI-Systems |
| Ngày nộp | 7/10 |

---

## 1. Bộ Siêu Tham Số Đã Chọn và Lý Do

| Lần chạy | n_estimators | learning_rate | max_depth | f1_score | accuracy |
|---|---|---|---|---|---|
| 1 | 100 | 0.1 | 3 | 0.7109 | 0.8780 |
| 2 | 50 | 0.05 | 2 | 0.6051 | 0.8460 |
| 3 | 200 | 0.1 | 5 | 0.7149 | 0.8740 |

**Bộ siêu tham số đã chọn:** `n_estimators=200`, `learning_rate=0.1`, `max_depth=5`.

**Lý do:** Lần 3 đạt F1 cao nhất. Lần 2 cho thấy giảm learning_rate mà không tăng n_estimators sẽ làm giảm hiệu quả. Lần 1 có accuracy cao nhất nhưng F1 thấp hơn Lần 3, cho thấy accuracy bị lệch bởi lớp đa số và F1 phản ánh tốt hơn hiệu quả thực trên lớp thiểu số.

---

## 2. Vì Sao Ngưỡng Chất Lượng Đặt Trên F1 Chứ Không Phải Accuracy

Tập dữ liệu Adult có tỷ lệ lớp thu nhập >50K chỉ khoảng 25%. Mô hình luôn dự đoán "thu nhập thấp" đạt accuracy ~75% mà không học được gì, nên accuracy không phản ánh đúng khả năng phân loại. F1-score của lớp dương kết hợp precision và recall, đo lường hiệu quả thực tế trên nhóm thu nhập cao - nhóm chúng ta quan tâm. Không dùng average="weighted" hay "macro" để tránh bị trung bình hóa bởi lớp đa số, làm che lấp hiệu quả thực trên lớp thiểu số.

---

## 3. Khó Khăn Gặp Phải và Cách Giải Quyết

| Khó khăn | Nguyên nhân | Cách giải quyết |
|---|---|---|
| Thiếu module `pkg_resources` và lỗi `sqlalchemy` | `mlflow==2.13.0` yêu cầu `setuptools==68.2.2` và `sqlalchemy<2.0` | Pin đúng versions vào `requirements.txt` và cài lại |
| Lỗi load model trên EC2 | EC2 cài `scikit-learn==1.7.2` tự động, model train với `1.4.2` | Cài `scikit-learn==1.4.2` lên EC2 để match version |
| Health check fail trong Release job | Server cần thời gian khởi động lâu hơn 5 giây | Thêm retry loop: kiểm tra tối đa 12 lần, mỗi lần chờ 5 giây |

---

## 4. So Sánh Bước 2 và Bước 3

| | f1_score | accuracy |
|---|---|---|
| Bước 2 (chỉ `train_batch1`) | 0.7149 | 0.874 |
| Bước 3 (thêm `train_batch2`) | 0.7354 | 0.882 |

**Nhận xét:** F1 tăng từ 0.7149 lên 0.7354 và accuracy tăng từ 0.874 lên 0.882 sau khi ghép thêm 22.361 mẫu. Mức tăng không lớn là bình thường vì 2 batch có cùng phân phối, nhưng xu hướng tích cực cho thấy pipeline tự động chạy đúng từ commit dữ liệu đến triển khai mô hình mới.

---

## 5. Phần Bonus Đã Thực Hiện (nếu có)

Không thực hiện bonus.
