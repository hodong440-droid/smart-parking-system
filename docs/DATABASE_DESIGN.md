# Thiết kế cơ sở dữ liệu

## 1. Quyết định nền tảng
- PostgreSQL, quản lý schema bằng Prisma Migrate.
- Khóa chính UUID; thời gian lưu bằng `timestamptz`; tiền dùng `numeric(12,2)`.
- Một hệ thống tập trung quản lý nhiều bãi xe.
- **Không tạo bảng Organization, Company hoặc Tenant.**
- Phân công quản lý/nhân viên vào bãi xe bằng bảng liên kết.

## 2. Nhóm bảng

### Tài khoản và phân quyền
| Bảng | Mục đích | Trường chính |
|---|---|---|
| users | Tài khoản | id, full_name, email, phone, password_hash, status |
| user_parking_lot_assignments | Phân công nhân sự theo bãi | user_id, parking_lot_id, assigned_at, assigned_by |

Role dùng enum `SYSTEM_ADMIN`, `PARKING_MANAGER`, `PARKING_STAFF`, `DRIVER` ở giai đoạn đầu. Một tài khoản có một role chính; nếu sau này cần nhiều role thì chuẩn hóa thành roles/user_roles.

### Hạ tầng bãi xe
| Bảng | Mục đích | Trường chính |
|---|---|---|
| parking_lots | Thông tin từng bãi | id, code, name, address, latitude, longitude, status |
| parking_zones | Khu vực trong bãi | id, parking_lot_id, code, name |
| parking_spaces | Vị trí đỗ | id, parking_zone_id, code, space_type, status |
| parking_gates | Cổng vào/ra | id, parking_lot_id, code, name, gate_type, status |
| pricing_rules | Quy tắc tính phí | id, parking_lot_id, vehicle_type, pricing_type, amount, effective_from, effective_to |

### Phương tiện và gửi xe
| Bảng | Mục đích | Trường chính |
|---|---|---|
| vehicles | Phương tiện của tài xế | id, owner_user_id, plate_number, vehicle_type |
| reservations | Đặt chỗ | id, user_id, vehicle_id, parking_lot_id, start_at, end_at, status |
| parking_sessions | Lượt xe vào/ra | id, parking_lot_id, vehicle_id, space_id, entry_gate_id, exit_gate_id, check_in_at, check_out_at, status, pricing_rule_id |

MVP có thể phân bổ vị trí cụ thể khi xe đến thay vì giữ một vị trí cố định ngay lúc đặt chỗ. Reservation và parking session là hai khái niệm khác nhau.

### Hóa đơn và thanh toán
| Bảng | Mục đích | Trường chính |
|---|---|---|
| invoices | Hóa đơn của lượt gửi xe | id, parking_session_id, subtotal, discount_amount, total_amount, status, issued_at |
| payments | Giao dịch thanh toán | id, invoice_id, amount, method, status, provider_reference, paid_at |

### Thiết bị và giám sát
| Bảng | Mục đích | Trường chính |
|---|---|---|
| simulator_devices | Thiết bị/cảm biến/barie mô phỏng | id, parking_lot_id, gate_id, device_type, code, status, last_seen_at |
| sensor_events | Sự kiện từ cảm biến hoặc simulator | id, device_id, parking_space_id, event_type, payload, occurred_at |
| alerts | Cảnh báo vận hành | id, parking_lot_id, device_id, alert_type, message, status, created_at |
| audit_logs | Nhật ký thao tác quan trọng | id, actor_user_id, action, resource_type, resource_id, metadata, created_at |

## 3. Quan hệ chính
- `parking_lots 1—N parking_zones`
- `parking_zones 1—N parking_spaces`
- `parking_lots 1—N parking_gates`
- `parking_lots N—N users` thông qua `user_parking_lot_assignments` (chỉ áp dụng phân công phạm vi cho manager/staff).
- `users 1—N vehicles`
- `users 1—N reservations`; `vehicles 1—N reservations`; `parking_lots 1—N reservations`
- `vehicles 1—N parking_sessions`; `parking_lots 1—N parking_sessions`; `parking_spaces 1—N parking_sessions` theo lịch sử.
- `parking_sessions 1—0..1 invoices`; `invoices 1—N payments`.
- `parking_lots 1—N simulator_devices`; `simulator_devices 1—N sensor_events`.

## 4. Ràng buộc quan trọng
- Unique `users.email` (chuẩn hóa lowercase trước khi lưu); unique `parking_lots.code`.
- Unique `(parking_zone_id, code)` cho vị trí; unique `(parking_lot_id, code)` cho cổng/khu vực theo quy ước mã.
- Unique `(user_id, parking_lot_id)` trong bảng phân công.
- Biển số cần chuẩn hóa trước khi so sánh; không giả định biển số luôn duy nhất toàn hệ thống nếu nghiệp vụ cho phép xe trùng chủ sở hữu/đổi biển.
- Một vị trí không được có nhiều session đang hoạt động cùng lúc.
- Một xe không được có hai session đang hoạt động trong cùng bãi.
- Reservation có `end_at > start_at`; pricing rule có khoảng hiệu lực hợp lệ.
- Dùng transaction và ràng buộc database phù hợp để tránh overbooking/chiếm trùng vị trí khi có request đồng thời.

## 5. Enum nghiệp vụ đề xuất
- UserStatus: ACTIVE, SUSPENDED, DISABLED.
- ParkingLotStatus: ACTIVE, INACTIVE, MAINTENANCE.
- SpaceStatus: AVAILABLE, OCCUPIED, RESERVED, OUT_OF_SERVICE.
- GateType: ENTRY, EXIT, BOTH.
- ReservationStatus: PENDING, CONFIRMED, CANCELLED, EXPIRED, COMPLETED.
- ParkingSessionStatus: ACTIVE, COMPLETED, CANCELLED.
- InvoiceStatus: UNPAID, PAID, VOID, REFUNDED.
- PaymentStatus: PENDING, SUCCEEDED, FAILED, REFUNDED.
- DeviceStatus: ONLINE, OFFLINE, ERROR, MAINTENANCE.

Các enum và tên trường là đặc tả ban đầu; cần rà soát cùng Prisma schema trước khi tạo migration thật.

## 6. Không đưa vào phiên bản hiện tại
- Không có Organization/Company/Tenant.
- Chưa triển khai subscription/SaaS billing cho doanh nghiệp.
- Chưa bắt buộc AI nhận diện biển số, camera thật hoặc ESP32/barie thật.
- Chưa thiết kế microservices.

## 7. Việc cần xác nhận trước migration
1. Chính sách đặt chỗ: giữ chỗ theo sức chứa hay chọn vị trí cụ thể.
2. Bảng giá: theo giờ, theo lượt, theo ngày hay kết hợp.
3. Quy tắc biển số và loại phương tiện.
4. Có cho phép tài xế thanh toán trực tuyến trong MVP hay chỉ mô phỏng.
