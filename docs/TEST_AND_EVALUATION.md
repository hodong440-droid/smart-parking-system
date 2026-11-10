# Kế hoạch kiểm thử và đánh giá

## 1. Mục đích

Tài liệu xác định cách kiểm thử và tiêu chí đánh giá Smart Parking System. Đây là kế hoạch kiểm thử; các test case chỉ được đánh dấu đạt sau khi có kết quả chạy thực tế và bằng chứng tương ứng.

## 2. Chiến lược kiểm thử

| Cấp độ | Mục tiêu | Công cụ dự kiến |
|---|---|---|
| Unit Test | Kiểm tra hàm và quy tắc nghiệp vụ độc lập | Vitest/Jest, pytest |
| Integration Test | Kiểm tra API, database, transaction và tích hợp | Supertest, Prisma test DB, pytest |
| Contract/Integration MQTT | Kiểm tra topic, payload, event trùng và ACK | MQTT client, Mosquitto test broker |
| End-to-End Test | Kiểm tra luồng người dùng từ giao diện đến database | Playwright |
| Manual Acceptance Test | Đánh giá kịch bản demo và khả năng sử dụng | Checklist nghiệm thu |

Tên framework là lựa chọn dự kiến; cần thống nhất khi khởi tạo source code để tránh sử dụng nhiều công cụ trùng chức năng.

## 3. Môi trường kiểm thử

- Dùng database riêng cho test, không dùng dữ liệu cá nhân hoặc dữ liệu production.
- Seed dữ liệu giả: nhiều bãi xe, các vai trò, phương tiện, vị trí và bảng giá.
- Mosquitto và Node-RED chạy trong môi trường mô phỏng.
- ALPR sử dụng bộ ảnh/video mẫu có quyền sử dụng phù hợp; không công bố ảnh biển số thật nếu chưa được phép.
- Mọi test phải có thể chạy lại và không phụ thuộc trạng thái dữ liệu của lần chạy trước.

## 4. Danh sách kịch bản kiểm thử

| Mã | Nhóm | Kịch bản | Kết quả mong đợi |
|---|---|---|---|
| TC-AUTH-01 | Authentication | Đăng nhập với thông tin hợp lệ | Tạo phiên/token hợp lệ |
| TC-AUTH-02 | Authentication | Đăng nhập sai mật khẩu | Từ chối, không tiết lộ thông tin nhạy cảm |
| TC-AUTH-03 | Authorization | Người dùng không có role phù hợp gọi API quản trị | Trả về 403 |
| TC-SCOPE-01 | Resource scope | Manager bãi A truy cập dữ liệu bãi B | Bị từ chối |
| TC-LOT-01 | Parking lot | Tạo hai bãi xe độc lập | Dữ liệu được lưu và phân biệt đúng |
| TC-SPACE-01 | Capacity | Hai yêu cầu đồng thời lấy cùng một vị trí | Chỉ một yêu cầu thành công |
| TC-RES-01 | Reservation | Đặt chỗ vượt sức chứa | Bị từ chối |
| TC-SESSION-01 | Session | Check-in xe hợp lệ | Tạo session và chiếm vị trí đúng |
| TC-SESSION-02 | Session | Check-out session đã hoàn tất | Không xử lý lần hai |
| TC-BILL-01 | Billing | Tính phí theo bảng giá và thời gian gửi | Tổng tiền đúng quy tắc đã chốt |
| TC-PAY-01 | Payment | Gửi lại cùng idempotency key | Không ghi nhận thanh toán trùng |
| TC-ALPR-01 | ALPR | Ảnh rõ, có biển số | Trả candidate và confidence |
| TC-ALPR-02 | ALPR | Ảnh mờ/không có biển số | Trả trạng thái không chắc chắn hoặc thất bại có kiểm soát |
| TC-ALPR-03 | ALPR | Nhân viên sửa kết quả OCR | Lưu biển số xác nhận và người thao tác |
| TC-MQTT-01 | MQTT | Gửi event hợp lệ | Backend nhận và xử lý đúng |
| TC-MQTT-02 | MQTT | Gửi lại eventId đã xử lý | Không thực hiện nghiệp vụ lần hai |
| TC-MQTT-03 | MQTT | Payload sai hoặc device không hợp lệ | Từ chối và ghi log phù hợp |
| TC-GATE-01 | Gate | Backend gửi command, simulator ACK | Cập nhật trạng thái operation đúng |
| TC-GATE-02 | Gate | Simulator không ACK | Timeout, cảnh báo và không giả định barrier đã mở |
| TC-FALLBACK-01 | Resilience | Tắt MQTT và ALPR | Nhân viên vẫn xử lý xe vào/ra thủ công |
| TC-REALTIME-01 | Realtime | Người dùng nhận cập nhật trạng thái | Chỉ nhận dữ liệu trong phạm vi quyền |
| TC-AUDIT-01 | Audit | Thay đổi bảng giá hoặc xác nhận biển số | Có audit log phù hợp |

## 5. Kịch bản nghiệm thu tổng thể

1. Tạo hai bãi xe trên cùng hệ thống.
2. Tạo manager/staff và phân công vào từng bãi.
3. Kiểm tra manager bãi thứ nhất không thể truy cập dữ liệu bãi thứ hai.
4. Cấu hình zone, space, gate và pricing rule.
5. Tạo phương tiện và reservation.
6. Tắt simulator/ALPR, thực hiện một lượt gửi xe thủ công hoàn chỉnh.
7. Bật Node-RED/Mosquitto và phát sự kiện xe đến.
8. Gửi ảnh mẫu tới ALPR; nhân viên xác nhận kết quả nhận diện.
9. Backend kiểm tra điều kiện, tạo operation và gửi command barrier.
10. Simulator phản hồi ACK; frontend nhận cập nhật sau khi backend lưu dữ liệu.
11. Thực hiện xe ra, tính phí, ghi nhận thanh toán và giải phóng vị trí.
12. Thử event trùng, payload sai, OCR không đọc được và barrier timeout.

## 6. Tiêu chí đánh giá

- Tính đúng đắn chức năng: các yêu cầu ưu tiên cao có test đạt.
- Tính toàn vẹn: không tạo lượt gửi trùng, không cấp trùng vị trí, không ghi nhận thanh toán trùng.
- Tính bảo mật: kiểm tra role và resource scope ở backend.
- Tính chịu lỗi: có manual fallback khi tích hợp ngoài không khả dụng.
- Tính tích hợp: luồng MQTT và ALPR có xử lý lỗi, log và trạng thái rõ ràng.
- Tính dễ bảo trì: module, schema, migration và tài liệu có cấu trúc nhất quán.
- Tính tái lập: người khác có thể cài đặt và chạy demo theo hướng dẫn.

## 7. Báo cáo kết quả kiểm thử

Khi bắt đầu chạy test, ghi nhận theo mẫu:

| Test ID | Ngày chạy | Phiên bản/commit | Kết quả | Bằng chứng | Ghi chú |
|---|---|---|---|---|---|
| TC-... | YYYY-MM-DD | commit SHA | Pass/Fail/Blocked | Link log/screenshot | Lỗi hoặc điều kiện |

Không điền Pass trước khi test được thực thi. Khi test thất bại, tạo Issue bug và liên kết test case.
