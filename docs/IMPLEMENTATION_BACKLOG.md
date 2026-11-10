# Implementation Backlog

Backlog chính thức của dự án cá nhân Smart Parking System. Các Issue #2–#14 đều đã tồn tại trên GitHub và đang mở; đây là kế hoạch, không phải bằng chứng chức năng đã được triển khai.

## Milestone A – Foundation
| Issue | Công việc | Phụ thuộc |
|---|---|---|
| [#2](../issues/2) | Docker Compose, PostgreSQL, Mosquitto | — |
| [#3](../issues/3) | Backend Express + TypeScript + Prisma | #2 |
| [#4](../issues/4) | Frontend React + TypeScript + Tailwind | #3 cho tích hợp API; khởi tạo song song |
| [#5](../issues/5) | Authentication, RBAC, parking-lot scope | #3 |
| [#6](../issues/6) | Bãi xe, khu vực, vị trí, cổng, bảng giá | #3, #4, #5 |

**Điều kiện qua mốc:** PostgreSQL/Mosquitto chạy được; backend kết nối DB; frontend gọi health API; migration nền tảng chạy trên database rỗng; test xác thực và resource scope cơ bản.

## Milestone B – Nghiệp vụ gửi xe thủ công
| Issue | Công việc | Phụ thuộc |
|---|---|---|
| [#7](../issues/7) | Phương tiện và reservation | #5, #6 |
| [#8](../issues/8) | Check-in/check-out và parking session | #6, #7 |
| [#9](../issues/9) | Tính phí, invoice, payment MVP | #6, #8 |

**Gate bắt buộc:** Nhân viên phải nhận xe, phân chỗ, tra cứu lượt đang gửi, tính phí, ghi nhận thanh toán và cho xe ra khi MQTT, Node-RED và ALPR đều tắt.

## Milestone C – ALPR và IoT
| Issue | Công việc | Phụ thuộc |
|---|---|---|
| [#10](../issues/10) | Python FastAPI + OpenCV + OCR adapter | #3, #5, #8 |
| [#11](../issues/11) | Mosquitto + Node-RED simulator + MQTT adapter | #2, #3, #6, #8 |
| [#12](../issues/12) | Tích hợp xe vào/ra đầu-cuối | #8, #9, #10, #11 |

ALPR và IoT là hai tích hợp độc lập; chỉ ghép vào luồng cổng đầu-cuối sau khi luồng gửi xe thủ công đã ổn định. Backend là nơi duy nhất quyết định nghiệp vụ.

## Milestone D – Hoàn thiện sản phẩm
| Issue | Công việc | Phụ thuộc |
|---|---|---|
| [#13](../issues/13) | Dashboard, sơ đồ bãi, realtime, alerts | #6, #8, #9, #11, #12 |
| [#14](../issues/14) | CI, test tổng thể, security review, backup/restore, demo | #12, #13 |

## Quy tắc thực hiện
- Mỗi thời điểm ưu tiên một Issue lớn; có thể làm việc nhỏ liên quan trong cùng vertical slice.
- Một Issue đại diện một kết quả có thể nghiệm thu, không phải từng file/hàm.
- Issue chỉ đóng sau khi Acceptance Criteria đạt, test phù hợp pass và PR đã merge.
- Không tạo báo cáo tuần hoặc phân công thành viên trong backlog cá nhân; chỉ làm báo cáo cá nhân nếu Mentor yêu cầu.
- Không triển khai thanh toán online thật, mobile app, phần cứng thật, microservices, Kubernetes hoặc multi-tenant trong MVP.

## Các quyết định nghiệp vụ cần khóa trước Prisma migration
1. Reservation giữ suất theo capacity hay khóa vị trí cụ thể: chọn capacity cho MVP.
2. Bảng giá MVP: theo giờ/theo lượt, cách làm tròn, grace period và mức tối thiểu.
3. Quy tắc một xe có thể có nhiều session ACTIVE ở các bãi khác nhau hay không.
4. Email/phone bắt buộc với DRIVER; cách tạo tài khoản staff.
5. Payment MVP có partial payment/refund hay chỉ ghi nhận đủ tiền một lần.
6. Thời hạn lưu ảnh/video và quyền xem/xóa.
7. Lưu UTC; hiển thị theo Asia/Ho_Chi_Minh.

## Cách đọc trạng thái
- GitHub Issue Open: công việc chưa được nghiệm thu, không có nghĩa đang triển khai.
- GitHub Issue Closed: chỉ dùng khi đã đạt Definition of Done.
- Tài liệu mô tả thiết kế mục tiêu; README phải cập nhật khi có code chạy được.
