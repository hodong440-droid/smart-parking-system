# Kế hoạch triển khai

## 1. Phạm vi mục tiêu

Phiên bản đầu tiên là **nền tảng quản lý tập trung nhiều bãi xe + mô phỏng IoT + nhận diện biển số từ ảnh/video**.

Quyết định đã chốt:
- Không có Organization/Company/Tenant.
- Backend nghiệp vụ là Express Modular Monolith.
- Website React + TypeScript.
- PostgreSQL + Prisma.
- Node-RED + Mosquitto MQTT mô phỏng cổng, barrier và cảm biến.
- Python + FastAPI + OpenCV + OCR engine nhận diện biển số ngay trong MVP.
- Chưa cần ESP32, cảm biến vật lý hoặc barrier thật.

## 2. Roadmap theo thứ tự phụ thuộc

| Giai đoạn | Công việc | Sản phẩm bàn giao / điều kiện hoàn thành |
|---|---|---|
| 0. Đặc tả | Chốt kiến trúc, DB, MQTT contract, ALPR API contract, quy ước code | Tài liệu nhất quán; không còn mâu thuẫn về phạm vi |
| 1. Môi trường | Tạo cấu trúc repo; Docker Compose; PostgreSQL; Mosquitto; cấu hình env | Các service nền khởi động được; health check hoạt động |
| 2. Backend foundation | Express + TypeScript, Prisma, config, logging, error handling, validation | API health; kết nối DB; migration đầu tiên |
| 3. Frontend foundation | React + TypeScript + Tailwind, routing, layout, API client | Web chạy được và gọi API |
| 4. Auth & access control | Đăng nhập, refresh rotation, logout, role, phân công bãi xe, scope middleware | Test 401/403 và truy cập chéo bãi xe |
| 5. Parking infrastructure | CRUD bãi xe, khu vực, vị trí, cổng, bảng giá | Quản lý nhiều bãi trong một hệ thống |
| 6. Vehicle & reservation | CRUD phương tiện, chuẩn hóa biển số, tìm bãi, đặt/hủy chỗ | Luồng đặt chỗ và kiểm tra sức chứa |
| 7. Parking session | Check-in/check-out, phân bổ/giải phóng chỗ, tính phí | Luồng gửi xe hoạt động bằng thao tác thủ công |
| 8. Billing | Invoice, payment record, xác nhận thanh toán mô phỏng/tiền mặt | Tính phí, hóa đơn và lịch sử giao dịch |
| 9. ALPR service | FastAPI, OpenCV, OCR adapter, upload ảnh, trả candidate/confidence, test ảnh mẫu | Nhận diện được bộ ảnh kiểm thử; có xử lý lỗi và nhập tay |
| 10. MQTT & simulator | Mosquitto auth/ACL, Node-RED flows, event/command contract, idempotency, ACK/timeout | Mô phỏng xe đến, cảm biến, barrier ảo và event trùng |
| 11. Kết nối luồng đầu-cuối | Nối ALPR + MQTT + backend + session + Socket.IO + UI cổng | Một kịch bản xe vào/ra chạy xuyên suốt không cần phần cứng |
| 12. Dashboard & alerts | Sơ đồ bãi, công suất, lượt xe, doanh thu, trạng thái thiết bị, cảnh báo | Dashboard realtime phản ánh dữ liệu backend |
| 13. Hardening | Test nghiệp vụ, quyền, race condition, bảo mật media, log correlationId, backup | Checklist test và lỗi nghiêm trọng được xử lý |
| 14. Đóng gói | Hướng dẫn cài đặt, seed demo, dữ liệu ảnh/video được phép sử dụng, báo cáo | Có thể cài trên máy khác và trình diễn |

## 3. Chiến lược triển khai theo lát cắt (vertical slice)

Không nên xây hết frontend rồi mới làm backend hoặc làm simulator tách biệt quá lâu. Mỗi lát cắt phải chạy được từ giao diện đến database.

### Slice A – Nghiệp vụ bãi xe tối thiểu
- Tạo bãi xe, khu vực, vị trí và cổng.
- Hiển thị sơ đồ vị trí.
- Nhân viên tạo lượt xe vào thủ công.
- Xe ra, tính phí và giải phóng vị trí.

### Slice B – Mô phỏng IoT
- Node-RED tạo xe đến cổng và cảm biến vị trí.
- Publish MQTT event theo contract.
- Backend validate, chống event trùng, cập nhật nghiệp vụ.
- Backend publish barrier command; Node-RED phản hồi ACK.
- Web nhận Socket.IO event sau khi DB commit.

