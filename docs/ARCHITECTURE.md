# Kiến trúc hệ thống

## 1. Quyết định kiến trúc

Sử dụng **Modular Monolith + hai dịch vụ hỗ trợ độc lập**:

1. **Express API** là Modular Monolith, giữ toàn bộ nghiệp vụ và quyền truy cập dữ liệu.
2. **Node-RED + Mosquitto MQTT** mô phỏng thiết bị IoT và luồng sự kiện.
3. **Python FastAPI ALPR** xử lý ảnh/video và trả kết quả nhận diện biển số.

Không tách backend nghiệp vụ thành microservices ở giai đoạn này. Hai thành phần IoT/ALPR được tách riêng vì chúng có runtime và thư viện chuyên biệt, không phải để chia nhỏ nghiệp vụ một cách hình thức.

Phạm vi sản phẩm là một nền tảng quản lý tập trung nhiều bãi xe. **Không có Organization/Company/Tenant**.

## 2. Sơ đồ tổng quan

```text
                         +----------------------+
                         | React + TypeScript    |
                         | Admin / Manager /     |
                         | Staff / Driver        |
                         +----------+-----------+
                                    |
                            REST + Socket.IO
                                    |
                         +----------v-----------+
                         | Express API           |
                         | Modular Monolith      |
                         | Auth, Parking,        |
                         | Sessions, Billing,    |
                         | Devices, ALPR results |
                         +----+-------------+----+
                              |             |
                         Prisma ORM     MQTT Client
                              |             |
                      +-------v---+   +-----v-----------+
                      | PostgreSQL|   | Mosquitto MQTT  |
                      +-----------+   +-----+-----------+
                                            ^
                                            | MQTT
                                   +--------+---------+
                                   | Node-RED         |
                                   | Parking Simulator|
                                   +------------------+

                         +----------------------+
                         | Python FastAPI ALPR  |
                         | OpenCV + OCR engine  |
                         +----------------------+
                                    ^
                                    | HTTP image upload/reference
                           Express API / staff UI
```

## 3. Trách nhiệm và ranh giới thành phần

### 3.1 React frontend

- Giao diện quản trị hệ thống, quản lý bãi xe, quản lý nhân sự.
- Giao diện manager/staff theo phạm vi bãi được phân công.
- Sơ đồ bãi xe: khu vực, vị trí, cổng; màu sắc phản ánh trạng thái từ backend.
- Màn hình vận hành cổng: xe chờ vào/ra, ảnh, biển số OCR, confidence, thao tác xác nhận/sửa.
- Màn hình simulator: chạy kịch bản, gửi xe vào/ra, xem trạng thái thiết bị ảo.
- Nhận cập nhật qua Socket.IO; không tự coi thao tác phía trình duyệt là trạng thái chính thức.

### 3.2 Express API – Modular Monolith

Các module nghiệp vụ:

- **auth**: đăng nhập, refresh token, logout, thu hồi phiên.
- **users**: hồ sơ, role, phân công bãi xe.
- **parking-lots**: bãi xe, khu vực, vị trí, cổng, bảng giá.
- **vehicles**: phương tiện và biển số chuẩn hóa.
- **reservations**: tìm kiếm, đặt/hủy chỗ.
- **parking-sessions**: check-in/check-out, phân bổ vị trí, tính thời gian.
- **billing**: hóa đơn và thanh toán.
- **devices**: thiết bị, MQTT events, trạng thái thiết bị, command.
- **recognition**: tạo job ALPR, lưu kết quả, xác nhận biển số.
- **alerts**: cảnh báo vận hành và theo dõi trạng thái xử lý cảnh báo thiết bị.
- **analytics**: công suất, lượt xe, doanh thu.

Controller chỉ điều phối request/response. Quy tắc nghiệp vụ nằm trong service. Dùng Prisma transaction khi một hành động thay đổi nhiều bảng.

