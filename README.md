# Smart Parking System
Hệ thống quản lý tập trung nhiều bãi đỗ xe thông minh.

> Trạng thái: khởi tạo nền tảng kiến trúc và đặc tả. Mã nguồn ứng dụng chưa được triển khai.

## 1. Mục tiêu
Xây dựng một nền tảng duy nhất để quản lý nhiều bãi đỗ xe tại các địa điểm khác nhau. Người quản trị hệ thống quản lý toàn bộ bãi xe; quản lý và nhân viên chỉ thao tác trong các bãi xe được phân công; tài xế có thể tìm kiếm, đặt chỗ và theo dõi lượt gửi xe.

**Phạm vi đã chốt:** quản lý tập trung nhiều bãi xe, không xây dựng mô hình doanh nghiệp/Organization hoặc multi-tenant.

## 2. Công nghệ
| Thành phần | Công nghệ |
|---|---|
| Frontend | React, TypeScript, Tailwind CSS |
| Backend API | Node.js, Express, TypeScript |
| Database | PostgreSQL |
| ORM / migrations | Prisma |
| Authentication | JWT access token + refresh token, RBAC |
| Realtime | Socket.IO |
| Bản đồ | Google Maps hoặc Mapbox (sẽ lựa chọn khi tích hợp) |
| Mô phỏng thiết bị | Parking Simulator; tích hợp cảm biến/barie thật ở giai đoạn sau |

## 3. Vai trò
- `SYSTEM_ADMIN`: quản trị toàn hệ thống, quản lý người dùng, bãi xe và phân công nhân sự.
- `PARKING_MANAGER`: quản lý hoạt động và cấu hình của các bãi xe được giao.
- `PARKING_STAFF`: xử lý xe vào/ra, lượt gửi xe và vận hành tại bãi được giao.
- `DRIVER`: quản lý phương tiện, tìm bãi, đặt chỗ, xem lịch sử và thanh toán.

Quyền truy cập phải được kiểm tra ở backend. Vai trò không thay thế việc kiểm tra phạm vi bãi xe được phân công.

## 4. Phạm vi chức năng
1. Tài khoản, đăng nhập, phân quyền và phân công người quản lý/nhân viên theo bãi xe.
2. Quản lý bãi xe, khu vực, vị trí đỗ, cổng và sức chứa.
3. Quản lý phương tiện và đặt chỗ.
4. Ghi nhận xe vào/ra, phân bổ vị trí và tính phí.
5. Hóa đơn, thanh toán và lịch sử giao dịch.
6. Dashboard, thống kê công suất và doanh thu.
7. Mô phỏng cảm biến, barie, sự kiện thiết bị và cảnh báo.
8. Cập nhật trạng thái theo thời gian thực bằng Socket.IO.

## 5. Nguyên tắc nghiệp vụ
- Một hệ thống quản lý nhiều bãi xe; không có bảng Organization/Tenant.
- Một tài khoản quản lý hoặc nhân viên có thể được phân công vào nhiều bãi xe.
- Quản lý/nhân viên chỉ được truy cập dữ liệu của bãi xe đã được phân công.
- Trạng thái chỗ đỗ phải được cập nhật nhất quán khi đặt chỗ, xe vào và xe ra.
- Tính phí phải dựa trên quy tắc giá có hiệu lực tại thời điểm phát sinh lượt gửi xe.
- Giai đoạn MVP ưu tiên nghiệp vụ lõi và mô phỏng thiết bị; AI nhận diện biển số và phần cứng thật triển khai sau.

## 6. Cấu trúc dự kiến
```
smart-parking-system/
├── frontend/                 # React + TypeScript
├── backend/                  # Express API + Prisma
├── docs/
│   ├── ARCHITECTURE.md
│   ├── DATABASE_DESIGN.md
│   └── PROJECT_PLAN.md
├── .gitignore
└── README.md
```

Đây là cấu trúc mục tiêu; các thư mục ứng dụng sẽ được tạo ở các bước triển khai tiếp theo.

## 7. Tài liệu
- [Kiến trúc hệ thống](docs/ARCHITECTURE.md)
- [Thiết kế cơ sở dữ liệu](docs/DATABASE_DESIGN.md)
- [Kế hoạch triển khai](docs/PROJECT_PLAN.md)

## 8. Trạng thái triển khai
Hiện repository mới có đặc tả nền tảng. Chưa có frontend/backend chạy được, Prisma schema, migration hay kiểm thử. Không xem các chức năng trong tài liệu là chức năng đã hoàn thành.
