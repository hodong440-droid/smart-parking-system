# Thiết kế cơ sở dữ liệu

## 1. Quyết định nền tảng

- PostgreSQL, quản lý schema bằng Prisma Migrate.
- Khóa chính UUID; thời gian lưu bằng `timestamptz`; tiền dùng `numeric(12,2)`.
- Một hệ thống tập trung quản lý nhiều bãi xe.
- **Không tạo bảng Organization, Company hoặc Tenant.**
- Phân công quản lý/nhân viên vào bãi xe bằng bảng liên kết.
- Ảnh/video không lưu dạng binary trong PostgreSQL; database chỉ lưu metadata và storage key/path có kiểm soát.

## 2. Nhóm bảng

### 2.1 Tài khoản và phân quyền

| Bảng | Mục đích | Trường chính |
|---|---|---|
| users | Tài khoản | id, full_name, email, phone, password_hash, role, status |
| user_parking_lot_assignments | Phân công nhân sự theo bãi | id, user_id, parking_lot_id, assigned_at, assigned_by |
| refresh_sessions | Phiên refresh token có thể thu hồi | id, user_id, token_hash, expires_at, revoked_at, created_at |

Role dùng enum `SYSTEM_ADMIN`, `PARKING_MANAGER`, `PARKING_STAFF`, `DRIVER` ở giai đoạn đầu. Một tài khoản có một role chính; chỉ manager/staff cần bản ghi phân công bãi xe.

### 2.2 Hạ tầng bãi xe

| Bảng | Mục đích | Trường chính |
|---|---|---|
| parking_lots | Thông tin từng bãi | id, code, name, address, latitude, longitude, status |
| parking_zones | Khu vực trong bãi | id, parking_lot_id, code, name |
| parking_spaces | Vị trí đỗ | id, parking_zone_id, code, space_type, status |
| parking_gates | Cổng vào/ra | id, parking_lot_id, code, name, gate_type, status |
| pricing_rules | Quy tắc tính phí | id, parking_lot_id, vehicle_type, pricing_type, amount, effective_from, effective_to |

### 2.3 Phương tiện và gửi xe

| Bảng | Mục đích | Trường chính |
|---|---|---|
| vehicles | Phương tiện của tài xế | id, owner_user_id, plate_number, normalized_plate, vehicle_type, status |
| reservations | Đặt chỗ | id, user_id, vehicle_id, parking_lot_id, start_at, end_at, status |
| parking_sessions | Lượt xe vào/ra | id, parking_lot_id, vehicle_id, space_id, entry_gate_id, exit_gate_id, check_in_at, check_out_at, status, pricing_rule_id |

Reservation và parking session là hai khái niệm khác nhau. MVP có thể phân bổ vị trí cụ thể khi xe đến thay vì giữ một vị trí cố định ngay lúc đặt chỗ.

### 2.4 Hóa đơn và thanh toán

| Bảng | Mục đích | Trường chính |
|---|---|---|
| invoices | Hóa đơn của lượt gửi xe | id, parking_session_id, subtotal, discount_amount, total_amount, status, issued_at |
| payments | Giao dịch thanh toán | id, invoice_id, amount, method, status, provider_reference, paid_at |

### 2.5 Thiết bị, MQTT và simulator

| Bảng | Mục đích | Trường chính |
|---|---|---|
| devices | Thiết bị thật hoặc mô phỏng | id, parking_lot_id, gate_id, space_id, code, device_type, source_type, status, last_seen_at |
| device_events | Sự kiện thiết bị nhận qua MQTT | id, event_id, device_id, event_type, topic, payload_json, occurred_at, received_at, processing_status |
| device_commands | Lệnh gửi tới thiết bị/barrier | id, command_id, device_id, command_type, payload_json, status, issued_at, acknowledged_at, expires_at |
| alerts | Cảnh báo vận hành | id, parking_lot_id, device_id, alert_type, message, status, created_at |

`event_id` và `command_id` cần unique. Payload lưu dạng JSONB để giữ metadata có cấu trúc; không dùng JSONB thay cho các cột quan hệ cần truy vấn thường xuyên.

### 2.6 Nhận diện biển số (ALPR)

| Bảng | Mục đích | Trường chính |
|---|---|---|
| recognition_jobs | Yêu cầu xử lý ảnh/video | id, parking_lot_id, gate_id, direction, source_type, status, correlation_id, requested_by, created_at, completed_at |
| recognition_results | Kết quả OCR theo từng job | id, job_id, candidate_plate, normalized_plate, confidence, engine_name, engine_version, processing_time_ms, review_status, confirmed_plate, reviewed_by, reviewed_at |
| media_assets | Metadata ảnh/video bằng chứng | id, parking_lot_id, storage_key, media_type, file_size, captured_at, expires_at, created_at |

Một job có thể có nhiều candidate result. Nếu chỉ cần một kết quả ở MVP, vẫn giữ cấu trúc này để sau này có thể lưu top-N candidate. Ảnh/video được lưu trong local storage khi phát triển; có thể chuyển sang object storage sau này. Không lưu binary trong DB và không commit media thật vào Git.

### 2.7 Nhật ký

| Bảng | Mục đích | Trường chính |
|---|---|---|
| audit_logs | Nhật ký thao tác quan trọng | id, actor_user_id, action, resource_type, resource_id, metadata, created_at |

## 3. Quan hệ chính

