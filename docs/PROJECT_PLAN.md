# Kế hoạch triển khai

## 1. Phạm vi và quyết định đã chốt
MVP là nền tảng quản lý tập trung nhiều bãi xe, có mô phỏng IoT và nhận diện biển số từ ảnh/video.
- Không có Organization/Company/Tenant.
- Backend: Express Modular Monolith; không tách nghiệp vụ thành microservices.
- Frontend: React + TypeScript + Tailwind CSS.
- Database: PostgreSQL + Prisma.
- IoT: Node-RED + Mosquitto MQTT; chưa cần phần cứng thật.
- ALPR: Python + FastAPI + OpenCV + OCR adapter; nằm trong phạm vi MVP.
- Backend giữ quyền quyết định nghiệp vụ và là nguồn dữ liệu chuẩn.
- Luồng xe vào/ra thủ công phải chạy độc lập khi ALPR/MQTT tắt.

## 2. Roadmap theo Issue
| Milestone | Issue | Kết quả bàn giao | Điều kiện qua mốc |
|---|---|---|---|
| A – Foundation | #2–#6 | Môi trường, backend/frontend foundation, RBAC, quản lý hạ tầng bãi | DB/API/web chạy được; migration nền tảng và test scope pass |
| B – Manual workflow | #7–#9 | Phương tiện, reservation, session, billing | Staff xử lý xe vào/ra thủ công từ đầu đến cuối |
| C – ALPR & IoT | #10–#12 | OCR, MQTT simulator, luồng cổng tích hợp | Event idempotent, OCR review, barrier ACK/timeout |
| D – Completion | #13–#14 | Dashboard, alerts, CI, test, security, backup, demo | Kịch bản demo chạy trên máy sạch; test quan trọng pass |

Chi tiết và phụ thuộc: [IMPLEMENTATION_BACKLOG.md](IMPLEMENTATION_BACKLOG.md).

## 3. Chiến lược vertical slice
Không xây toàn bộ frontend rồi mới làm backend. Mỗi lát cắt phải chạy được qua giao diện, API và database.

### Slice A – Nghiệp vụ gửi xe thủ công
- Quản lý bãi, khu vực, vị trí, cổng và bảng giá.
- Quản lý phương tiện/reservation.
- Nhân viên check-in, phân chỗ, tra cứu xe đang gửi.
- Check-out, tính phí, ghi nhận thanh toán và giải phóng chỗ.

### Slice B – IoT simulator
- Node-RED phát hiện xe/cảm biến qua MQTT.
- Backend validate event, chống trùng và kiểm tra nghiệp vụ.
- Backend publish command; Node-RED mô phỏng barrier và trả ACK.
- Frontend nhận cập nhật qua Socket.IO sau khi database commit.

### Slice C – ALPR
- Staff upload ảnh qua Express API.
- Express kiểm tra quyền/file và gọi ALPR service nội bộ.
- ALPR trả candidate/confidence; staff xác nhận hoặc nhập tay.
- Backend lưu kết quả và liên kết với phương tiện/session.

### Slice D – Cổng đầu-cuối
- Event xe đến → ALPR/manual review → kiểm tra reservation/sức chứa.
- Transaction tạo session/phân bổ vị trí.
- Backend gửi barrier command; simulator ACK hoặc timeout.
- Xe ra → tính phí/payment → hoàn tất session/giải phóng vị trí.
- Có manual fallback nếu thiết bị hoặc OCR lỗi.

## 4. Kịch bản demo nghiệm thu
1. SYSTEM_ADMIN tạo ít nhất hai bãi xe.
2. SYSTEM_ADMIN phân công manager/staff cho từng bãi.
3. Manager cấu hình khu vực, vị trí và cổng.
4. Driver đăng ký phương tiện hoặc staff sử dụng dữ liệu demo.
5. Nhân viên thực hiện được check-in thủ công khi simulator tắt.
6. Node-RED phát event xe đến cổng qua MQTT.
7. ALPR nhận ảnh mẫu và trả candidate/confidence.
8. Staff xác nhận/sửa biển số; backend kiểm tra quyền vào và sức chứa.
9. Backend tạo session và gửi command mở barrier; simulator trả ACK.
10. Dashboard cập nhật số chỗ trống và xe đang gửi.
11. Xe ra: tính phí, ghi nhận thanh toán, hoàn tất session và giải phóng chỗ.
12. Thử event trùng, OCR thất bại, barrier timeout và truy cập sai bãi.

## 5. Kiểm thử tối thiểu
- Authentication: sai mật khẩu, token hết hạn, refresh token bị thu hồi.
- RBAC/resource scope: role sai nhận 403; manager/staff bãi A không truy cập bãi B.
- Reservation/capacity: request đồng thời không overbook.
- Session: không chiếm trùng vị trí; không check-out session đã hoàn tất.
- Billing: rounding, grace period, invoice snapshot, payment idempotency.
- MQTT: event trùng không xử lý lần hai; payload sai bị từ chối; timeout tạo alert.
- ALPR: ảnh sai định dạng, không đọc được, confidence thấp, nhiều candidate, service offline.
- Media: truy cập ảnh phải qua kiểm tra quyền và retention.
- Realtime: client chỉ nhận dữ liệu trong scope được cấp.

## 6. Definition of Done
- Acceptance Criteria của Issue đạt và test phù hợp pass.
- PR được tự review hoặc Mentor review khi cần; đã merge vào main.
- Tài liệu liên quan phản ánh code đã triển khai.
- Không có secret hoặc media nhạy cảm trong Git.
- Có hướng dẫn cài đặt, seed dữ liệu giả và demo từ môi trường sạch.

## 7. Phạm vi chưa làm trong MVP
Không triển khai thanh toán online thật, mobile app, phần cứng vật lý, microservices nghiệp vụ, Kubernetes, multi-tenant hoặc huấn luyện model OCR riêng.

## 8. Quy trình cá nhân
Dự án do một người trực tiếp phân tích, thiết kế và triển khai. GitHub Issues/PR là công cụ theo dõi công việc cá nhân và nhận review từ Mentor. Nhiệm vụ nhóm trưởng tổng hợp báo cáo tuần và tạo sheet báo cáo nhóm không áp dụng cho dự án này; chỉ lập báo cáo cá nhân nếu Mentor yêu cầu riêng.