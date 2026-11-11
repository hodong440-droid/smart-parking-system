# Nhật ký chỉnh sửa repository

Tài liệu này ghi nhận các thay đổi về cấu trúc, nội dung và cách quản lý GitHub để người hướng dẫn có thể theo dõi lý do chỉnh sửa. Nhật ký mô tả các thay đổi tài liệu; không phải báo cáo rằng chức năng phần mềm đã được lập trình.

## Đợt rà soát: Chuẩn hóa theo cách trình bày dự án tham khảo

**Repository tham khảo:** [HoangDinhTAn/DA-TTCN](https://github.com/HoangDinhTAn/DA-TTCN)

### 1. Những cách trình bày được tham khảo

- Chia công việc theo Sprint có mục tiêu và khoảng thời gian dự kiến.
- Mỗi Sprint liệt kê các nhiệm vụ cụ thể.
- Mỗi Issue mô tả công việc đủ chi tiết để người thực hiện hiểu đầu vào, kết quả và hướng giải quyết.
- Có liên kết tới mã nguồn/tài liệu liên quan và tiêu chí kết quả.
- Sử dụng GitHub Issues để theo dõi trạng thái công việc.

### 2. Những nội dung không sao chép

- Không sao chép công nghệ, mô hình AI, dữ liệu PaySim hoặc nghiệp vụ của dự án tham khảo.
- Không áp dụng cơ chế chia việc cho nhiều thành viên vì Smart Parking System là dự án cá nhân.
- Không đưa nhiệm vụ nhóm trưởng, tổng hợp báo cáo nhóm hoặc sheet báo cáo thành viên vào quy trình.
- Không sao chép lịch Sprint và mốc ngày của repository tham khảo; lịch thực hiện được xác định theo tiến độ thực tế của dự án này.

### 3. Danh sách chỉnh sửa trong đợt này

| STT | Vị trí | Nội dung chỉnh sửa | Lý do |
|---|---|---|---|
| 1 | `README.md` | Bổ sung liên kết tới kế hoạch Sprint và hướng dẫn Issue/PR | Người xem có lối đọc và tra cứu thống nhất |
| 2 | `docs/PROJECT_PLAN.md` | Bổ sung lộ trình Sprint 0–11, mục tiêu, Issue và kết quả bàn giao | Thể hiện tiến độ theo giai đoạn như dự án tham khảo |
| 3 | `docs/PROJECT_PLAN.md` | Bổ sung quy trình chuẩn bị, thực hiện, kiểm thử, PR và tổng kết Sprint | Làm rõ cách triển khai từng công việc |
| 4 | `docs/PROJECT_PLAN.md` | Quy định Input, Output, Technical Approach và Acceptance Criteria | Thống nhất cấu trúc mô tả công việc |
| 5 | `docs/ISSUE_AND_PR_GUIDE.md` | Tạo hướng dẫn và mẫu Issue/PR bằng tiếng Việt | Giúp người đọc hiểu yêu cầu và cách nghiệm thu |
| 6 | `.github/ISSUE_TEMPLATE/task.yml` | Việt hóa mô tả trường nhập và bổ sung trường phụ thuộc | Khi tạo Issue mới, biểu mẫu hướng dẫn đúng cách viết |
| 7 | `docs/IMPLEMENTATION_BACKLOG.md` | Bổ sung liên kết kế hoạch Sprint và làm rõ trạng thái kế hoạch | Tránh nhầm Issue mở với chức năng đã hoàn thành |
| 8 | `docs/README.md` | Bổ sung liên kết tài liệu mới | Dễ tìm tài liệu |
| 9 | `docs/CHANGELOG_DOCUMENTATION.md` | Ghi lại toàn bộ thay đổi và lý do | Có thể kiểm tra, đối chiếu và giải trình với Mentor |

### 4. Nguyên tắc bảo toàn thiết kế Smart Parking

Các thay đổi phải giữ nguyên quyết định đã chốt:
- Nền tảng quản lý tập trung nhiều bãi xe.
- Không có Organization/Company/Tenant.
- Kiến trúc backend Modular Monolith.
- Frontend React + TypeScript + Tailwind CSS.
- Backend Node.js + Express + TypeScript.
- PostgreSQL + Prisma; Socket.IO cho realtime.
- Node-RED + Mosquitto cho mô phỏng IoT.
- Python FastAPI + OpenCV + OCR adapter cho ALPR.
- Luồng gửi xe thủ công vẫn hoạt động khi ALPR/IoT không khả dụng.

### 5. Những việc chưa được xác nhận hoàn thành

- Source code ứng dụng chưa được xác nhận triển khai chỉ bằng việc bổ sung kế hoạch.
- Các Sprint từ Sprint 1 trở đi là kế hoạch dự kiến cho tới khi Issue đạt nghiệm thu.
- Chưa có báo cáo kiểm thử thực tế nếu chưa có log hoặc kết quả chạy.
- Không đánh dấu Issue hoàn thành khi chưa có PR đã merge và tiêu chí nghiệm thu đạt.

## 6. Lịch sử cập nhật

| Ngày | Nội dung |
|---|---|
| 2026-11-11 | Chuẩn hóa tài liệu theo Sprint, Việt hóa hướng dẫn Issue/PR và lập nhật ký chỉnh sửa |
