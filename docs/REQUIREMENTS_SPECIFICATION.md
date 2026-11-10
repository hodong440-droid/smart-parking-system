# Đặc tả yêu cầu hệ thống

## 1. Mục đích tài liệu

Tài liệu xác định các yêu cầu chức năng và phi chức năng của Smart Parking System làm căn cứ thiết kế, triển khai và nghiệm thu. Nội dung phản ánh phạm vi MVP đã thống nhất; các yêu cầu chưa được xác minh được ghi rõ là cần quyết định trước khi triển khai.

## 2. Phạm vi hệ thống

Hệ thống là ứng dụng web quản lý tập trung nhiều bãi đỗ xe. Người dùng truy cập qua trình duyệt; backend cung cấp API, xử lý nghiệp vụ và quản lý dữ liệu. Node-RED/Mosquitto mô phỏng thiết bị IoT; dịch vụ ALPR xử lý ảnh/video độc lập.

Không xây dựng lớp Organization/Company/Tenant trong MVP.

## 3. Tác nhân

| Mã | Tác nhân | Mô tả |
|---|---|---|
| ACT-01 | SYSTEM_ADMIN | Quản trị tài khoản, bãi xe và phân công nhân sự |
| ACT-02 | PARKING_MANAGER | Quản lý các bãi được phân công |
| ACT-03 | PARKING_STAFF | Thực hiện nghiệp vụ vận hành tại bãi được phân công |
| ACT-04 | DRIVER | Người sử dụng dịch vụ gửi xe |
| ACT-05 | IoT Simulator | Thiết bị/cổng ảo gửi sự kiện và nhận lệnh |
| ACT-06 | ALPR Service | Dịch vụ xử lý ảnh và trả kết quả nhận diện |

## 4. Yêu cầu chức năng

### 4.1 Tài khoản và phân quyền

| Mã | Yêu cầu | Tác nhân |
|---|---|---|
| FR-001 | Người dùng có thể đăng nhập và đăng xuất khỏi hệ thống | ACT-01–04 |
| FR-002 | Hệ thống quản lý hồ sơ và trạng thái tài khoản | ACT-01–04 |
| FR-003 | Quản trị viên tạo, cập nhật, khóa tài khoản và gán vai trò | ACT-01 |
| FR-004 | Quản trị viên phân công manager/staff vào một hoặc nhiều bãi xe | ACT-01 |
| FR-005 | Backend từ chối truy cập nếu người dùng không có vai trò hoặc không được phân công vào bãi tương ứng | ACT-01–04 |

### 4.2 Quản lý hạ tầng bãi xe

| Mã | Yêu cầu | Tác nhân |
|---|---|---|
| FR-006 | Quản trị viên tạo, cập nhật và ngừng hoạt động bãi xe | ACT-01 |
| FR-007 | Manager quản lý khu vực, vị trí đỗ, cổng và bảng giá trong bãi được giao | ACT-02 |
| FR-008 | Hệ thống theo dõi trạng thái vị trí đỗ: trống, đang sử dụng, tạm khóa hoặc trạng thái tương đương đã chốt | ACT-02–03 |
| FR-009 | Hệ thống cung cấp thông tin sức chứa và số vị trí còn trống | ACT-01–04 |

### 4.3 Phương tiện và đặt chỗ

| Mã | Yêu cầu | Tác nhân |
|---|---|---|
| FR-010 | Driver quản lý danh sách phương tiện của mình | ACT-04 |
| FR-011 | Driver tìm kiếm bãi xe và thông tin sức chứa phù hợp | ACT-04 |
| FR-012 | Driver tạo hoặc hủy reservation theo quy tắc đặt chỗ | ACT-04 |
| FR-013 | Hệ thống ngăn đặt chỗ vượt quá sức chứa theo cơ chế kiểm soát đồng thời | ACT-04 |

### 4.4 Quản lý lượt gửi xe

| Mã | Yêu cầu | Tác nhân |
|---|---|---|
| FR-014 | Staff ghi nhận xe vào bằng thao tác thủ công | ACT-03 |
| FR-015 | Hệ thống kiểm tra phương tiện, reservation và sức chứa trước khi xác nhận xe vào | ACT-03 |
| FR-016 | Hệ thống tạo parking session và ghi nhận thời điểm vào, vị trí đỗ, cổng vào | ACT-03 |
| FR-017 | Staff tra cứu các lượt gửi đang hoạt động | ACT-03 |
| FR-018 | Staff thực hiện xe ra và ghi nhận thời điểm kết thúc lượt gửi | ACT-03 |
| FR-019 | Hệ thống ngăn hoàn tất một session đã kết thúc hoặc giải phóng vị trí không hợp lệ | ACT-03 |

### 4.5 Tính phí và thanh toán

| Mã | Yêu cầu | Tác nhân |
|---|---|---|
| FR-020 | Hệ thống tính phí theo bảng giá có hiệu lực tại thời điểm áp dụng | ACT-03 |
| FR-021 | Hệ thống lập hóa đơn gắn với parking session | ACT-03–04 |
| FR-022 | Staff ghi nhận thanh toán thủ công/mô phỏng và trạng thái thanh toán | ACT-03 |
| FR-023 | Hệ thống lưu lịch sử giao dịch và ngăn ghi nhận trùng cùng một yêu cầu thanh toán | ACT-03 |

