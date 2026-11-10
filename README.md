# Smart Parking System

Nền tảng quản lý tập trung nhiều bãi đỗ xe, kết hợp mô phỏng IoT và nhận diện biển số bằng xử lý ảnh.

> **Trạng thái repository:** hiện đang ở giai đoạn đặc tả kiến trúc và kế hoạch. Chưa có ứng dụng chạy được, chưa có Prisma migration, MQTT flow hoặc dịch vụ AI được triển khai.

## 1. Phạm vi đã chốt

- Một nền tảng quản lý tập trung nhiều bãi đỗ xe tại các địa điểm khác nhau.
- **Không có** Organization, Company, Tenant hoặc lớp quản lý doanh nghiệp.
- Mô phỏng mức 2: website + Node-RED + MQTT Mosquitto; chưa yêu cầu phần cứng thật.
- Nhận diện biển số được đưa vào phiên bản đầu tiên bằng Python + OpenCV + OCR, chạy trên máy tính với ảnh/video thử nghiệm.
- Thiết kế để có thể kết nối ESP32, cảm biến và barrier thật trong giai đoạn mở rộng mà không phải viết lại nghiệp vụ lõi.

## 2. Công nghệ mục tiêu

| Thành phần | Công nghệ | Trách nhiệm |
|---|---|---|
| Web frontend | React, TypeScript, Tailwind CSS | Giao diện quản trị, tài xế, sơ đồ bãi xe |
| Backend | Node.js, Express, TypeScript | API, nghiệp vụ, phân quyền, điều phối |
| Database | PostgreSQL + Prisma | Dữ liệu nghiệp vụ và lịch sử |
| Realtime | Socket.IO | Đẩy trạng thái bãi xe tới trình duyệt |
| IoT messaging | MQTT Mosquitto | Truyền sự kiện thiết bị và lệnh mô phỏng |
| IoT flow | Node-RED | Tạo kịch bản xe vào/ra, cảm biến, barrier ảo |
| ALPR service | Python, FastAPI, OpenCV, OCR engine | Nhận ảnh/video và trả kết quả đọc biển số |
| Local development | Docker Compose | Chạy PostgreSQL, MQTT broker và các thành phần phụ trợ |

OCR engine sẽ được đóng sau một adapter để có thể thay đổi PaddleOCR/Tesseract hoặc engine phù hợp sau khi benchmark dữ liệu biển số Việt Nam. Không khóa kiến trúc vào một engine trước khi kiểm thử.

## 3. Vai trò hệ thống

- `SYSTEM_ADMIN`: quản trị toàn hệ thống, quản lý tài khoản, bãi xe và phân công nhân sự.
- `PARKING_MANAGER`: quản lý hoạt động của các bãi xe được giao.
- `PARKING_STAFF`: xử lý xe vào/ra và vận hành tại bãi được giao.
- `DRIVER`: quản lý phương tiện, tìm bãi, đặt chỗ, theo dõi lượt gửi và thanh toán.

Backend phải kiểm tra cả role lẫn phạm vi bãi xe được phân công. Không dùng việc ẩn nút trên frontend làm cơ chế bảo mật.

## 4. Phạm vi chức năng phiên bản đầu

1. Tài khoản, xác thực, RBAC và phân công nhân sự theo bãi.
2. Quản lý nhiều bãi xe, khu vực, vị trí đỗ, cổng và bảng giá.
3. Quản lý phương tiện, đặt chỗ và lượt gửi xe.
4. Quy trình xe vào/ra, tính phí, hóa đơn và thanh toán mô phỏng.
5. Sơ đồ bãi xe và dashboard cập nhật thời gian thực.
6. Parking Simulator bằng Node-RED; cảm biến và barrier ảo giao tiếp qua MQTT.
7. Dịch vụ nhận diện biển số từ ảnh/video; lưu kết quả, độ tin cậy và ảnh bằng chứng theo chính sách lưu trữ.
8. Nhật ký sự kiện, cảnh báo và báo cáo vận hành.

