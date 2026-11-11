# Kế hoạch triển khai theo giai đoạn và Sprint

## 1. Nguyên tắc lập kế hoạch

Dự án do một cá nhân trực tiếp phân tích, thiết kế và triển khai. Cách chia Sprint dưới đây dùng để lập kế hoạch và tự theo dõi tiến độ, đồng thời giúp Mentor đánh giá từng kết quả bàn giao. Không áp dụng việc phân công thành viên, tổng hợp báo cáo nhóm hoặc nhiệm vụ của nhóm trưởng.

- Mỗi Sprint có mục tiêu, danh sách Issue, kết quả bàn giao và điều kiện nghiệm thu.
- Sprint là khoảng thời gian dự kiến; ngày bắt đầu/kết thúc được cập nhật theo tiến độ thực tế, không tự ghi nhận công việc chưa làm.
- Một Issue có thể kéo dài qua Sprint nếu chưa đạt tiêu chí nghiệm thu.
- Chỉ đóng Issue sau khi hoàn thành, kiểm thử phù hợp và merge thay đổi.
- Các mô tả trong kế hoạch là mục tiêu dự kiến, không phải xác nhận đã triển khai.

## 2. Lộ trình Sprint

| Sprint | Trọng tâm | Issue liên quan | Kết quả bàn giao |
|---|---|---|---|
| Sprint 0 | Chốt bài toán, phạm vi, kiến trúc, database và quy trình GitHub | #15–#17 và tài liệu thiết kế | Hồ sơ phân tích/thiết kế thống nhất; backlog có thứ tự |
| Sprint 1 | Thiết lập môi trường và khởi tạo nền tảng backend | #2, #3 | PostgreSQL, Mosquitto chạy local; Express kết nối DB; health check hoạt động |
| Sprint 2 | Khởi tạo frontend và xác thực/phân quyền | #4, #5 | React chạy được; đăng nhập, vai trò và giới hạn dữ liệu theo bãi |
| Sprint 3 | Quản lý hạ tầng nhiều bãi xe | #6 | Quản lý bãi, khu vực, vị trí, cổng, bảng giá và phân công |
| Sprint 4 | Quản lý phương tiện và đặt chỗ | #7 | Quản lý phương tiện; tạo/hủy/tra cứu reservation; chống đặt vượt sức chứa |
| Sprint 5 | Nghiệp vụ xe vào/ra thủ công | #8 | Check-in, phân vị trí, tra cứu xe đang gửi, check-out |
| Sprint 6 | Tính phí và ghi nhận thanh toán MVP | #9 | Bảng giá, hóa đơn, thanh toán thủ công/mô phỏng, lịch sử |
| Sprint 7 | Xây dựng dịch vụ nhận diện biển số | #10 | ALPR nhận ảnh, trả kết quả gợi ý; nhân viên xác nhận hoặc sửa |
| Sprint 8 | Mô phỏng thiết bị và tích hợp MQTT | #11 | Node-RED, Mosquitto, event/command, ACK, timeout và chống xử lý trùng |
| Sprint 9 | Hoàn thiện luồng xe vào/ra tích hợp | #12 | Kịch bản đầu-cuối kết hợp nghiệp vụ, ALPR và barrier simulator |
| Sprint 10 | Dashboard, realtime và cảnh báo | #13 | Theo dõi sức chứa, lượt gửi, doanh thu và cảnh báo vận hành |
| Sprint 11 | Kiểm thử, bảo mật, sao lưu và bàn giao | #14 | Bộ kiểm thử, CI, backup/restore, hướng dẫn chạy và video demo |

**Lưu ý:** Sprint 0 phản ánh phần chuẩn bị tài liệu đã thực hiện. Các Sprint còn lại là kế hoạch mục tiêu; không suy ra rằng chúng đã hoàn thành chỉ vì Issue đã được tạo.

## 3. Cách triển khai trong từng Sprint

### Bước 1 – Chuẩn bị
- Chọn Issue phù hợp với thứ tự phụ thuộc trong [Backlog](IMPLEMENTATION_BACKLOG.md).
- Đọc yêu cầu, thiết kế liên quan và các quyết định đã ghi trong ADR.
- Kiểm tra đầu vào, đầu ra, phạm vi, tiêu chí nghiệm thu và các câu hỏi chưa rõ.
- Nếu phát hiện thiếu yêu cầu hoặc mâu thuẫn thiết kế, cập nhật tài liệu/Issue trước khi viết code.

### Bước 2 – Thực hiện
- Tạo nhánh riêng cho Issue, đặt tên ngắn gọn theo loại công việc.
- Triển khai theo lát cắt dọc: giao diện (nếu có), API, nghiệp vụ và dữ liệu cần thiết.
- Commit theo thay đổi nhỏ, nội dung commit mô tả đúng việc đã làm.
- Không đưa secret, dữ liệu cá nhân hoặc ảnh biển số thật lên GitHub.

