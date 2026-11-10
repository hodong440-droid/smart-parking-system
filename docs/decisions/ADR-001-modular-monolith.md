# ADR-001: Lựa chọn kiến trúc Modular Monolith

- **Trạng thái:** Accepted (phạm vi MVP)
- **Ngày:** 2026-11-11
- **Phạm vi:** Backend nghiệp vụ Smart Parking System

## Bối cảnh

Đề tài do một người phát triển trong phạm vi đồ án/thực tập. Hệ thống cần nhiều module nghiệp vụ (tài khoản, bãi xe, phương tiện, reservation, session, billing, thiết bị, nhận diện và báo cáo), nhưng quy mô triển khai ban đầu chưa đủ để biện minh cho chi phí vận hành microservices.

ALPR cần runtime/thư viện xử lý ảnh Python; Node-RED/Mosquitto phục vụ mô phỏng IoT. Đây là các thành phần hỗ trợ có ranh giới kỹ thuật khác với backend nghiệp vụ.

## Quyết định

- Backend nghiệp vụ dùng Node.js + Express + TypeScript theo Modular Monolith.
- PostgreSQL là nguồn dữ liệu chuẩn; Prisma quản lý truy cập dữ liệu và migration.
- ALPR là service Python FastAPI độc lập, chỉ nhận ảnh/metadata và trả kết quả nhận diện.
- Node-RED mô phỏng thiết bị; Mosquitto làm MQTT broker.
- Frontend React giao tiếp với backend qua REST/Socket.IO; không kết nối trực tiếp MQTT.
- Không triển khai Organization/Company/Tenant và không tách nghiệp vụ thành microservices trong MVP.

## Lý do

- Giảm độ phức tạp triển khai, debug và vận hành cho dự án một người.
- Dễ quản lý transaction xuyên các nghiệp vụ cốt lõi.
- Vẫn tách được phần xử lý ảnh Python và mô phỏng thiết bị IoT.
- Có thể mở rộng module hoặc tách service sau này nếu có số liệu và nhu cầu thực tế.

## Hệ quả

- Cần giữ ranh giới module, không để controller chứa business logic.
- Các module không truy cập chéo tùy tiện; giao tiếp qua service/application boundary.
- Cần kiểm thử tích hợp để tránh coupling ẩn trong monolith.
- Nếu tải hoặc yêu cầu triển khai thay đổi đáng kể, phải tạo ADR mới thay vì tự ý chuyển sang microservices.
