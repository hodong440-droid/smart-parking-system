# MÔ TẢ CÔNG VIỆC & KẾ HOẠCH 9 SPRINT — SMART PARKING SYSTEM

* **Đề tài:** Xây dựng hệ thống quản lý bãi đỗ xe ô tô thông minh tích hợp bản đồ chỉ đường, đặt chỗ trước và nhận diện biển số (ALPR).
* **Thời gian thực hiện:** 9 tuần (**02/11/2026 – 04/01/2027**).
* **Nhân sự:** 01 sinh viên thực hiện (`hodong440-droid`).
* **Quy chuẩn theo dõi tiến độ:** Mỗi Sprint tương ứng với **01 GitHub Issue** làm nhật ký minh chứng. Khi hoàn thành từng công việc, sinh viên comment vào Issue theo mẫu chuẩn 4 mục:
  1. **Nội dung thực hiện**
  2. **Khó khăn gặp phải**
  3. **Cách khắc phục**
  4. **Kết quả** *(kèm mã commit GitHub)*

---

## LỘ TRÌNH 9 SPRINT CHI TIẾT

### Sprint 1 (02/11 – 08/11): Khảo sát bài toán & Khởi tạo dự án
* **Mục tiêu:** Hiểu rõ thực tế bài toán đỗ ô tô đô thị, xác định các bên liên quan (Stakeholders) và khởi tạo kho lưu trữ GitHub.
* **Công việc thực hiện:**
  * `Task 1.1`: Khảo sát các nỗi đau thực tế của tài xế ô tô, nhân viên trực cổng và chủ bãi xe; chốt đề tài Smart Parking System (chỉ dành cho ô tô).
  * `Task 1.2`: Khởi tạo repository `smart-parking-system`, thiết lập `.gitignore` và mẫu Issue/PR.
  * `Task 1.3`: Chuẩn bị môi trường phát triển cục bộ trên Windows (Node.js, Python 3.10, GitHub CLI).
* **Đầu ra:** Repo GitHub sẵn sàng, bản tóm lược vấn đề thực tế & các vai trò (`DRIVER`, `STAFF`, `ADMIN`).

---

### Sprint 2 (09/11 – 15/11): Phân tích yêu cầu, Phản biện phạm vi & Thiết kế kiến trúc (SAD)
* **Mục tiêu:** Phản biện làm sạch phạm vi dự án (cắt bỏ phần thừa như MQTT/Node-RED/Docker), thiết kế kiến trúc chuẩn 4+1 View và mô hình CSDL 7 bảng.
* **Công việc thực hiện:**
  * `Task 2.1`: Chốt Phạm vi dự án (In-Scope / Out-of-Scope / Assumptions) và phản biện 6 rủi ro thực tế (đặt theo sức chứa, cấu hình giá linh hoạt theo bãi, nút mở Google Maps, tìm kiếm mờ cổng ra).
  * `Task 2.2`: Viết tài liệu Kiến trúc & Thiết kế Phần mềm `docs/SAD.md` (Quality Scenarios 6 thành phần, Use Case, ERD 7 bảng, State Machine, Sequence, Activity, Deployment).
  * `Task 2.3`: Chuẩn hóa `README.md`, `MoTaCongViec.md` và đồng bộ GitHub Issues theo 9 Sprint.
* **Đầu ra:** Tài liệu `MoTaCongViec.md` và `docs/SAD.md` hoàn chỉnh, thống nhất 100%.

---

### Sprint 3 (16/11 – 22/11): Thiết lập PostgreSQL, Khởi tạo Backend & Xác thực 3 vai trò
* **Mục tiêu:** Chạy được PostgreSQL trên Windows, khởi tạo khung Backend Express + TypeScript + Prisma với 7 bảng dữ liệu mẫu tại TP. Vinh và hoàn thiện Auth/RBAC.
* **Công việc thực hiện:**
  * `Task 3.1`: Cài đặt PostgreSQL trên Windows, khởi tạo `backend/` (Express + TypeScript + Prisma), viết `schema.prisma` gồm 7 bảng và chạy migration đầu tiên.
  * `Task 3.2`: Viết script `seed.ts` tạo sẵn 5 bãi đỗ xe ô tô tọa độ thật tại TP. Vinh (ĐH Vinh, Quảng trường HCM, Ga Vinh, BV Đa khoa, Vincom Vinh), lưới ô 2D và tài khoản mẫu 3 vai trò (`ADMIN`, `STAFF`, `DRIVER`).
  * `Task 3.3`: Xây dựng API Đăng ký, Đăng nhập (JWT + bcrypt), Middleware phân quyền 3 vai trò (khóa `STAFF` theo `assigned_lot_id`) và module Quản lý biển số ô tô (chuẩn hóa + Regex biển số VN).