- `parking_lots 1—N parking_zones`; `parking_zones 1—N parking_spaces`.
- `parking_lots 1—N parking_gates`.
- `parking_lots N—N users` thông qua `user_parking_lot_assignments` (áp dụng cho manager/staff).
- `users 1—N vehicles`; `users 1—N reservations`.
- `vehicles 1—N reservations`; `parking_lots 1—N reservations`.
- `vehicles 1—N parking_sessions`; `parking_lots 1—N parking_sessions`; `parking_spaces 1—N parking_sessions` theo lịch sử.
- `parking_sessions 1—0..1 invoices`; `invoices 1—N payments`.
- `parking_lots 1—N devices`; `devices 1—N device_events`; `devices 1—N device_commands`.
- `parking_lots 1—N recognition_jobs`; `recognition_jobs 1—N recognition_results`.
- `recognition_jobs N—1 media_assets` hoặc liên kết media theo thiết kế quan hệ cụ thể khi tạo Prisma schema.
- `parking_lots 1—N media_assets`.

## 4. Ràng buộc và index quan trọng

- Unique `users.email` sau khi chuẩn hóa lowercase.
- Unique `parking_lots.code`.
- Unique `(parking_zone_id, code)` cho vị trí; unique `(parking_lot_id, code)` cho cổng và khu vực.
- Unique `(user_id, parking_lot_id)` trong bảng phân công.
- Unique `devices.code` theo phạm vi toàn hệ thống hoặc unique `(parking_lot_id, code)`; chọn một quy ước nhất quán trước migration.
- Unique `device_events.event_id`; unique `device_commands.command_id`.
- Index `(parking_lot_id, status)` trên parking_spaces và parking_sessions.
- Index `(parking_lot_id, normalized_plate, status)` hoặc cấu trúc tương đương để tra cứu lượt xe đang hoạt động.
- Partial unique index cho session ACTIVE theo vị trí; cân nhắc thêm partial unique index theo `(parking_lot_id, vehicle_id)` cho session ACTIVE.
- Unique `invoices.parking_session_id` nếu nghiệp vụ chỉ cho một hóa đơn chính trên mỗi lượt.
- Index `device_events(received_at)`, `device_events(processing_status)`, `recognition_jobs(created_at, status)`.
- Reservation phải có `end_at > start_at`; pricing rule có khoảng hiệu lực hợp lệ.
- Dùng transaction và constraint phù hợp để tránh overbooking/chiếm trùng vị trí khi có request đồng thời.

## 5. Enum nghiệp vụ đề xuất

- UserStatus: ACTIVE, SUSPENDED, DISABLED.
- UserRole: SYSTEM_ADMIN, PARKING_MANAGER, PARKING_STAFF, DRIVER.
- ParkingLotStatus: ACTIVE, INACTIVE, MAINTENANCE.
- SpaceStatus: AVAILABLE, OCCUPIED, RESERVED, OUT_OF_SERVICE.
- GateType: ENTRY, EXIT, BOTH.
- ReservationStatus: PENDING, CONFIRMED, CANCELLED, EXPIRED, COMPLETED.
- ParkingSessionStatus: ACTIVE, COMPLETED, CANCELLED.
- InvoiceStatus: UNPAID, PAID, VOID, REFUNDED.
- PaymentStatus: PENDING, SUCCEEDED, FAILED, REFUNDED.
- DeviceType: GATE_CONTROLLER, BARRIER, ENTRY_SENSOR, SPACE_SENSOR, CAMERA.
- DeviceSourceType: SIMULATOR, PHYSICAL.
- DeviceStatus: ONLINE, OFFLINE, ERROR, MAINTENANCE.
- DeviceEventStatus: RECEIVED, PROCESSED, IGNORED, FAILED.
- DeviceCommandStatus: PENDING, ACKNOWLEDGED, TIMED_OUT, FAILED.
- RecognitionJobStatus: QUEUED, PROCESSING, COMPLETED, FAILED.
- RecognitionReviewStatus: PENDING, CONFIRMED, REJECTED, MANUAL_ENTRY.
- VehicleDirection: ENTRY, EXIT.

Tên enum và trường là đặc tả ban đầu; rà soát cùng Prisma schema trước khi tạo migration.

## 6. Các nguyên tắc dữ liệu cần giữ

1. PostgreSQL là nguồn dữ liệu chuẩn cho trạng thái chỗ, lượt gửi, hóa đơn và thanh toán.
2. MQTT event phải có eventId duy nhất; lưu event trước/đồng thời với xử lý nghiệp vụ để chống xử lý trùng.
3. Không tạo parking session chỉ vì OCR đọc được một biển số; phải qua kiểm tra nghiệp vụ và xác nhận khi cần.
4. Kết quả OCR cần giữ candidate, confidence, engine/version và thông tin người xác nhận.
5. Không lưu ảnh nhị phân trong DB; chỉ lưu storage key/path và metadata.
6. Không lưu mật khẩu, refresh token dạng rõ hoặc credential MQTT dạng rõ.
7. Cân nhắc retention policy và quyền truy cập đối với ảnh/video biển số.

## 7. Không đưa vào phiên bản hiện tại

- Không có Organization/Company/Tenant.
- Chưa triển khai subscription/SaaS billing.
- Chưa bắt buộc ESP32, camera vật lý hoặc barrier thật.
- Chưa thiết kế microservices cho nghiệp vụ.
- Chưa tích hợp cổng thanh toán thật; MVP có thể mô phỏng giao dịch.

## 8. Việc cần xác nhận trước Prisma migration

1. Chính sách đặt chỗ: giữ chỗ theo sức chứa hay chọn vị trí cụ thể.
2. Bảng giá: theo giờ, theo lượt, theo ngày hay kết hợp.
3. Quy tắc chuẩn hóa biển số Việt Nam và xử lý xe đổi biển/trùng biển.
4. Thời hạn lưu ảnh/video và ai được phép xem/xóa.
5. Một job ALPR có cần giữ top-N candidate hay chỉ candidate tốt nhất.
6. Phương thức thanh toán MVP: mô phỏng, tiền mặt xác nhận tại quầy hay tích hợp cổng thật.
