# Tổng quan đề tài

## 1. Thông tin chung

| Thuộc tính | Nội dung |
|---|---|
| Tên đề tài | Xây dựng nền tảng quản lý bãi đỗ xe thông minh |
| Tên tiếng Anh | Smart Parking Management Platform |
| Loại sản phẩm | Ứng dụng web quản lý tập trung nhiều bãi đỗ xe |
| Định hướng thực hiện | Đồ án/thực tập chuyên ngành Công nghệ thông tin |
| Kiến trúc | Modular Monolith kết hợp các dịch vụ hỗ trợ chuyên biệt |
| Trạng thái | Giai đoạn phân tích, thiết kế và chuẩn bị triển khai |

> Tài liệu mô tả mục tiêu và thiết kế dự kiến. Chỉ đánh dấu một chức năng là hoàn thành khi đã có mã nguồn, kiểm thử và bằng chứng nghiệm thu tương ứng.

## 2. Bối cảnh và lý do chọn đề tài

Hoạt động quản lý bãi đỗ xe thường phải xử lý đồng thời nhiều công việc: theo dõi sức chứa, kiểm soát phương tiện ra/vào, quản lý vị trí đỗ, tính phí, ghi nhận thanh toán và tra cứu lịch sử. Khi các thao tác được ghi chép hoặc quản lý bằng những công cụ rời rạc, việc cập nhật trạng thái có thể thiếu nhất quán, khó tổng hợp số liệu và mất thời gian đối soát.

Đề tài hướng tới xây dựng một nền tảng web quản lý tập trung nhiều bãi đỗ xe. Hệ thống hỗ trợ nhân viên vận hành nghiệp vụ gửi xe, cung cấp dữ liệu cho người quản lý theo dõi tình hình hoạt động và ứng dụng nhận diện biển số cùng mô phỏng IoT để minh họa quy trình kiểm soát phương tiện tự động.

## 3. Bài toán cần giải quyết

Hệ thống tập trung giải quyết các vấn đề sau:

1. Quản lý thông tin nhiều bãi xe trên cùng một nền tảng.
2. Quản lý khu vực, vị trí đỗ, cổng và sức chứa của từng bãi.
3. Quản lý tài khoản, vai trò và phạm vi bãi xe được phân công.
4. Theo dõi phương tiện, đặt chỗ và lượt gửi xe.
5. Thực hiện quy trình xe vào, xe ra, tính phí, lập hóa đơn và ghi nhận thanh toán.
6. Hỗ trợ nhận diện biển số từ ảnh/video, cho phép nhân viên kiểm tra và hiệu chỉnh kết quả.
7. Mô phỏng thiết bị, cảm biến và barrier để kiểm thử trao đổi sự kiện với hệ thống.
8. Cung cấp số liệu tổng quan, lịch sử và cảnh báo phục vụ vận hành.

## 4. Mục tiêu đề tài

### 4.1 Mục tiêu tổng quát

Phân tích, thiết kế và xây dựng một ứng dụng web có khả năng quản lý tập trung nhiều bãi đỗ xe, hỗ trợ quy trình gửi xe và minh họa khả năng tự động hóa bằng nhận diện biển số, mô phỏng thiết bị IoT.

### 4.2 Mục tiêu cụ thể

- Xây dựng cơ chế xác thực, phân quyền và giới hạn dữ liệu theo bãi xe.
- Thiết kế cơ sở dữ liệu quan hệ đáp ứng các nghiệp vụ cốt lõi.
- Xây dựng giao diện web cho quản trị viên, quản lý bãi, nhân viên và tài xế.
- Hoàn thiện luồng gửi xe thủ công làm phương án vận hành nền tảng.
- Tích hợp dịch vụ nhận diện biển số và cơ chế nhân viên xác nhận kết quả.
- Xây dựng mô phỏng IoT giao tiếp hai chiều qua MQTT.
- Kiểm thử các luồng nghiệp vụ, quyền truy cập, xử lý lỗi và tính nhất quán dữ liệu.
- Đánh giá kết quả dựa trên các kịch bản nghiệm thu đã xác định.

## 5. Đối tượng sử dụng và phạm vi quyền hạn

| Đối tượng | Vai trò trong hệ thống | Nhiệm vụ chính |
|---|---|---|
| Quản trị viên hệ thống | SYSTEM_ADMIN | Quản lý tài khoản, tạo bãi xe, phân công quản lý/nhân viên |
| Quản lý bãi xe | PARKING_MANAGER | Cấu hình và giám sát các bãi được phân công |
| Nhân viên bãi xe | PARKING_STAFF | Thực hiện xe vào/ra, kiểm tra biển số, xử lý thanh toán và sự cố |
| Tài xế | DRIVER | Quản lý phương tiện, tìm bãi, đặt chỗ, theo dõi lượt gửi và thanh toán |

Một tài khoản quản lý hoặc nhân viên có thể được phân công vào nhiều bãi. Quyền truy cập phải được kiểm tra ở backend theo cả vai trò và bãi xe được giao.

## 6. Phạm vi thực hiện

### 6.1 Trong phạm vi MVP

