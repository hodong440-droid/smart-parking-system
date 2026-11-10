# Smart Parking System

Nền tảng quản lý tập trung nhiều bãi đỗ xe, kết hợp mô phỏng IoT và nhận diện biển số bằng xử lý ảnh.

> **Trạng thái hiện tại:** repository đang ở giai đoạn đặc tả và chuẩn bị triển khai. Chưa có ứng dụng nghiệp vụ chạy được, chưa có Prisma migration, MQTT flow hoặc ALPR service đã kiểm thử.

## 1. Phạm vi đã chốt
- Một nền tảng quản lý tập trung nhiều bãi xe tại các địa điểm khác nhau.
- Không có Organization, Company, Tenant hoặc lớp quản lý doanh nghiệp.
- Mô phỏng IoT mức 2: website + Node-RED + Mosquitto MQTT; chưa yêu cầu phần cứng thật.
- ALPR nằm trong phạm vi MVP: Python + OpenCV + OCR, chạy trên ảnh/video thử nghiệm.
- Có thể tích hợp ESP32, cảm biến và barrier vật lý ở giai đoạn mở rộng mà không viết lại nghiệp vụ lõi.

## 2. Công nghệ mục tiêu
| Thành phần | Công nghệ | Trách nhiệm |
|---|---|---|
| Frontend | React, TypeScript, Tailwind CSS | Giao diện Admin, Manager, Staff, Driver |
| Backend | Node.js, Express, TypeScript | API, nghiệp vụ, phân quyền, điều phối |
| Database | PostgreSQL + Prisma | Dữ liệu nghiệp vụ và lịch sử |
| Realtime | Socket.IO | Cập nhật trạng thái tới trình duyệt |
| MQTT broker | Mosquitto | Chuyển tiếp event/command |
| IoT simulator | Node-RED | Mô phỏng cổng, barrier và cảm biến |
| ALPR | Python, FastAPI, OpenCV, OCR adapter | Nhận ảnh/video và trả candidate biển số |
| Local environment | Docker Compose | Khởi chạy các dịch vụ nền |

OCR engine chỉ được chốt sau khi benchmark trên bộ ảnh biển số Việt Nam phù hợp. Không tuyên bố accuracy trước khi đo.

## 3. Vai trò hệ thống
- `SYSTEM_ADMIN`: quản trị tài khoản, bãi xe và phân công nhân sự.
- `PARKING_MANAGER`: quản lý các bãi được giao.
- `PARKING_STAFF`: xử lý xe vào/ra và vận hành tại bãi được giao.
- `DRIVER`: quản lý phương tiện, tìm bãi, đặt chỗ, theo dõi lượt gửi và thanh toán.

Backend phải kiểm tra cả role và phạm vi bãi xe được phân công. Ẩn nút trên frontend không phải cơ chế bảo mật.

## 4. Phạm vi chức năng MVP
1. Tài khoản, xác thực, RBAC và phân công nhân sự theo bãi.
2. Quản lý nhiều bãi xe, khu vực, vị trí đỗ, cổng và bảng giá.
3. Quản lý phương tiện, reservation và parking session.
4. Xe vào/ra, tính phí, hóa đơn và ghi nhận thanh toán MVP.
5. Sơ đồ bãi xe, dashboard và realtime.
6. Node-RED Parking Simulator giao tiếp với backend qua MQTT.
7. ALPR đọc ảnh/video; lưu candidate, confidence và cho phép nhân viên xác nhận/sửa.
8. Nhật ký, cảnh báo và báo cáo vận hành.

## 5. Kiến trúc tổng quan
```text
React Web --REST/Socket.IO--> Express Modular Monolith <--> PostgreSQL
                                      |
                                 MQTT Client
                                      |
                                Mosquitto Broker
                                      ^
                                      | MQTT
                              Node-RED Simulator

Express API --internal HTTP--> Python FastAPI ALPR
```

Backend là nguồn dữ liệu chuẩn cho trạng thái nghiệp vụ. MQTT chỉ truyền thông điệp thiết bị. Node-RED mô phỏng thiết bị; ALPR chỉ trả kết quả nhận diện, không tự tạo session hoặc mở barrier.