### Bước 3 – Kiểm thử và tự rà soát
- Chạy kiểm thử phù hợp với phạm vi Issue.
- Kiểm tra trường hợp hợp lệ, không hợp lệ, lỗi, quyền truy cập và dữ liệu biên.
- Đối chiếu code với Acceptance Criteria.
- Cập nhật tài liệu kỹ thuật và hướng dẫn chạy nếu có thay đổi.

### Bước 4 – Pull Request và nghiệm thu
- Tạo Pull Request liên kết Issue bằng `Closes #<số>` khi PR thực sự giải quyết Issue.
- Mô tả thay đổi, cách kiểm thử và kết quả thực tế; đính kèm ảnh/video nếu có giao diện.
- Tự review diff, kiểm tra file thừa, secret và thay đổi ngoài phạm vi.
- Chỉ merge và đóng Issue khi tiêu chí nghiệm thu đạt.

### Bước 5 – Tổng kết Sprint cá nhân
Ghi lại trong tài liệu theo dõi cá nhân hoặc GitHub Project:
- Issue đã hoàn thành và PR tương ứng.
- Kết quả kiểm thử có bằng chứng.
- Issue còn dở và lý do.
- Vướng mắc, quyết định mới và công việc chuyển tiếp.
- Không lập báo cáo tổng hợp thay cho nhóm trưởng hoặc báo cáo công việc của các thành viên khác.

## 4. Cấu trúc một Issue

Mỗi Issue chức năng phải có tối thiểu các phần sau:

### 4.1. Mô tả – Input
Viết bằng tiếng Việt, theo góc nhìn người dùng. Nêu rõ ai đang gặp nhu cầu gì, trong hoàn cảnh nào và vấn đề hiện tại là gì. Hạn chế dùng tên bảng, endpoint hoặc thuật ngữ kỹ thuật ở phần này.

### 4.2. Kết quả đầu ra – Output
Mô tả bằng tiếng Việt những gì người dùng có thể thực hiện sau khi chức năng hoàn thành, hệ thống phản hồi ra sao và các trạng thái quan trọng cần hiển thị.

### 4.3. Hướng xử lý – Technical Approach
Trình bày giải pháp kỹ thuật đủ cụ thể để có thể triển khai:
- Frontend: màn hình, thành phần và luồng tương tác.
- Backend: module, API, xử lý nghiệp vụ.
- Database: bảng, trường, kiểu dữ liệu, khóa, quan hệ, constraint và index.
- Bảo mật: xác thực, vai trò và phạm vi dữ liệu.
- Tích hợp: dịch vụ ngoài, MQTT, ALPR hoặc realtime nếu có.
- Kiểm thử: các trường hợp cần xác minh.

### 4.4. Tiêu chí nghiệm thu – Acceptance Criteria
Dùng checklist có thể kiểm tra được. Tránh tiêu chí mơ hồ như “hoạt động tốt” hoặc “giao diện đẹp”.

### 4.5. Phụ thuộc và liên kết
Gắn Issue liên quan bằng số Issue; ghi rõ công việc nào phải hoàn thành trước, công việc nào có thể làm song song và quyết định nào còn chờ xác minh.

## 5. Cách viết mô tả kỹ thuật và ngôn ngữ

- Phần Input và Output ưu tiên tiếng Việt tự nhiên, dễ hiểu với người không chuyên.
- Phần Technical Approach có thể dùng thuật ngữ tiếng Anh chuẩn ngành, nhưng phải giải thích khi cần.
- Tên bảng, cột, API, class, module và biến giữ theo quy ước kỹ thuật thống nhất trong mã nguồn.
- Tránh trộn nhiều cách gọi cho cùng một khái niệm; tham chiếu [Glossary](GLOSSARY.md).
- Không đưa thông tin giả định thành yêu cầu đã chốt; đánh dấu rõ “Cần xác minh”.

## 6. Tiêu chí hoàn thành một Sprint

Một Sprint chỉ được xem là hoàn thành khi:
- Các Issue được chọn đã đạt tiêu chí nghiệm thu hoặc được ghi rõ phần chuyển tiếp.
- Pull Request tương ứng đã được review và merge.
- Kiểm thử phù hợp đã chạy; kết quả được ghi nhận trung thực.
- Tài liệu thiết kế/hướng dẫn được cập nhật theo code.
- Có thể trình bày hoặc chạy thử kết quả bàn giao.
- Các lỗi còn tồn tại và giới hạn đã được ghi nhận.

## 7. Tài liệu liên quan

- [Tổng quan đề tài](PROJECT_OVERVIEW.md)
- [Đặc tả yêu cầu](REQUIREMENTS_SPECIFICATION.md)
- [Kiến trúc hệ thống](ARCHITECTURE.md)
- [Thiết kế cơ sở dữ liệu](DATABASE_DESIGN.md)
- [Backlog triển khai](IMPLEMENTATION_BACKLOG.md)
- [Quy trình làm việc cá nhân](PERSONAL_WORKFLOW.md)
- [Kế hoạch kiểm thử và đánh giá](TEST_AND_EVALUATION.md)