### 3.3 Mosquitto MQTT

- Là message broker cho simulator và backend.
- Không lưu trạng thái nghiệp vụ chuẩn; PostgreSQL mới là nguồn dữ liệu chuẩn.
- Dùng MQTT QoS 1 cho event cần độ tin cậy phù hợp; do QoS 1 có thể giao trùng, backend bắt buộc xử lý idempotent.
- Dùng username/password hoặc cơ chế xác thực phù hợp, ACL giới hạn topic theo thiết bị; không để broker mở anonymous trên mạng công khai.
- Không gửi ảnh/video dạng binary qua MQTT. Chỉ gửi metadata và image reference có thời hạn; ảnh được upload qua HTTP hoặc vùng lưu trữ riêng.

### 3.4 Node-RED Parking Simulator

Node-RED mô phỏng các thực thể:

- Cổng vào/cổng ra.
- Barrier ảo: CLOSED, OPENING, OPEN, CLOSING, ERROR.
- Cảm biến phát hiện xe tại cổng.
- Cảm biến hiện diện tại vị trí đỗ.
- Kịch bản xe đến, xe rời đi, thiết bị offline, event gửi trùng, cảm biến lỗi.

Node-RED phát event qua MQTT và nhận command từ backend. Node-RED **không tự ghi trực tiếp PostgreSQL** và không tự quyết định một lượt xe đã được phép vào hay chưa.

### 3.5 Python FastAPI ALPR Service

- Nhận ảnh upload hoặc image reference hợp lệ.
- Tiền xử lý ảnh bằng OpenCV: kiểm tra ảnh, resize, cân bằng sáng/crop khi cần.
- Chạy OCR engine thông qua adapter; có thể benchmark PaddleOCR/Tesseract trên ảnh biển số Việt Nam trước khi chốt engine.
- Trả danh sách candidate, biển số chuẩn hóa, confidence, thời gian xử lý và trạng thái.
- Không truy cập trực tiếp PostgreSQL; Express API chịu trách nhiệm lưu kết quả và liên kết với vehicle/session.
- Nếu OCR confidence thấp hoặc có nhiều candidate, chuyển sang trạng thái cần nhân viên xác nhận.
- Không dùng kết quả OCR đơn lẻ để tự động từ chối người dùng hoặc khẳng định danh tính chủ xe.

API mục tiêu:
- `GET /health`
- `POST /v1/recognitions` – nhận ảnh và metadata (gateId, direction, correlationId).
- `GET /v1/recognitions/{requestId}` – chỉ cần nếu triển khai xử lý bất đồng bộ.

Ban đầu có thể xử lý đồng bộ để giảm độ phức tạp. Chuyển sang queue/background worker nếu thời gian xử lý gây nghẽn API.

## 4. MQTT topic và message contract

Topic đề xuất:

| Topic | Hướng | Mục đích |
|---|---|---|
| `parking/{parkingLotCode}/gates/{gateCode}/events` | Node-RED → API | Xe đến, cảm biến, trạng thái barrier |
| `parking/{parkingLotCode}/gates/{gateCode}/commands` | API → Node-RED | Yêu cầu mở/đóng barrier ảo |
| `parking/{parkingLotCode}/spaces/{spaceCode}/events` | Simulator → API | Trạng thái cảm biến vị trí |
| `parking/{parkingLotCode}/devices/{deviceCode}/status` | Simulator → API | Heartbeat, online/offline |
| `parking/{parkingLotCode}/devices/{deviceCode}/acks` | Node-RED → API | Xác nhận command |

Payload event tối thiểu:

```json
{
  "eventId": "uuid",
  "eventType": "VEHICLE_DETECTED",
  "occurredAt": "2026-01-01T10:00:00.000Z",
  "parkingLotCode": "LOT-01",
  "gateCode": "GATE-IN-01",
  "deviceCode": "SIM-CAMERA-01",
  "correlationId": "uuid",
  "data": {
    "direction": "ENTRY",
    "imageRef": null,
    "plateCandidate": null
  }
}
```