* **Đầu ra:** Backend chạy ổn định, kết nối PostgreSQL thành công, API Auth & Vehicle hoạt động chuẩn.

---

### Sprint 4 (23/11 – 29/11): Backend — Quản lý bãi xe, Đặt chỗ theo sức chứa & Vận hành Cổng
* **Mục tiêu:** Hoàn thiện toàn bộ API nghiệp vụ cốt lõi (chạy trơn tru 100% trước khi ghép AI).
* **Công việc thực hiện:**
  * `Task 4.1`: Xây dựng API Tìm bãi gần nhất (Haversine theo bán kính + tính số suất trống khả dụng) và API Admin quản lý Bãi xe, Sơ đồ ô 2D, Cấu hình giá linh hoạt (`HOURLY` / `PER_TURN`, `free_minutes`, `base_price`).
  * `Task 4.2`: Xây dựng API Đặt chỗ theo sức chứa (`POST /api/reservations`) có Transaction chống vượt sức chứa, giới hạn chống spam (tối đa 3 lần hủy/quá hạn/ngày) và Cron job tự động hủy đơn quá hạn 30 phút.
  * `Task 4.3`: Xây dựng API Cổng Vào (`check-in`: tự khớp đơn đặt trước hoặc cấp ô vãng lai, hỗ trợ tới trễ chuyển vãng lai, nút *Đổi ô*), API Cổng Ra (`check-out/preview` & `confirm`: tìm kiếm mờ biển số, tính phí động theo cấu hình bãi, lưu snapshot hóa đơn) và API Dashboard thống kê.
* **Đầu ra:** Toàn bộ REST API cốt lõi hoàn chỉnh, đã kiểm thử logic bằng script/Postman.

---

### Sprint 5 (30/11 – 06/12): Frontend — Giao diện Tài xế (Bản đồ TP. Vinh, Chỉ đường & Đặt chỗ)
* **Mục tiêu:** Xây dựng giao diện Web trực quan cho Tài xế ô tô tìm bãi trên bản đồ, xem chỗ trống, đặt chỗ và dẫn đường.
* **Công việc thực hiện:**
  * `Task 5.1`: Khởi tạo `frontend/` (React + TypeScript + Tailwind CSS), xây dựng luồng Đăng nhập/Đăng ký và màn hình Quản lý biển số ô tô cá nhân.
  * `Task 5.2`: Tích hợp bản đồ **Leaflet + OpenStreetMap** tại TP. Vinh: lấy GPS / tìm địa chỉ (Nominatim) / chấm tay giả lập vị trí, hiển thị ghim màu trạng thái bãi (Xanh/Vàng/Đỏ), vẽ tuyến đường **OSRM** kèm `km` & `phút`, và nút **"Mở dẫn đường bằng Google Maps"**.
  * `Task 5.3`: Xây dựng màn hình Chi tiết bãi (sơ đồ ô 2D, bảng giá, số suất trống), form Đặt giữ suất 30 phút (có đồng hồ đếm ngược, nút Hủy) và trang Lịch sử gửi xe.
* **Đầu ra:** Tài xế có thể thực hiện trọn vẹn luồng từ mở Bản đồ $\rightarrow$ Chỉ đường $\rightarrow$ Đặt chỗ $\rightarrow$ Xem lịch sử trên Web.

---

### Sprint 6 (07/12 – 13/12): Frontend — Giao diện Vận hành Cổng (Staff) & Quản trị (Admin)
* **Mục tiêu:** Hoàn thiện màn hình trực cổng cho Nhân viên và trang Quản trị/Thống kê cho Admin.
* **Công việc thực hiện:**
  * `Task 6.1`: Xây dựng màn hình **Vận hành Cổng Vào / Cổng Ra** cho `STAFF`: tải ảnh / chụp từ Webcam, kiểm tra/sửa tay biển số, chỉ định/đổi ô trên sơ đồ 2D, đối chiếu ảnh vào/ra, hiện hóa đơn + mã QR mô phỏng và xác nhận thu tiền.
  * `Task 6.2`: Xây dựng trang **Quản trị Bãi xe** cho `ADMIN`: thêm/sửa bãi bằng cách chấm tọa độ trực tiếp trên bản đồ, cài đặt giá linh hoạt, tạo nhanh lưới ô 2D, bật/tắt bảo trì ô và phân công bãi cho `STAFF`.
  * `Task 6.3`: Xây dựng trang **Dashboard Thống kê**: 4 thẻ KPI, biểu đồ doanh thu 7 ngày và bảng tra cứu lịch sử vào/ra kèm ảnh.
