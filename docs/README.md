# Danh mục tài liệu dự án

Thư mục `docs/` lưu hồ sơ phân tích, thiết kế, quyết định kỹ thuật và kiểm thử. Tài liệu phải phản ánh đúng trạng thái thực tế của mã nguồn.

## 1. Hồ sơ đề tài
- [PROJECT_OVERVIEW.md](PROJECT_OVERVIEW.md): bối cảnh, bài toán, mục tiêu, phạm vi và phương pháp.
- [REQUIREMENTS_SPECIFICATION.md](REQUIREMENTS_SPECIFICATION.md): tác nhân, FR/NFR và quy tắc nghiệp vụ.

## 2. Thiết kế hệ thống
- [ARCHITECTURE.md](ARCHITECTURE.md): kiến trúc logic, ranh giới thành phần, luồng tích hợp.
- [DATABASE_DESIGN.md](DATABASE_DESIGN.md): mô hình dữ liệu logic, quan hệ và ràng buộc.
- [decisions/](decisions/): Architecture Decision Records (ADR), lưu quyết định quan trọng và lý do lựa chọn.
- [diagrams/](diagrams/): sơ đồ Mermaid dùng trong tài liệu.

## 3. Triển khai và đánh giá
- [PROJECT_PLAN.md](PROJECT_PLAN.md): roadmap và điều kiện qua từng giai đoạn.
- [IMPLEMENTATION_BACKLOG.md](IMPLEMENTATION_BACKLOG.md): ánh xạ công việc sang GitHub Issues.
- [TEST_AND_EVALUATION.md](TEST_AND_EVALUATION.md): chiến lược kiểm thử và nghiệm thu.
- [PERSONAL_WORKFLOW.md](PERSONAL_WORKFLOW.md): quy trình phát triển cá nhân.

## 4. Quy tắc quản lý tài liệu
- Mỗi tài liệu có một mục đích chính; tránh sao chép toàn bộ nội dung giữa nhiều file.
- Khi quyết định kiến trúc thay đổi, cập nhật ADR trước/sau khi thống nhất, sau đó đồng bộ Architecture, Database Design và Requirements.
- Dùng mã FR/NFR/TC ổn định để truy vết yêu cầu → Issue → test.
- Ghi ngày và trạng thái khi tài liệu cần lịch sử quyết định; không ghi trạng thái hoàn thành nếu chưa có bằng chứng.