Quy tắc:
- `eventId` duy nhất; lưu unique trong database để loại event trùng.
- Validate topic, deviceCode, parkingLotCode và gateCode có liên kết hợp lệ.
- Timestamp do thiết bị gửi chỉ là thời điểm khai báo; backend cũng ghi `receivedAt`.
- Command phải có `commandId`, `correlationId`, thời hạn hiệu lực và ACK/timeout.
- Không dùng payload MQTT làm nguồn xác thực người dùng.
- Các topic ở trên là quy ước đề xuất, cần được cấu hình đồng nhất trong Node-RED và backend.

## 5. Luồng xe vào

1. Node-RED mô phỏng xe đến cổng và phát `VEHICLE_DETECTED` qua MQTT.
2. Express nhận message, kiểm tra schema, device/gate và eventId.
3. Backend tạo event record; event trùng được bỏ qua an toàn.
4. Nếu có ảnh, backend gọi ALPR service (hoặc nhận kết quả từ màn hình upload ảnh).
5. ALPR trả candidate biển số + confidence. Backend lưu recognition result.
6. Nếu confidence đạt ngưỡng cấu hình và biển số khớp vehicle/reservation, hệ thống đề xuất xe tương ứng; nếu không, nhân viên xác nhận thủ công.
7. Backend kiểm tra reservation (nếu có), quyền vào bãi, sức chứa và chỗ khả dụng.
8. Backend tạo gate operation ở trạng thái PENDING và giữ chỗ/capacity theo transaction; chưa ghi nhận xe đã vào bãi.
9. Backend publish barrier command có commandId/correlationId và thời hạn hiệu lực.
10. Node-RED mô phỏng barrier, trả ACK/FAILED; backend đối chiếu command, xử lý trùng và timeout.
11. Chỉ khi nhận ACK hợp lệ, backend mới hoàn tất bước chuyển session sang ACTIVE và xác nhận vị trí đang sử dụng. Nếu thất bại/timeout, giữ trạng thái chờ hoặc chuyển quy trình fallback thủ công có audit; không giả định xe đã vào.

## 6. Luồng xe ra

1. Nhận diện hoặc nhập biển số xe tại cổng ra.
2. Backend tìm parking session ACTIVE trong đúng bãi xe.
3. Tính phí theo pricing rule áp dụng; hiển thị phí để nhân viên/tài xế xác nhận.
4. Ghi nhận thanh toán (MVP có thể dùng trạng thái mô phỏng hoặc xác nhận thủ công).
5. Backend tạo gate operation EXIT ở trạng thái PENDING; chưa hoàn tất session hoặc giải phóng vị trí.
6. Backend gửi command mở barrier; Node-RED phản hồi ACK/FAILED.
7. Chỉ khi ACK hợp lệ (hoặc nhân viên xác nhận fallback có audit), backend mới hoàn tất session, ghi nhận thanh toán theo chính sách đã chốt và giải phóng vị trí trong transaction.
8. Timeout/FAILED tạo alert và giữ trạng thái có thể đối soát; không tự đánh dấu xe đã rời bãi.

## 7. Nhận diện biển số: luồng và kiểm soát

- Nguồn ảnh MVP: upload ảnh tĩnh và video mẫu đã chuẩn bị; camera thật là mở rộng sau.
- Ảnh không truyền trực tiếp trong MQTT. Dùng HTTP multipart hoặc storage reference được bảo vệ.
- Lưu candidate, normalizedPlate, confidence, engine/version, processingTimeMs, capturedAt, gate, direction và reviewStatus.
- Trạng thái review: PENDING, CONFIRMED, REJECTED, MANUAL_ENTRY.
- Nhân viên có thể sửa kết quả; lưu người xác nhận và thời điểm xác nhận.
- Confidence threshold là cấu hình cần hiệu chỉnh bằng tập ảnh thử nghiệm, không đặt một con số tùy tiện rồi coi là độ chính xác thực tế.
- Ảnh biển số có thể là dữ liệu cá nhân trong bối cảnh sử dụng; cần giới hạn quyền xem, thời hạn lưu, mục đích sử dụng và không đưa ảnh thật vào Git/demo công khai nếu chưa được phép.