## 5. Kiến trúc ở mức cao

```text
React Web
   | REST + Socket.IO
   v
Express API (Modular Monolith) ------ PostgreSQL
   | MQTT publish/subscribe                 |
   v                                        |
Mosquitto MQTT Broker                       |
   ^                                        |
   | MQTT                                   |
Node-RED Parking Simulator -----------------+
   |
   | HTTP image request / recognition result
   v
Python FastAPI ALPR Service
(OpenCV + OCR engine)
```

Backend là nguồn dữ liệu chuẩn cho trạng thái nghiệp vụ. MQTT dùng để truyền thông điệp thiết bị, không thay thế PostgreSQL. Node-RED mô phỏng thiết bị; Python ALPR xử lý ảnh/video và không tự ý cập nhật trạng thái chỗ đỗ.

## 6. Nguyên tắc nghiệp vụ và kỹ thuật

- Không tạo bảng Organization/Company/Tenant.
- Một tài khoản quản lý/nhân viên có thể được phân công vào nhiều bãi xe.
- Reservation, parking session, recognition result và sensor event là các khái niệm riêng.
- Mọi MQTT event cần eventId để chống xử lý trùng; backend xác thực device/gate, kiểm tra dữ liệu và ghi nhận trước khi phát realtime.
- Chỉ mở barrier ảo sau khi backend chấp thuận nghiệp vụ; không để Node-RED tự quyết định trạng thái thanh toán hoặc chỗ đỗ.
- Kết quả OCR là dữ liệu gợi ý. Luồng MVP phải cho phép nhân viên xác nhận/sửa biển số khi độ tin cậy thấp.
- Không lưu ảnh nhị phân trong PostgreSQL; lưu file ở vùng lưu trữ riêng và chỉ lưu metadata/path.
- Không commit secret, token, ảnh biển số thật hoặc dữ liệu cá nhân vào Git.

## 7. Cấu trúc repository mục tiêu

```text
smart-parking-system/
├── frontend/                 # React + TypeScript
├── backend/                  # Express API + Prisma
├── alpr-service/             # Python + FastAPI + OpenCV + OCR
├── iot/
│   ├── mosquitto/            # MQTT broker config
│   └── node-red/             # Flows và cấu hình simulator
├── infra/                    # Docker Compose và cấu hình local
├── docs/
│   ├── ARCHITECTURE.md
│   ├── DATABASE_DESIGN.md
│   └── PROJECT_PLAN.md
└── README.md
```

Đây là cấu trúc mục tiêu, không phải danh sách thư mục đã tồn tại.

## 8. Tài liệu

- [Kiến trúc hệ thống](docs/ARCHITECTURE.md)
- [Thiết kế cơ sở dữ liệu](docs/DATABASE_DESIGN.md)
- [Kế hoạch triển khai](docs/PROJECT_PLAN.md)
- [Quy trình quản lý công việc cá nhân và Issue/PR](docs/PERSONAL_WORKFLOW.md)

> Repository này phục vụ một dự án cá nhân. Issue/PR được dùng để tự quản lý công việc và hỗ trợ Mentor review khi cần; không áp dụng phân công thành viên hay trách nhiệm nhóm trưởng báo cáo tuần.

## 9. Tiêu chí hoàn thành MVP

- Tạo và quản lý được nhiều bãi xe trong cùng hệ thống.
- Tài khoản chỉ truy cập đúng phạm vi được phân quyền.
- Mô phỏng được xe đến cổng, nhận diện/nhập biển số, kiểm tra điều kiện, mở barrier ảo, cấp chỗ, xe ra và tính phí.
- MQTT event được backend xử lý idempotent; trạng thái web cập nhật realtime.
- ALPR nhận ảnh/video mẫu, trả biển số và confidence; có bước nhân viên xác nhận.
- Có test cho luồng xe vào/ra, phân quyền, event trùng và nhận diện thất bại.
- README và tài liệu phản ánh đúng chức năng đã thực sự chạy được.
