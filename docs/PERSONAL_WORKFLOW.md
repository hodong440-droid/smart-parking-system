# Quy trình quản lý công việc cá nhân

## 1. Phạm vi áp dụng

Đây là dự án cá nhân do một người trực tiếp phân tích, thiết kế và triển khai. GitHub Issues và Pull Requests được dùng để quản lý tiến độ, lưu dấu quyết định và giúp Mentor có thể xem xét khi cần; không áp dụng quy trình phân công thành viên hoặc tổng hợp báo cáo nhóm.

## 2. Cách sử dụng GitHub Issues

Mỗi chức năng, lỗi hoặc công việc kỹ thuật đáng kể nên có một Issue. Issue cần mô tả đủ ba phần:

### 2.1 Input – Nhu cầu người dùng

Viết bằng ngôn ngữ tự nhiên: ai sử dụng, họ muốn giải quyết việc gì, bối cảnh và cách họ hình dung chức năng. Không mở đầu bằng tên bảng hoặc endpoint.

### 2.2 Output – Kết quả mong muốn

Nêu rõ người dùng có thể làm gì sau khi hoàn thành; giao diện hiển thị gì; hệ thống phản hồi thế nào ở trường hợp thành công và thất bại.

### 2.3 Technical Approach – Hướng xử lý kỹ thuật

Chuyển yêu cầu thành kế hoạch triển khai có thể thực hiện:
- Frontend: trang/component, trạng thái, validation.
- Backend: module, API/endpoint, service, authentication và authorization.
- Database: bảng, trường, kiểu dữ liệu, quan hệ, unique/check constraint và index.
- Tích hợp: MQTT, Socket.IO, ALPR hoặc dịch vụ ngoài nếu liên quan.
- Kiểm thử: test case, dữ liệu thử và điều kiện nghiệm thu.

Chỉ ghi quyết định kỹ thuật đã có căn cứ. Nếu còn điểm chưa rõ, ghi thành câu hỏi/giả định cần xác minh, không tự biến giả định thành yêu cầu.

### 2.4 Acceptance Criteria

Mỗi Issue có checklist nghiệm thu đo lường được. Ví dụ:
- [ ] Người dùng thực hiện được luồng chính.
- [ ] Dữ liệu không hợp lệ được từ chối và có thông báo phù hợp.
- [ ] Quyền truy cập được kiểm tra ở backend.
- [ ] Có test cho trường hợp thành công và lỗi quan trọng.

## 3. Workflow cá nhân

1. Tạo Issue và làm rõ Input, Output, Technical Approach.
2. Chọn một Issue ưu tiên; tránh triển khai nhiều đầu việc lớn cùng lúc.
3. Tạo branch từ `main`: `feature/<issue-number>-<short-name>`, `fix/<issue-number>-<short-name>` hoặc `docs/<issue-number>-<short-name>`.
4. Triển khai và commit theo từng thay đổi có ý nghĩa.
5. Chạy lint, type-check, test và build phù hợp với phần đã sửa.
6. Tạo Pull Request về `main`, mô tả thay đổi và liên kết Issue bằng `Closes #<number>`.
7. Tự review diff; nếu Mentor review thì xử lý góp ý trước khi merge.
8. Chỉ đóng Issue sau khi tiêu chí nghiệm thu đạt và thay đổi đã được merge.

Với thay đổi nhỏ có thể làm trực tiếp trên branch làm việc nếu phù hợp, nhưng không commit trực tiếp lên `main` khi đang áp dụng quy trình PR.

## 4. Trạng thái theo dõi

Có thể dùng GitHub Projects hoặc labels để biểu diễn:
- **Backlog**: đã ghi nhận, chưa sẵn sàng.
- **Ready**: yêu cầu và tiêu chí nghiệm thu đã rõ.
- **In Progress**: đang triển khai.
- **In Review**: đang tự kiểm tra hoặc chờ Mentor review qua PR.
- **Done**: đã nghiệm thu và merge.

Không đóng Issue chỉ để làm đẹp bảng tiến độ. Issue chưa hoàn thành phải giữ mở và ghi rõ phần còn lại.

## 5. Quy tắc repository

- `main` giữ trạng thái ổn định có thể xem xét/demo.
- Không commit `.env`, token, mật khẩu, credential, ảnh biển số thật hoặc dữ liệu cá nhân.
- Tài liệu kiến trúc/database phải được cập nhật khi có quyết định làm thay đổi thiết kế.
- README và tài liệu phải phản ánh đúng những gì đã triển khai; không ghi tính năng dự kiến như thể đã chạy.
- Nếu cần Mentor kiểm tra repository Private, cấp quyền truy cập GitHub phù hợp cho đúng tài khoản Mentor; không chia sẻ mật khẩu hoặc token.

## 6. Phạm vi báo cáo

Các yêu cầu về nhóm trưởng tổng hợp báo cáo tuần, tạo sheet hàng tuần và gửi báo cáo trước 14:00 Thứ Bảy là quy trình chung dành cho nhóm trưởng theo hướng dẫn buổi họp. Đây không phải đầu việc quản lý nhóm của dự án cá nhân này. Chỉ thực hiện báo cáo cá nhân nếu Mentor yêu cầu riêng.

## 7. Definition of Done

- [ ] Output và Acceptance Criteria của Issue đã đạt.
- [ ] Code đã qua các bước kiểm tra phù hợp.
- [ ] Không đưa secret hoặc dữ liệu nhạy cảm vào Git.
- [ ] PR liên kết đúng Issue và đã được review phù hợp.
- [ ] Tài liệu liên quan đã cập nhật.
- [ ] Thay đổi đã merge; Issue được đóng sau nghiệm thu.