## 6. Nguyên tắc kỹ thuật
- Không tạo bảng Organization/Company/Tenant.
- Một tài khoản manager/staff có thể được phân công vào nhiều bãi.
- Reservation, parking session, recognition result và device event là các khái niệm riêng.
- MQTT event có eventId; backend xác thực device/gate và xử lý idempotent.
- Chỉ backend quyết định nghiệp vụ; barrier chỉ được điều khiển sau khi backend chấp thuận.
- OCR là gợi ý, phải có manual review khi confidence thấp hoặc kết quả không chắc chắn.
- Không lưu binary ảnh/video trong PostgreSQL; chỉ lưu metadata và storage key.
- Không commit secret, token, ảnh biển số thật hoặc dữ liệu cá nhân vào Git.
- Luồng gửi xe thủ công phải hoạt động khi MQTT/ALPR tắt.

## 7. Cấu trúc repository mục tiêu
```text
smart-parking-system/
├── frontend/                 # React + TypeScript
├── backend/                  # Express API + Prisma
├── alpr-service/             # Python + FastAPI + OpenCV + OCR
├── iot/
│   ├── mosquitto/            # Broker config
│   └── node-red/             # Simulator flows
├── infra/                    # Docker Compose
├── docs/
└── README.md
```

Đây là cấu trúc đích, không phải danh sách thư mục đã tồn tại.

## 8. Tài liệu và quản lý công việc
- [Kiến trúc hệ thống](docs/ARCHITECTURE.md)
- [Thiết kế cơ sở dữ liệu](docs/DATABASE_DESIGN.md)
- [Kế hoạch triển khai](docs/PROJECT_PLAN.md)
- [Backlog triển khai](docs/IMPLEMENTATION_BACKLOG.md)
- [Quy trình quản lý công việc cá nhân](docs/PERSONAL_WORKFLOW.md)

Đây là dự án cá nhân. GitHub Issues/PR dùng để tự quản lý công việc và hỗ trợ Mentor review; không áp dụng phân công thành viên hay trách nhiệm nhóm trưởng tổng hợp báo cáo tuần.

## 9. Thứ tự triển khai
1. Docker Compose, PostgreSQL và Mosquitto.
2. Backend foundation và Prisma.
3. Frontend foundation.
4. Authentication/RBAC.
5. Nghiệp vụ gửi xe thủ công: hạ tầng bãi → phương tiện/reservation → session → invoice/payment.
6. ALPR và MQTT/Node-RED sau khi luồng thủ công ổn định.
7. Tích hợp đầu-cuối, dashboard, kiểm thử và đóng gói.

## 10. Definition of Done cho MVP
- Quản lý tập trung được nhiều bãi xe, không có tenant layer.
- RBAC và resource scope được kiểm tra ở backend.
- Xe vào/ra, phân bổ chỗ, tính phí và hóa đơn chạy được.
- Node-RED trao đổi MQTT hai chiều với backend; event idempotent, command có ACK/timeout.
- ALPR xử lý ảnh/video mẫu, trả candidate/confidence và có bước nhân viên xác nhận.
- Dashboard cập nhật từ dữ liệu đã commit.
- Có test cho luồng chính, phân quyền và các tình huống lỗi.
- Có hướng dẫn cài đặt/demo; tài liệu phản ánh đúng trạng thái code thực tế.

## 11. Quy ước GitHub
- `main` là nhánh ổn định; phát triển qua branch theo Issue và Pull Request.
- Một Issue đại diện một kết quả có thể nghiệm thu; không tạo Issue cho từng file/hàm.
- PR phải liên kết đúng Issue bằng `Closes #<number>` và nêu bằng chứng kiểm thử.
- Chỉ đóng Issue sau khi Acceptance Criteria đạt và PR đã merge.
- Repository Private; cấp quyền Mentor bằng đúng GitHub username khi có thông tin tài khoản.
- Tài liệu kiến trúc là thiết kế mục tiêu, không đồng nghĩa tính năng đã được code hoặc kiểm thử.