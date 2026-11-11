# Smart Parking System
### Nền tảng quản lý tập trung nhiều bãi đỗ xe

[![Status](https://img.shields.io/badge/status-in%20development-orange)]()
[![Architecture](https://img.shields.io/badge/architecture-modular--monolith-blue)]()
[![Backend](https://img.shields.io/badge/backend-Node.js%20%7C%20Express-green)]()
[![Frontend](https://img.shields.io/badge/frontend-React%20%7C%20TypeScript-blue)]()
[![Database](https://img.shields.io/badge/database-PostgreSQL-316192)]()

## 1. Giới thiệu đề tài

**Smart Parking System** là đề tài xây dựng nền tảng web quản lý tập trung nhiều bãi đỗ xe. Hệ thống hướng tới số hóa các hoạt động quản lý bãi xe, kiểm soát phương tiện vào/ra, theo dõi vị trí đỗ, tính phí và tra cứu lịch sử. Đề tài đồng thời nghiên cứu khả năng ứng dụng nhận diện biển số (ALPR) và mô phỏng thiết bị IoT trong quy trình vận hành.

Dự án được thực hiện theo định hướng đồ án/thực tập chuyên ngành Công nghệ thông tin, từ phân tích yêu cầu, thiết kế kiến trúc và cơ sở dữ liệu đến triển khai, kiểm thử và đánh giá.

> **Trạng thái phát triển:** Repository hiện đang ở giai đoạn phân tích, thiết kế và chuẩn bị triển khai. Các chức năng mô tả trong tài liệu là phạm vi/mục tiêu của hệ thống, không phải tuyên bố rằng ứng dụng đã hoàn thành.

## 2. Bài toán và mục tiêu

Hoạt động tại bãi đỗ xe cần quản lý đồng bộ sức chứa, vị trí đỗ, thông tin phương tiện, lượt gửi, hóa đơn và trạng thái thanh toán. Khi dữ liệu được theo dõi rời rạc, nhân viên khó cập nhật tức thời và người quản lý khó tổng hợp tình hình hoạt động.

Mục tiêu của đề tài là xây dựng một hệ thống tập trung giúp:
- Quản lý nhiều bãi xe trên cùng một nền tảng.
- Chuẩn hóa quy trình xe vào, xe ra và tính phí.
- Phân quyền theo vai trò và phạm vi bãi xe.
- Hỗ trợ nhận diện biển số từ ảnh/video, có nhân viên xác nhận kết quả.
- Mô phỏng cổng, barrier và cảm biến thông qua MQTT.
- Cung cấp dashboard, lịch sử và cảnh báo vận hành.

## 3. Phạm vi đề tài

**Trong phạm vi MVP**
- Quản lý tài khoản, vai trò và phân công nhân sự.
- Quản lý bãi xe, khu vực, vị trí đỗ, cổng và bảng giá.
- Quản lý phương tiện, reservation và parking session.
- Check-in/check-out, hóa đơn và ghi nhận thanh toán thủ công/mô phỏng.
- ALPR trên ảnh/video thử nghiệm.
- IoT simulator sử dụng Node-RED và Mosquitto MQTT.
- Dashboard, realtime, audit log và cảnh báo cơ bản.

**Ngoài phạm vi MVP**
- Không có lớp Organization/Company/Tenant.
- Không bắt buộc phần cứng thật.
- Không tích hợp cổng thanh toán trực tuyến thật.
- Không xây dựng mobile app hoặc tách nghiệp vụ thành microservices.

## 4. Đối tượng sử dụng

| Vai trò | Trách nhiệm |
|---|---|
| SYSTEM_ADMIN | Quản lý tài khoản, bãi xe và phân công nhân sự |
| PARKING_MANAGER | Quản lý các bãi xe được giao |
| PARKING_STAFF | Vận hành xe vào/ra và xử lý nghiệp vụ tại bãi |
| DRIVER | Quản lý phương tiện, tìm bãi, đặt chỗ và theo dõi lượt gửi |

Một tài khoản manager/staff có thể được phân công vào nhiều bãi. Backend kiểm tra quyền theo cả vai trò và phạm vi bãi được giao.

## 5. Kiến trúc và công nghệ

| Thành phần | Công nghệ | Vai trò |
|---|---|---|
| Frontend | React, TypeScript, Tailwind CSS | Giao diện web |
| Backend | Node.js, Express, TypeScript | API và nghiệp vụ |
| ORM | Prisma | Truy cập dữ liệu, migration |
| Database | PostgreSQL | Lưu trữ dữ liệu |
| Realtime | Socket.IO | Cập nhật trạng thái |
| MQTT Broker | Eclipse Mosquitto | Trung chuyển thông điệp IoT |
| IoT Simulator | Node-RED | Mô phỏng thiết bị/cổng/barrier |
| ALPR Service | Python, FastAPI, OpenCV, OCR adapter | Nhận diện biển số |
| Local Environment | Docker Compose | Môi trường phát triển |

### Sơ đồ kiến trúc khái quát

```text
React Web --REST/Socket.IO--> Express Modular Monolith <--> PostgreSQL
                                      |
                                  MQTT Client
                                      |
                                Mosquitto Broker
                                      ^
                                      | MQTT
                              Node-RED Simulator

Express API --HTTP nội bộ--> Python FastAPI ALPR
```

Backend là nơi quyết định nghiệp vụ và lưu trạng thái chuẩn. Node-RED chỉ mô phỏng thiết bị; ALPR chỉ cung cấp kết quả nhận diện. Luồng gửi xe thủ công phải hoạt động ngay cả khi MQTT hoặc ALPR không khả dụng.

## 6. Tài liệu dự án

| Tài liệu | Nội dung |
|---|---|
| [Danh mục tài liệu](docs/README.md) | Chỉ mục và quy tắc tổ chức tài liệu |
| [Thuật ngữ](docs/GLOSSARY.md) | Giải thích thuật ngữ nghiệp vụ và kỹ thuật |
| [Sơ đồ hệ thống](docs/diagrams/system-context.mmd) | Sơ đồ ngữ cảnh thành phần |
| [Luồng xe vào](docs/diagrams/vehicle-entry-sequence.mmd) | Sequence diagram minh họa check-in và ACK |
| [Quyết định kiến trúc ADR-001](docs/decisions/ADR-001-modular-monolith.md) | Lý do lựa chọn Modular Monolith |
| [Tổng quan đề tài](docs/PROJECT_OVERVIEW.md) | Bối cảnh, bài toán, mục tiêu, phạm vi, đối tượng và phương pháp |
| [Đặc tả yêu cầu](docs/REQUIREMENTS_SPECIFICATION.md) | Tác nhân, yêu cầu chức năng, phi chức năng và quy tắc nghiệp vụ |
| [Kiến trúc hệ thống](docs/ARCHITECTURE.md) | Thành phần, trách nhiệm, luồng giao tiếp và quyết định kiến trúc |
| [Thiết kế cơ sở dữ liệu](docs/DATABASE_DESIGN.md) | Bảng dữ liệu, trường, quan hệ và ràng buộc |
| [Kế hoạch triển khai](docs/PROJECT_PLAN.md) | Lộ trình Sprint, mục tiêu và kết quả bàn giao từng giai đoạn |
| [Hướng dẫn Issue và Pull Request](docs/ISSUE_AND_PR_GUIDE.md) | Quy chuẩn viết yêu cầu, tiêu chí nghiệm thu và PR |
| [Nhật ký chỉnh sửa tài liệu](docs/CHANGELOG_DOCUMENTATION.md) | Ghi rõ các mục đã chỉnh sửa và lý do |

| [Kế hoạch kiểm thử](docs/TEST_AND_EVALUATION.md) | Chiến lược test, test case và tiêu chí đánh giá |
| [Backlog](docs/IMPLEMENTATION_BACKLOG.md) | Danh sách công việc và phụ thuộc giữa các Issue |
| [Quy trình cá nhân](docs/PERSONAL_WORKFLOW.md) | Cách quản lý Issue, branch, commit và Pull Request |

## 7. Cấu trúc repository dự kiến

```text
smart-parking-system/
├── frontend/             # React application
├── backend/              # Express API và Prisma
├── alpr-service/         # Python FastAPI và xử lý ảnh
├── iot/
│   ├── mosquitto/        # MQTT broker configuration
│   └── node-red/         # IoT simulation flows
├── infra/                # Docker Compose và cấu hình môi trường
└── docs/                 # Tài liệu phân tích, thiết kế, kiểm thử
```

Đây là cấu trúc mục tiêu; chỉ những thư mục và thành phần thực sự có trong repository mới được xem là đã triển khai.

## 8. Lộ trình thực hiện

1. **Foundation:** môi trường, backend/frontend nền tảng, xác thực và phân quyền.
2. **Core parking workflow:** hạ tầng bãi, phương tiện, reservation, session và billing.
3. **Smart integration:** ALPR, MQTT, Node-RED và luồng barrier.
4. **Completion:** dashboard, realtime, kiểm thử, bảo mật, backup và demo.

Tiến độ thực tế được theo dõi qua [GitHub Issues](../../issues) và Pull Requests. Issue chỉ đóng sau khi chức năng đạt tiêu chí nghiệm thu và thay đổi được merge.

## 9. Cách chạy dự án

**Hiện chưa có hướng dẫn chạy ứng dụng hoàn chỉnh** vì source code và cấu hình môi trường chưa được triển khai. Hướng dẫn cài đặt sẽ được bổ sung cùng các Issue nền tảng sau khi Docker Compose, backend và frontend có thể khởi chạy được.

## 10. Định hướng đánh giá

Kết quả đề tài sẽ được đánh giá theo mức độ đáp ứng yêu cầu, tính đúng đắn nghiệp vụ, phân quyền, toàn vẹn dữ liệu, khả năng chịu lỗi, mức độ tích hợp ALPR/IoT, kiểm thử và khả năng tái lập demo.

---

**Lưu ý học thuật:** Tài liệu thiết kế thể hiện giải pháp dự kiến. Báo cáo kết quả phải phân biệt rõ nội dung đã phân tích, đã thiết kế, đã lập trình, đã kiểm thử và chưa hoàn thành.
