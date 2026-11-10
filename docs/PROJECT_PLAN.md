# Kế hoạch triển khai

Thứ tự ưu tiên nhằm hoàn thiện nghiệp vụ lõi trước, sau đó mới bổ sung tính năng thông minh.

| Giai đoạn | Nội dung | Kết quả |
|---|---|---|
| 0. Nền tảng | Chốt kiến trúc, database design, quy ước code, môi trường | Tài liệu nền tảng |
| 1. Khởi tạo | React + TypeScript + Tailwind; Express + TypeScript; PostgreSQL + Prisma; cấu hình môi trường | Frontend/backend khởi chạy |
| 2. Tài khoản | Đăng ký/đăng nhập, JWT, refresh token, RBAC, phân công nhân sự theo bãi | Auth và phân quyền hoạt động |
| 3. Hạ tầng bãi xe | CRUD bãi xe, khu vực, vị trí, cổng; trạng thái và sức chứa | Quản lý cấu trúc bãi |
| 4. Phương tiện & đặt chỗ | Hồ sơ xe, tìm bãi, đặt/hủy chỗ | Luồng đặt chỗ |
| 5. Xe vào/ra | Tạo session, phân bổ chỗ, tính phí, giải phóng chỗ | Nghiệp vụ gửi xe hoàn chỉnh |
| 6. Hóa đơn & thanh toán | Hóa đơn, phương thức thanh toán, trạng thái giao dịch | Theo dõi doanh thu |
| 7. Dashboard | Công suất, xe đang gửi, lượt xe, doanh thu | Báo cáo vận hành |
| 8. Realtime & simulator | Socket.IO, mô phỏng cảm biến/barie, cảnh báo | Cập nhật trạng thái trực tiếp |
| 9. Thông minh | QR, nhận diện biển số, tích hợp thiết bị thật nếu đủ điều kiện | Mở rộng smart features |
| 10. Hoàn thiện | Kiểm thử, bảo mật, logging, triển khai, tài liệu báo cáo | Bản bàn giao |

## Definition of Done tối thiểu
- API validate dữ liệu đầu vào và trả lỗi nhất quán.
- Kiểm tra RBAC và phạm vi bãi xe ở backend.
- Nghiệp vụ cập nhật nhiều bảng dùng transaction.
- Có kiểm thử cho luồng chính và trường hợp lỗi.
- Không commit `.env`, secret, token hoặc mật khẩu.
- Tài liệu README phản ánh đúng trạng thái thực tế; không ghi chức năng dự kiến thành đã hoàn thành.
