# Kiến trúc hệ thống

## 1. Kiểu kiến trúc
Sử dụng **Modular Monolith**: một backend Express triển khai như một ứng dụng thống nhất, nhưng chia thành các module nghiệp vụ độc lập. Không tách microservices ở giai đoạn đầu.

Lý do:
- Phù hợp quy mô đồ án và đội phát triển nhỏ.
- Dễ triển khai, debug, kiểm thử và quản lý transaction.
- Giữ ranh giới module rõ ràng để có thể tách dịch vụ khi có nhu cầu thực tế.

## 2. Sơ đồ tổng quan

```text
React Web App (TypeScript + Tailwind)
             |
          HTTPS / REST
             |
Express API (TypeScript - Modular Monolith)
  | Auth & Users
  | Parking Lots & Infrastructure
  | Reservations & Vehicles
  | Parking Sessions & Pricing
  | Invoices & Payments
  | Devices, Events & Alerts
  | Dashboard & Reports
             |
       Prisma ORM
             |
        PostgreSQL

Express API <---- Socket.IO ----> React Web App
Parking Simulator ---- device events/API ----> Express API
```

## 3. Backend modules
Mỗi module nên có routes, controller, service, validation và repository/data-access riêng khi cần:
- **auth**: đăng nhập, làm mới/thu hồi token, đăng xuất.
- **users**: hồ sơ, vai trò, phân công bãi xe.
- **parking-lots**: bãi xe, khu vực, vị trí đỗ, cổng.
- **vehicles**: phương tiện và biển số.
- **reservations**: tìm chỗ và đặt chỗ.
- **parking-sessions**: xe vào/ra, phân bổ chỗ, tính thời gian.
- **pricing**: quy tắc giá và tính phí.
- **billing**: hóa đơn, thanh toán, hoàn tiền (nếu triển khai).
- **devices**: cảm biến, barie, trạng thái thiết bị, sự kiện mô phỏng.
- **notifications**: cảnh báo và thông báo.
- **analytics**: công suất, lượt xe, doanh thu.

Không để controller chứa trực tiếp nghiệp vụ hoặc truy vấn database phức tạp.

## 4. Phân quyền và phạm vi dữ liệu
Bốn role:
- `SYSTEM_ADMIN`
- `PARKING_MANAGER`
- `PARKING_STAFF`
- `DRIVER`

Backend áp dụng hai lớp kiểm tra:
1. **RBAC**: người dùng có role được phép thực hiện hành động hay không.
2. **Resource scope**: với quản lý/nhân viên, bãi xe đích phải nằm trong danh sách được phân công.

Không tin role, userId hoặc parkingLotId do client tự gửi nếu chưa xác thực. Không chỉ ẩn nút trên frontend để bảo vệ API.

## 5. Luồng xác thực dự kiến
1. Client gửi thông tin đăng nhập qua HTTPS.
2. API kiểm tra tài khoản và đối chiếu mật khẩu với password hash lưu trong database.
3. API cấp access token thời hạn ngắn và refresh token; refresh token phải được lưu theo cách có thể thu hồi (ưu tiên lưu hash trong database).
4. Client gửi access token khi gọi API được bảo vệ.
5. Middleware xác minh chữ ký, thời hạn và trạng thái token; middleware RBAC và resource-scope kiểm tra quyền tiếp theo.
6. Khi access token hết hạn, client gọi endpoint refresh; refresh token được xoay vòng. Logout thu hồi refresh token.
7. Trả `401 Unauthorized` khi chưa xác thực hợp lệ; `403 Forbidden` khi đã xác thực nhưng không đủ quyền.

Đây là thiết kế mục tiêu, chưa phải mã xác thực đã triển khai. Không lưu mật khẩu dạng rõ, không commit secret hoặc token vào Git.

## 6. Luồng xe vào/ra (MVP)
**Xe vào**
1. Nhân viên hoặc simulator ghi nhận biển số/xe tại cổng vào.
2. Backend kiểm tra đặt chỗ (nếu có), sức chứa và vị trí khả dụng.
3. Backend tạo parking session và phân bổ vị trí trong transaction.
4. Cập nhật trạng thái vị trí và phát sự kiện realtime.

**Xe ra**
1. Tra cứu session đang hoạt động theo xe.
2. Tính phí theo pricing rule áp dụng.
3. Tạo invoice/payment record và ghi nhận kết quả thanh toán.
4. Đóng session, giải phóng vị trí và phát sự kiện realtime.

Cần chống tạo hai session đang hoạt động cho cùng một xe trong cùng bãi và xử lý đồng thời khi nhiều xe vào.

## 7. Cấu trúc thư mục backend mục tiêu
```
backend/
├── prisma/
│   ├── schema.prisma
│   └── migrations/
└── src/
    ├── config/
    ├── common/
    │   ├── middleware/
    │   ├── errors/
    │   └── validation/
    ├── modules/
    │   ├── auth/
    │   ├── users/
    │   ├── parking-lots/
    │   ├── vehicles/
    │   ├── reservations/
    │   ├── parking-sessions/
    │   ├── billing/
    │   ├── devices/
    │   └── analytics/
    ├── app.ts
    └── server.ts
```

## 8. Nguyên tắc kỹ thuật
- Validate request ở biên API.
- Dùng transaction cho phân bổ chỗ, tạo session, thanh toán và giải phóng chỗ.
- Dùng UTC để lưu thời điểm; chuyển múi giờ khi hiển thị.
- Ghi audit log cho thao tác quản trị và nghiệp vụ quan trọng.
- Không phát sự kiện realtime trước khi transaction database commit thành công.
- Secrets và connection string lấy từ environment variables.