- Nền tảng web quản lý tập trung nhiều bãi xe.
- Quản lý tài khoản, vai trò và phân công nhân sự.
- Quản lý bãi xe, khu vực, vị trí đỗ, cổng và bảng giá.
- Quản lý phương tiện, đặt chỗ và lượt gửi xe.
- Check-in/check-out, tính phí, hóa đơn và ghi nhận thanh toán ở mức MVP.
- Dashboard và cập nhật trạng thái theo thời gian thực.
- Nhận diện biển số từ ảnh/video thử nghiệm bằng ALPR.
- Mô phỏng cổng, barrier và cảm biến bằng Node-RED; trao đổi dữ liệu qua Mosquitto MQTT.
- Nhật ký thao tác và cảnh báo vận hành cơ bản.

### 6.2 Ngoài phạm vi MVP

- Quản lý doanh nghiệp/tenant hoặc cung cấp nền tảng đa khách thuê.
- Ứng dụng di động native.
- Kết nối phần cứng vật lý bắt buộc như ESP32, camera chuyên dụng hoặc barrier thật.
- Thanh toán trực tuyến với ngân hàng/cổng thanh toán thật.
- Tách nghiệp vụ thành microservices, triển khai Kubernetes.
- Huấn luyện mô hình nhận diện biển số riêng từ đầu.

Các nội dung ngoài phạm vi có thể được xem xét ở giai đoạn mở rộng nếu có yêu cầu và nguồn lực phù hợp.

## 7. Phương pháp và quy trình thực hiện

Đề tài được triển khai theo hướng phát triển tăng dần, ưu tiên hoàn thiện từng luồng nghiệp vụ xuyên suốt thay vì chỉ xây dựng riêng lẻ giao diện hoặc backend.

1. Khảo sát bài toán và xác định tác nhân sử dụng.
2. Phân tích yêu cầu chức năng, phi chức năng và điều kiện nghiệm thu.
3. Thiết kế kiến trúc, cơ sở dữ liệu và giao diện.
4. Xây dựng môi trường phát triển và nền tảng backend/frontend.
5. Triển khai nghiệp vụ gửi xe thủ công.
6. Tích hợp ALPR và mô phỏng IoT.
7. Kiểm thử, đánh giá, hoàn thiện tài liệu và kịch bản trình diễn.

## 8. Công nghệ dự kiến

| Lớp | Công nghệ | Mục đích |
|---|---|---|
| Frontend | React, TypeScript, Tailwind CSS | Xây dựng giao diện web |
| Backend | Node.js, Express, TypeScript | API và xử lý nghiệp vụ |
| ORM | Prisma | Ánh xạ dữ liệu và quản lý migration |
| Database | PostgreSQL | Lưu trữ dữ liệu quan hệ |
| Realtime | Socket.IO | Cập nhật dữ liệu tới trình duyệt |
| Message broker | Eclipse Mosquitto | Trung chuyển thông điệp MQTT |
| IoT simulator | Node-RED | Mô phỏng cổng, barrier và cảm biến |
| ALPR service | Python, FastAPI, OpenCV, OCR adapter | Xử lý ảnh/video và nhận diện biển số |
| Môi trường | Docker Compose | Khởi chạy các dịch vụ phục vụ phát triển |

Việc lựa chọn công nghệ được cân nhắc theo mức độ phù hợp với bài toán, khả năng tích hợp, tài liệu hỗ trợ và khả năng triển khai trong phạm vi đồ án. Hiệu năng và độ chính xác thực tế cần được kiểm chứng bằng thử nghiệm, không suy luận chỉ từ tên công nghệ.

## 9. Sản phẩm dự kiến bàn giao

- Mã nguồn frontend, backend và dịch vụ ALPR.
- Cấu hình môi trường phát triển và mô phỏng IoT.
- Cơ sở dữ liệu, migration và dữ liệu mẫu.
- Tài liệu phân tích yêu cầu, kiến trúc và thiết kế dữ liệu.
- Bộ kiểm thử cho các nghiệp vụ và tình huống lỗi quan trọng.
- Hướng dẫn cài đặt, cấu hình và chạy hệ thống.
- Kịch bản demo và báo cáo đánh giá kết quả.

## 10. Tiêu chí đánh giá kết quả

Đề tài được đánh giá dựa trên các tiêu chí:

- Đáp ứng các yêu cầu chức năng đã xác định.
- Phân quyền đúng vai trò và phạm vi bãi xe.
- Dữ liệu phiên gửi xe, vị trí đỗ, hóa đơn và thanh toán nhất quán.
- Luồng thủ công hoạt động khi ALPR hoặc IoT simulator không khả dụng.
- Sự kiện MQTT được kiểm tra và xử lý trùng; lệnh barrier có ACK/timeout.
- Kết quả ALPR có thông tin độ tin cậy và hỗ trợ nhân viên xác nhận.
- Có kiểm thử, hướng dẫn cài đặt và kịch bản demo tái lập được.

## 11. Tài liệu liên quan

- [Đặc tả yêu cầu hệ thống](REQUIREMENTS_SPECIFICATION.md)
- [Kiến trúc hệ thống](ARCHITECTURE.md)
- [Thiết kế cơ sở dữ liệu](DATABASE_DESIGN.md)
- [Kế hoạch triển khai](PROJECT_PLAN.md)
- [Kế hoạch kiểm thử và đánh giá](TEST_AND_EVALUATION.md)
- [Backlog triển khai](IMPLEMENTATION_BACKLOG.md)