## 8. Realtime và tính nhất quán

- PostgreSQL là nguồn trạng thái chuẩn cho chỗ đỗ, session, invoice và payment.
- MQTT truyền event/command; Socket.IO chỉ thông báo cho frontend sau khi backend đã xử lý và commit dữ liệu.
- Dùng transaction cho phân bổ/giải phóng chỗ và tạo session.
- Dùng unique constraint/partial index phù hợp để ngăn nhiều session ACTIVE trên cùng vị trí hoặc cùng xe trong một bãi.
- Xử lý event trùng bằng unique eventId; lưu trạng thái xử lý để có thể retry có kiểm soát.
- Không publish event thành công trước khi transaction commit. Với độ tin cậy cao hơn, bổ sung transactional outbox ở giai đoạn hardening.

## 9. Phân quyền và phạm vi dữ liệu

Role:
- `SYSTEM_ADMIN`
- `PARKING_MANAGER`
- `PARKING_STAFF`
- `DRIVER`

Hai lớp kiểm tra bắt buộc:
1. RBAC: role có được phép thực hiện hành động không?
2. Resource scope: manager/staff có được phân công vào parkingLot đích không?

Không tin userId, role hoặc parkingLotId do client tự gửi. Simulator/device dùng credential riêng, không giả danh user. Không chỉ ẩn nút frontend để bảo vệ API.

## 10. Authentication mục tiêu

1. Đăng nhập qua HTTPS; kiểm tra password hash.
2. Cấp access token ngắn hạn và refresh token có thể thu hồi.
3. Lưu hash refresh token/session trong database; hỗ trợ rotation.
4. Middleware xác thực token, RBAC và resource scope.
5. Logout thu hồi refresh token.
6. Trả 401 khi chưa xác thực; 403 khi không đủ quyền.

Đây là thiết kế mục tiêu, chưa phải chức năng đã triển khai.

## 11. Cấu trúc repository mục tiêu

```text
smart-parking-system/
├── frontend/
│   └── src/
├── backend/
│   ├── prisma/
│   └── src/
│       ├── common/
│       ├── config/
│       └── modules/
├── alpr-service/
│   ├── app/
│   ├── tests/
│   ├── requirements.txt
│   └── Dockerfile
├── iot/
│   ├── mosquitto/
│   └── node-red/
├── infra/
│   └── docker-compose.yml
└── docs/
```

## 12. Quy tắc triển khai

- Local development trước; Docker Compose để khởi động PostgreSQL và Mosquitto, sau đó bổ sung Node-RED/ALPR.
- Secrets qua environment variables và file .env không commit.
- Có health check cho API, MQTT và ALPR.
- Có log correlationId để lần theo một lượt xe qua nhiều thành phần.
- Viết contract test cho MQTT payload và API giữa Express ↔ ALPR.
- Chưa triển khai Kubernetes, microservices nghiệp vụ hoặc cloud phức tạp trong đồ án.


## 13. Quyết định cần khóa trước khi triển khai nghiệp vụ

### 13.1 Luồng vận hành phải có phương án thủ công
- Luồng check-in/check-out thủ công là đường chạy nghiệp vụ chuẩn đầu tiên; MQTT và ALPR chỉ cung cấp tín hiệu đầu vào.
- Nhân viên được nhập biển số, xác nhận phương tiện và xử lý khi camera/OCR/MQTT/barrier simulator không hoạt động.
- ALPR và Node-RED không được gọi thẳng database hoặc tự tạo hóa đơn/session.
- Trạng thái qua luồng thủ công và luồng thiết bị phải đi qua cùng application service.

