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


## Đợt cập nhật: Chuẩn hóa nội dung GitHub Issues #2–#14

**Ngày:** 2026-11-11

### 1. Phạm vi thực hiện
Đã cập nhật trực tiếp nội dung 13 Issue đang mở (#2 đến #14). Không thay đổi trạng thái Open/Closed và không tự đóng Issue.

### 2. Nội dung đã chỉnh sửa
- Chuẩn hóa tiêu đề và năm phần mô tả: Input, Output, Technical Approach, Acceptance Criteria, công việc liên quan/phụ thuộc.
- Việt hóa phần mô tả nhu cầu và kết quả đầu ra; giữ thuật ngữ kỹ thuật chuẩn trong phần hướng xử lý.
- Bổ sung chi tiết cấu hình môi trường, bảo mật, kiểm thử, dữ liệu và điều kiện nghiệm thu theo phạm vi từng Issue.
- Làm rõ phụ thuộc giữa các Issue và phân biệt công việc có thể triển khai song song.
- Làm rõ phạm vi MVP: không có Organization/Company/Tenant, không thanh toán online thật, không cần phần cứng vật lý.

### 3. Các điểm kỹ thuật được làm rõ
- **#5:** Kiểm soát quyền ở backend, giới hạn dữ liệu theo bãi xe, vòng đời refresh token và bảo vệ phiên.
- **#7:** Chống overbooking bằng transaction/locking; cần chốt chính sách reservation trước migration.
- **#8:** Tách trạng thái Parking Session khỏi trạng thái barrier; không coi publish command là ACK.
- **#9:** Dùng kiểu Decimal cho tiền, idempotency cho thanh toán và snapshot bảng giá.
- **#10:** ALPR chỉ trả kết quả gợi ý; nhân viên xác nhận; Express kiểm soát quyền media.
- **#11:** MQTT event/command có ID, correlation, ACK, timeout và xử lý lặp; Node-RED không quyết định nghiệp vụ.
- **#12:** Chỉ chuyển session sang ACTIVE/COMPLETED sau ACK hợp lệ hoặc fallback thủ công có audit.
- **#13:** Dashboard và Socket.IO phải giới hạn theo quyền và bãi xe; doanh thu chỉ tính theo giao dịch đã thu.
- **#14:** CI, kiểm thử, security review và backup/restore cần có bằng chứng thực tế.

### 4. Những việc chưa được xác nhận
- Các Issue #2–#14 vẫn ở trạng thái Open sau khi chuẩn hóa mô tả.
- Chỉnh sửa Issue chỉ hoàn thiện đặc tả công việc, không đồng nghĩa chức năng đã được lập trình.
- Các quyết định nghiệp vụ được ghi là cần xác minh vẫn phải được chốt trước migration liên quan.
- Không có báo cáo test ứng dụng trong đợt này vì không thay đổi source code.