* **Đầu ra:** Hoàn thành 100% MVP không cần AI (toàn bộ hệ thống vận hành khép kín).

---

### Sprint 7 (14/12 – 20/12): Xây dựng & Tích hợp Dịch vụ AI Nhận diện Biển số (ALPR)
* **Mục tiêu:** Xây dựng module AI Python đọc biển số ô tô Việt Nam và ghép nối mượt mà vào luồng Cổng Vào/Ra.
* **Công việc thực hiện:**
  * `Task 7.1`: Thu thập bộ 15–20 ảnh ô tô Việt Nam mẫu (biển dài 1 dòng, biển vuông 2 dòng, điều kiện sáng khác nhau) lưu làm tập test chuẩn.
  * `Task 7.2`: Xây dựng `alpr-service/` (Python FastAPI + OpenCV + EasyOCR): tiền xử lý ảnh, cắt vùng biển số, đọc ký tự 1 dòng & 2 dòng, hậu xử lý sửa lỗi OCR phổ biến và chuẩn hóa theo Regex biển số ô tô VN.
  * `Task 7.3`: Kết nối Backend Node.js $\leftrightarrow$ `alpr-service` (timeout 10s, fallback nhập tay nếu AI lỗi), hoàn thiện cảnh báo độ tin cậy (*Human-in-the-loop*) và *Tìm kiếm mờ (Fuzzy Search)* ở Cổng Ra.
* **Đầu ra:** Chụp/tải ảnh ô tô tại Cổng Vào/Ra $\rightarrow$ hệ thống tự nhận diện biển số, gợi ý khớp xe và tính tiền tự động.

---

### Sprint 8 (21/12 – 27/12): Kiểm thử tổng thể, Sửa lỗi & Viết TROUBLESHOOTING
* **Mục tiêu:** Kiểm thử toàn bộ các kịch bản chất lượng, vá lỗi phát sinh và hoàn thiện trải nghiệm người dùng.
* **Công việc thực hiện:**
  * `Task 8.1`: Kiểm thử chức năng đầu-cuối (End-to-End) và các tình huống biên: đặt chỗ quá sức chứa, tự hủy sau 30 phút, spam hủy quá 3 lần, xe đến trễ, biển số đọc lệch 1 ký tự ở Cổng Ra, mất mạng OSRM.
  * `Task 8.2`: Tối ưu giao diện Responsive (xem mượt trên khung điện thoại cho Tài xế), dọn sạch code thừa và ghi lại các lỗi gặp phải + cách xử lý vào `TROUBLESHOOTING.md`.
  * `Task 8.3`: Chuẩn hóa lại hướng dẫn cài đặt & chạy hệ thống trong `README.md`.
* **Đầu ra:** Hệ thống ổn định, sẵn sàng demo không lỗi, có tài liệu `TROUBLESHOOTING.md`.

---

### Sprint 9 (28/12 – 04/01): Quay Video Demo, Viết Báo cáo & Bảo vệ Đồ án
* **Mục tiêu:** Đóng gói hoàn chỉnh sản phẩm, quay video demo trực quan và hoàn thiện báo cáo thực tập chuyên ngành.
* **Công việc thực hiện:**
  * `Task 9.1`: Quay video demo đầy đủ kịch bản thực tế (Tài xế tìm bãi trên bản đồ TP. Vinh $\rightarrow$ Đặt chỗ $\rightarrow$ Chỉ đường $\rightarrow$ Cổng vào quét biển số AI $\rightarrow$ Cổng ra tính tiền $\rightarrow$ Admin xem doanh thu).
  * `Task 9.2`: Hoàn thiện quyển Báo cáo Đồ án Thực tập Chuyên ngành (trích xuất trực tiếp sơ đồ 4+1 View từ `docs/SAD.md`) và slide thuyết trình.
  * `Task 9.3`: Rà soát cuối cùng trên GitHub (đóng đủ Issues, kiểm tra commit sạch) và bảo vệ đồ án.
* **Đầu ra:** Video demo, Báo cáo hoàn chỉnh, Slide bảo vệ và kho GitHub sạch đẹp.