### Slice C – Nhận diện biển số
- Upload ảnh tại màn hình cổng.
- Backend tạo recognition job và gọi Python service.
- Python trả candidate + confidence.
- Nhân viên xác nhận/sửa biển số.
- Backend liên kết xe, reservation và session; không tự động cho xe vào chỉ dựa trên OCR.

### Slice D – Tích hợp đầu-cuối
- Node-RED mô phỏng xe đến.
- Ảnh mẫu được đưa qua ALPR.
- Nhân viên xác nhận khi cần.
- Backend kiểm tra quyền vào và chỗ trống.
- Tạo session, mở barrier ảo, cập nhật dashboard.
- Mô phỏng xe ra, tính phí, thanh toán và giải phóng chỗ.

## 4. Kịch bản demo bắt buộc

1. SYSTEM_ADMIN tạo ít nhất hai bãi xe.
2. SYSTEM_ADMIN phân công manager/staff cho từng bãi.
3. Manager tạo khu vực, vị trí và cổng.
4. Driver đăng ký phương tiện hoặc staff dùng dữ liệu demo.
5. Node-RED phát sự kiện xe đến cổng vào.
6. ALPR nhận ảnh mẫu, trả candidate biển số và confidence.
7. Staff xác nhận hoặc nhập lại biển số.
8. Backend kiểm tra reservation/sức chứa, cấp vị trí và tạo parking session.
9. Backend gửi lệnh mở barrier; simulator ACK.
10. Dashboard cập nhật số chỗ trống và xe đang gửi.
11. Mô phỏng xe ra; hệ thống tính phí, ghi nhận thanh toán, đóng session và giải phóng chỗ.
12. Kiểm thử event MQTT gửi lặp, OCR thất bại, barrier timeout và user truy cập sai bãi.

## 5. Test tối thiểu

- Auth: sai mật khẩu, token hết hạn, refresh token bị thu hồi.
- RBAC: role không đủ quyền nhận 403.
- Scope: manager/staff bãi A không đọc/sửa dữ liệu bãi B.
- Capacity: hai xe đồng thời tranh một vị trí không được chiếm trùng.
- Session: không có hai session ACTIVE trái phép cho cùng xe/vị trí.
- MQTT: event gửi lặp chỉ được xử lý một lần; payload sai bị từ chối; command timeout tạo alert.
- ALPR: ảnh không hợp lệ, không đọc được biển số, nhiều candidate, confidence thấp, service offline.
- Manual review: sửa biển số phải lưu người xác nhận và thời điểm.
- Realtime: client chỉ nhận dữ liệu trong phạm vi được phép.
- Media: người không có quyền không thể tải ảnh bằng cách đoán URL/path.

## 6. Thứ tự ưu tiên thực tế

1. Hoàn thiện nền tảng backend/frontend và database.
2. Hoàn thiện luồng xe vào/ra thủ công trước.
3. Làm ALPR service độc lập và kiểm thử trên bộ ảnh thực tế được phép sử dụng.
4. Làm MQTT + Node-RED simulator.
5. Nối các luồng thành demo đầu-cuối.
6. Cuối cùng mới tối ưu dashboard, analytics và giao diện.

Lý do: nếu nghiệp vụ session/chỗ đỗ chưa ổn định, thêm MQTT và AI chỉ làm tăng số nơi có thể phát sinh lỗi.

## 7. Definition of Done cho MVP

- Quản lý được nhiều bãi xe tập trung, không có tenant layer.
- RBAC và resource scope được kiểm tra ở backend.
- Xe vào/ra, phân bổ chỗ, tính phí và hóa đơn chạy được.
- Simulator Node-RED trao đổi MQTT hai chiều với backend.
- Backend xử lý event idempotent và có ACK/timeout cho command.
- ALPR xử lý ảnh/video mẫu, trả candidate/confidence và cho phép nhân viên xác nhận.
- Dashboard cập nhật realtime từ trạng thái đã lưu trong database.
- Có test cho các tình huống chính và tình huống lỗi.
- Có hướng dẫn cài đặt/demo; không tuyên bố nhận diện chính xác tuyệt đối nếu chưa có kết quả benchmark.


## 8. Quy trình triển khai và theo dõi dự án cá nhân

Dự án do một người trực tiếp phân tích, thiết kế và triển khai. GitHub Issues dùng để quản lý từng chức năng/lỗi; mỗi Issue phải có Input, Output, Technical Approach và Acceptance Criteria. Công việc được triển khai theo từng Issue, tạo branch/PR và chỉ đóng Issue sau khi nghiệm thu.