### 4.6 Nhận diện biển số

| Mã | Yêu cầu | Tác nhân |
|---|---|---|
| FR-024 | Staff gửi ảnh/video tới hệ thống để xử lý nhận diện | ACT-03 |
| FR-025 | ALPR trả về candidate biển số, confidence và thông tin xử lý | ACT-06 |
| FR-026 | Staff xem, xác nhận hoặc chỉnh sửa biển số nhận diện | ACT-03 |
| FR-027 | Hệ thống lưu kết quả nhận diện và thông tin người xác nhận | ACT-03 |

### 4.7 Mô phỏng IoT và realtime

| Mã | Yêu cầu | Tác nhân |
|---|---|---|
| FR-028 | Simulator phát sự kiện xe đến, xe rời đi hoặc trạng thái cảm biến qua MQTT | ACT-05 |
| FR-029 | Backend xác thực nguồn gửi, kiểm tra payload và chống xử lý trùng event | ACT-05 |
| FR-030 | Backend gửi lệnh barrier và theo dõi ACK/timeout | ACT-05 |
| FR-031 | Frontend nhận cập nhật trạng thái từ backend qua Socket.IO | ACT-01–04 |
| FR-032 | Hệ thống ghi nhận lỗi thiết bị và tạo cảnh báo vận hành | ACT-02–03 |

### 4.8 Thống kê và nhật ký

| Mã | Yêu cầu | Tác nhân |
|---|---|---|
| FR-033 | Dashboard hiển thị số liệu tổng quan theo phạm vi được cấp quyền | ACT-01–02 |
| FR-034 | Hệ thống hỗ trợ tra cứu lịch sử lượt gửi, hóa đơn và thanh toán | ACT-01–03 |
| FR-035 | Hệ thống ghi audit log cho các thao tác quan trọng | ACT-01–03 |

## 5. Yêu cầu phi chức năng

| Mã | Nhóm | Yêu cầu |
|---|---|---|
| NFR-001 | Bảo mật | Mật khẩu phải được băm bằng thuật toán phù hợp; không lưu mật khẩu dạng rõ |
| NFR-002 | Phân quyền | Backend kiểm tra role và parking-lot scope trên mọi API có dữ liệu theo bãi |
| NFR-003 | Toàn vẹn dữ liệu | Các thao tác nhiều bảng phải dùng transaction phù hợp |
| NFR-004 | Đồng thời | Đặt chỗ và phân bổ vị trí không được vượt sức chứa hoặc chiếm trùng |
| NFR-005 | Khả năng phục hồi | Luồng gửi xe thủ công vẫn hoạt động khi MQTT hoặc ALPR ngừng |
| NFR-006 | Tích hợp | MQTT event có định danh duy nhất; command có correlation, ACK và timeout |
| NFR-007 | Khả năng bảo trì | Backend chia module theo nghiệp vụ; controller không chứa toàn bộ business logic |
| NFR-008 | Khả năng kiểm thử | Nghiệp vụ cốt lõi có unit/integration test và dữ liệu thử có thể tái lập |
| NFR-009 | Quyền riêng tư | Ảnh/video có kiểm soát truy cập và thời hạn lưu; không commit media thật vào repository |
| NFR-010 | Khả năng sử dụng | Giao diện hiển thị rõ trạng thái, lỗi và yêu cầu xác nhận của người dùng |

## 6. Quy tắc nghiệp vụ nền tảng

- Một parking lot có nhiều zone; mỗi zone có nhiều parking space.
- Manager/staff chỉ thao tác trong các bãi được phân công.
- Reservation không đồng nghĩa với parking session.
- Một parking session chỉ được hoàn tất một lần.
- Vị trí đang được một session chiếm dụng không được cấp cho session khác.
- Kết quả OCR là dữ liệu gợi ý; người có quyền có thể xác nhận hoặc sửa.
- Backend là thành phần duy nhất quyết định cho phép xe vào/ra.
- Publish lệnh barrier không đồng nghĩa barrier đã thực hiện; phải xử lý ACK hoặc timeout.
- Các trạng thái session/gate operation phải được chốt thành state machine trước khi tạo migration.

## 7. Các quyết định cần xác minh trước khi triển khai

1. Quy tắc bảng giá: theo giờ/theo lượt, đơn vị làm tròn, thời gian miễn phí và mức phí tối thiểu.
2. Reservation giữ capacity hay khóa một vị trí cụ thể; thiết kế hiện tại chọn giữ capacity cho MVP.
3. Một phương tiện có được có nhiều session đang hoạt động tại các bãi khác nhau không.
4. Cách tạo tài khoản Driver và nhân viên; email/điện thoại nào bắt buộc.
5. Payment MVP có hỗ trợ thanh toán một phần/hoàn tiền hay chỉ ghi nhận thanh toán đủ.
6. Thời hạn lưu ảnh/video, dung lượng tối đa và quyền truy cập.
7. Quy trình fallback khi barrier không ACK hoặc ALPR không trả kết quả.

## 8. Truy vết yêu cầu và nghiệm thu

Mỗi FR/NFR phải được liên kết với Issue triển khai và test case tương ứng. Không đánh dấu yêu cầu hoàn thành chỉ vì đã mô tả trong tài liệu. Trạng thái nghiệm thu phải dựa trên mã nguồn và kết quả kiểm thử thực tế.