### 13.2 Trạng thái xe vào/ra và barrier
Không coi việc publish MQTT command là bằng chứng barrier đã mở. Phân biệt trạng thái nghiệp vụ với trạng thái thiết bị. Command phải có PENDING, ACKNOWLEDGED, TIMED_OUT hoặc FAILED; session không được tạo lại khi ACK thất bại. Khi thiết bị lỗi, tạo alert và cho phép nhân viên xử lý thủ công/retry có kiểm soát. Với luồng vật lý sau này, cân nhắc trạng thái trung gian ENTRY_PENDING/EXIT_PENDING trước khi chốt session.

### 13.3 Ranh giới tích hợp ALPR
- Frontend upload ảnh lên Express API; Express kiểm tra quyền, loại/kích thước file, lưu media an toàn rồi gọi ALPR nội bộ.
- Không để browser gọi trực tiếp ALPR service trong MVP; không expose service nội bộ ra Internet.
- ALPR chỉ trả candidate/confidence/metadata. Backend lưu kết quả và nhân viên xác nhận khi cần.
- Có timeout, giới hạn kích thước, kiểm tra MIME/content, xóa file tạm và retention policy.

### 13.4 Quy tắc dữ liệu và giao dịch
- Lưu snapshot bảng giá/đơn giá tại thời điểm tạo invoice; không tính lại hóa đơn lịch sử bằng pricing rule hiện hành.
- Mọi thao tác chiếm/giải phóng chỗ phải chạy trong transaction và có constraint chống hai session ACTIVE cùng chiếm một vị trí.
- API tạo payment cần idempotency key hoặc unique provider reference để request retry không ghi nhận thanh toán hai lần.
- Lưu UTC trong database; frontend hiển thị theo Asia/Ho_Chi_Minh.
- API danh sách phải có pagination, sort/filter allowlist và giới hạn page size.

### 13.5 Bảo mật nền tảng
- Validate input ở biên API; rate limit endpoint đăng nhập và upload.
- Cấu hình CORS theo origin cụ thể, security headers và HTTPS khi triển khai.
- Không log password, token, ảnh hoặc payload chứa dữ liệu cá nhân không cần thiết.
- Device credentials tách biệt user credentials và có thể thu hồi/đổi.
- Socket.IO phải xác thực kết nối và lọc dữ liệu theo role + parking-lot scope; không broadcast dữ liệu toàn hệ thống cho mọi client.

### 13.6 Phạm vi không triển khai sớm
Chưa làm thanh toán online thật, mobile app, microservices, Kubernetes, multi-tenant, phần cứng thật hoặc AI model training. Chỉ mở rộng khi luồng nghiệp vụ lõi chạy ổn định.


### 13.7 Nhất quán giữa session và barrier ACK

- Không đánh đồng việc publish command với việc barrier đã mở.
- Với luồng simulator có ACK, dùng trạng thái trung gian cho thao tác cổng/session: ENTRY_PENDING trước khi xác nhận xe vào hoàn tất; EXIT_PENDING trước khi xác nhận xe ra hoàn tất. Chỉ chuyển sang ACTIVE/COMPLETED khi ACK phù hợp được nhận hoặc nhân viên thực hiện quy trình fallback có audit.
- Giữ reservation/space ở trạng thái giữ chỗ trong thời gian chờ; timeout phải có thời hạn, retry idempotent và thao tác giải phóng/đối soát rõ ràng.
- Nếu chọn phương án đơn giản hơn cho demo, vẫn phải lưu gate operation riêng và không ghi nhận barrier OPEN nếu chưa có ACK.
- Chốt state machine cụ thể trong implementation Issue #12 trước khi viết migration; cập nhật enum/constraint tương ứng trong DATABASE_DESIGN.md.