Các yêu cầu về nhóm trưởng tổng hợp báo cáo tuần, tạo sheet mới và gửi trước 14:00 Thứ Bảy là hướng dẫn chung cho nhóm, không phải trách nhiệm của chủ dự án cá nhân này. Chỉ lập báo cáo cá nhân nếu Mentor yêu cầu riêng.

Chi tiết: [PERSONAL_WORKFLOW.md](PERSONAL_WORKFLOW.md).


## 9. Roadmap thực thi cho dự án cá nhân

Roadmap cũ mô tả đầy đủ đích đến nhưng dễ khiến một người triển khai quá nhiều nhánh cùng lúc. Thứ tự dưới đây là thứ tự thực hiện; không mở rộng sang giai đoạn sau khi giai đoạn trước chưa có kết quả chạy được.

### Milestone A – Nền tảng chạy được
1. #2 Docker Compose: PostgreSQL và Mosquitto; Node-RED có thể chạy riêng ban đầu.
2. #3 Backend foundation: Express, TypeScript, Prisma, health check, logging, validation.
3. #4 Frontend foundation: React, TypeScript, Tailwind, routing, API client.
4. #5 Auth/RBAC và resource scope.

Điều kiện qua mốc: frontend gọi được backend; backend kết nối DB; migration chạy trên database rỗng; test 401/403 và scope cơ bản.

### Milestone B – Nghiệp vụ gửi xe thủ công
5. #6 Quản lý bãi, khu vực, vị trí, cổng và bảng giá.
6. #7 Quản lý phương tiện và reservation.
7. #8 Check-in/check-out, phân bổ chỗ, trạng thái session.
8. #9 Tính phí, invoice và payment record mô phỏng/tiền mặt.

Điều kiện qua mốc: staff tiếp nhận xe bằng nhập tay, phân chỗ, tìm lượt đang gửi, tính phí, ghi nhận thanh toán và cho xe ra. Luồng này phải hoạt động khi MQTT/ALPR tắt.

### Milestone C – Tích hợp mô phỏng và nhận diện
9. #10 ALPR service: chỉ tích hợp sau khi luồng thủ công ổn định; OCR có manual review.
10. #11 MQTT + Node-RED: event/command, idempotency, ACK/timeout.
11. #12 Kết nối cổng đầu-cuối: kết hợp ảnh ALPR, MQTT, session và barrier ảo.

Điều kiện qua mốc: simulator chỉ phát tín hiệu; backend quyết định nghiệp vụ. Có fallback thủ công khi ALPR/MQTT/barrier lỗi.

### Milestone D – Hoàn thiện sản phẩm
12. #13 Dashboard/realtime/alerts và lịch sử hoạt động.
13. #14 Test tổng thể, bảo mật, CI, backup/restore và tài liệu triển khai/demo.

Điều kiện hoàn tất: kịch bản demo chạy trên máy mới theo README; test quan trọng pass; không còn lỗi nghiêm trọng về phân quyền, chiếm chỗ hoặc tính tiền.

## 10. Quy tắc quản lý phạm vi
- Chỉ có một Issue lớn đang In Progress tại một thời điểm; việc nhỏ có thể nằm trong cùng vertical slice.
- Không tạo Issue cho từng file/hàm. Một Issue đại diện một kết quả người dùng/đầu ra kỹ thuật có thể nghiệm thu.
- Không tạo Issue “hoàn thành toàn bộ hệ thống” quá lớn; tách theo vertical slice.
- Không đóng Issue chỉ vì code đã viết; phải đạt Acceptance Criteria, test phù hợp và merge.
- ALPR và MQTT vẫn thuộc mục tiêu demo đã chốt, nhưng không được chặn việc hoàn thiện luồng gửi xe thủ công.
- Chưa triển khai thanh toán online thật, app mobile, phần cứng thật, microservices, Kubernetes hoặc multi-tenant.

## 11. Các quyết định nghiệp vụ cần chốt trước migration
1. Reservation MVP giữ suất theo capacity hay khóa vị trí cụ thể (khuyến nghị giữ suất theo capacity).
2. Bảng giá MVP: theo giờ hay theo lượt; cách làm tròn và grace period.
3. Một xe có được đồng thời có nhiều session ACTIVE trong các bãi khác nhau không.
4. Email/phone nào bắt buộc đối với DRIVER; cách tạo tài khoản staff.
5. Thanh toán MVP có hỗ trợ nhiều lần/partial payment hay chỉ một lần xác nhận đủ tiền.
6. Thời hạn lưu ảnh/video và người có quyền xem/xóa.
7. Lưu UTC và hiển thị Asia/Ho_Chi_Minh.
