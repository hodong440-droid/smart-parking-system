# Smart Parking System — Hệ thống Quản lý Bãi đỗ Ô tô Thông minh

**Đồ án Thực tập Chuyên ngành (9 tuần: 02/11/2026 – 04/01/2027)**  
**Phạm vi:** Ứng dụng Web hỗ trợ tìm kiếm bãi đỗ ô tô trên bản đồ, xem chỗ trống thời gian thực, đặt giữ suất trước, chỉ đường tới cổng bãi, nhận diện biển số ô tô bằng AI (ALPR), vận hành cổng vào/ra và thống kê doanh thu.  
**Khu vực dữ liệu mẫu:** TP. Vinh, Nghệ An *(ĐH Vinh, Quảng trường Hồ Chí Minh, Ga Vinh, BV Hữu nghị Đa khoa, Vincom Vinh)*.

---

## 1. Tính năng cốt lõi

* **Bản đồ & Chỉ đường thông minh (Tài xế):**
  * Định vị GPS, tìm theo địa điểm muốn đến hoặc chấm chọn điểm trên bản đồ **OpenStreetMap (Leaflet)**.
  * Lọc bãi đỗ ô tô gần nhất theo bán kính (Haversine), phân màu trạng thái chỗ trống (🟢 Còn nhiều | 🟡 Sắp đầy | 🔴 Hết chỗ).
  * Vẽ tuyến đường đi ô tô bằng **OSRM** (quãng đường `km` & thời gian `phút`) + nút mở nhanh dẫn đường trên **Google Maps**.
* **Đặt chỗ theo sức chứa & Sơ đồ ô 2D:**
  * Đặt giữ 1 suất đỗ ô tô trong **30 phút** (tự động hủy quá hạn TTL, giới hạn chống spam tối đa 3 lần hủy/quá hạn mỗi ngày).
  * Xem sơ đồ ô đỗ 2D (`AVAILABLE`, `OCCUPIED`, `MAINTENANCE`).
* **Vận hành Cổng Vào / Ra & Nhận diện biển số AI (Nhân viên trực cổng):**
  * Dịch vụ **ALPR (Python FastAPI + OpenCV + EasyOCR)** đọc biển số ô tô Việt Nam (1 dòng & 2 dòng), kèm kiểm tra/sửa tay (*Human-in-the-loop*).
  * Tự khớp đơn đặt trước còn hạn, tự gán ô trống trên sơ đồ 2D (hỗ trợ *Đổi ô* và *chuyển vãng lai* nếu đến trễ).
  * **Cổng ra:** Tìm kiếm mờ (*Fuzzy Search*) nếu AI đọc lệch 1–2 ký tự, đối chiếu ảnh vào/ra, tự động tính tiền theo cấu hình giá riêng của từng bãi và xuất hóa đơn (Tiền mặt / QR mô phỏng).
* **Quản trị hệ thống (Admin):**
  * Quản lý bãi đỗ xe (chấm tọa độ trực tiếp trên bản đồ), cấu hình quy tắc giá linh hoạt (*Theo giờ* hoặc *Theo lượt*, số phút miễn phí đầu), quản lý nhân viên theo từng bãi và Dashboard thống kê doanh thu.

---

## 2. Kiến trúc & Công nghệ

| Thành phần | Công nghệ sử dụng | Thư mục |
| :--- | :--- | :--- |
| **Backend API** | Node.js, Express, TypeScript, Prisma ORM | `backend/` |
| **Database** | PostgreSQL (cài trực tiếp trên Windows) | `backend/prisma/` |
| **Frontend Web** | React, TypeScript, Tailwind CSS, Leaflet | `frontend/` |
| **ALPR AI Service** | Python 3.10+, FastAPI, OpenCV, EasyOCR | `alpr-service/` |
| **Tài liệu** | Kế hoạch Sprint & Kiến trúc hệ thống (4+1 View) | `MoTaCongViec.md`, `docs/SAD.md` |

---

## 3. Tài liệu dự án

Để tránh tài liệu dàn trải và dư thừa, toàn bộ dự án chỉ duy trì đúng **2 tài liệu gốc**:
1. **[MoTaCongViec.md](MoTaCongViec.md):** Lộ trình thực hiện 9 Sprint (từ 02/11/2026), phân rã đầu việc từng tuần và quy chuẩn ghi nhật ký tiến độ trên GitHub Issues.
2. **[docs/SAD.md](docs/SAD.md):** Tài liệu Kiến trúc & Thiết kế Phần mềm (*Software Architecture Document*) theo chuẩn mô hình **4+1 View**, gồm Quality Scenarios 6 thành phần, Use Case, ERD 7 bảng, State Machine, Sequence, Activity và Deployment Diagram.

---

## 4. Hướng dẫn chạy cục bộ (Sẽ cập nhật chi tiết khi khởi tạo mã nguồn ở Sprint 3–4)

### Yêu cầu môi trường
* **Node.js** $\ge 20$
* **PostgreSQL** $\ge 15$ (chạy dịch vụ trực tiếp trên Windows)
* **Python** $\ge 3.10$ (cho `alpr-service`)
