# Implementation Backlog

Đây là backlog triển khai cá nhân. Issue #2–#6 là các đầu việc khởi tạo đã tạo; #7–#14 là các hạng mục chức năng tiếp theo cần tạo thành GitHub Issue trước khi bắt đầu từng giai đoạn.

## Milestone A – Foundation
| Issue | Công việc | Phụ thuộc |
|---|---|---|
| #2 | Docker Compose, PostgreSQL, Mosquitto | — |
| #3 | Backend Express + TypeScript + Prisma | #2 |
| #4 | Frontend React + TypeScript + Tailwind | #3 (tích hợp API) |
| #5 | Authentication, RBAC, parking-lot scope | #3 |
| #6 | Bãi xe, khu vực, vị trí, cổng, bảng giá | #5 |

## Milestone B – Manual parking workflow
| Planned Issue | Công việc | Phụ thuộc |
|---|---|---|
| #7 | Phương tiện và reservation | #5, #6 |
| #8 | Check-in/check-out và parking session | #6, #7 |
| #9 | Tính phí, invoice, payment MVP | #6, #8 |

**Gate:** Luồng thủ công phải hoạt động độc lập khi MQTT, Node-RED và ALPR tắt.

## Milestone C – Recognition and IoT
| Planned Issue | Công việc | Phụ thuộc |
|---|---|---|
| #10 | Python FastAPI + OpenCV + OCR adapter | #3, #5, #8 |
| #11 | Mosquitto + Node-RED simulator + MQTT adapter | #2, #3, #6, #8 |
| #12 | Tích hợp xe vào/ra đầu-cuối | #8, #9, #10, #11 |

## Milestone D – Product completion
| Planned Issue | Công việc | Phụ thuộc |
|---|---|---|
| #13 | Dashboard, sơ đồ bãi, realtime, alerts | #6, #8, #9, #11, #12 |
| #14 | CI, test tổng thể, security review, backup/restore, demo | #12, #13 |

## Quy tắc sử dụng
- Các số #7–#14 là kế hoạch dự kiến; chỉ coi là GitHub Issue sau khi chúng thực sự được tạo.
- Không tạo Issue cho từng file/hàm; một Issue là một kết quả có thể nghiệm thu.
- Chỉ có một Issue lớn In Progress tại một thời điểm.
- Issue chỉ đóng khi Acceptance Criteria đạt, kiểm thử phù hợp đã chạy và PR được merge.
- Không tự tạo milestone/label trạng thái nếu chưa có cấu hình tương ứng trên GitHub.

## Các quyết định cần khóa trước migration
1. Reservation giữ suất theo capacity hay khóa vị trí cụ thể (khuyến nghị capacity).
2. Pricing MVP: theo giờ/theo lượt, cách làm tròn, grace period.
3. Một xe có thể có nhiều session ACTIVE ở các bãi khác nhau hay không.
4. Email/phone bắt buộc với DRIVER; cách tạo tài khoản nhân sự.
5. Chính sách partial payment/refund.
6. Thời hạn lưu ảnh/video và quyền xem/xóa.
7. Lưu UTC, hiển thị Asia/Ho_Chi_Minh.
