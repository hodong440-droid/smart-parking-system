# Hướng dẫn quản lý Issue và Pull Request

## 1. Mục đích

Tài liệu này quy định cách ghi nhận, triển khai, kiểm thử và nghiệm thu công việc trên GitHub cho dự án cá nhân Smart Parking System. Mục tiêu là giúp người đọc hiểu yêu cầu, người phát triển biết cần làm gì và Mentor có thể kiểm tra kết quả.

## 2. Quy tắc viết Issue

Một Issue chức năng cần có đủ năm phần:

1. **Mô tả (Input):** Nhu cầu của người dùng, viết bằng tiếng Việt tự nhiên.
2. **Kết quả đầu ra (Output):** Hành vi và kết quả người dùng mong muốn, viết bằng tiếng Việt.
3. **Hướng xử lý (Technical Approach):** Giải pháp kỹ thuật gồm frontend, backend, database, bảo mật, tích hợp và kiểm thử.
4. **Tiêu chí nghiệm thu (Acceptance Criteria):** Checklist rõ ràng, có thể xác minh.
5. **Issue liên quan / phụ thuộc:** Liên kết bằng số Issue và nêu quan hệ phụ thuộc.

### Mẫu nội dung Issue

```markdown
## 1. Mô tả (Input)
[Người dùng nào? Nhu cầu/vấn đề gì? Bối cảnh sử dụng?]

## 2. Kết quả đầu ra (Output)
[Người dùng có thể làm gì? Hệ thống phản hồi/hiển thị thế nào?]

## 3. Hướng xử lý (Technical Approach)
- Frontend:
- Backend / API:
- Database: bảng, trường, kiểu dữ liệu, quan hệ, constraint, index.
- Bảo mật và phân quyền:
- Tích hợp:
- Kiểm thử:

## 4. Tiêu chí nghiệm thu (Acceptance Criteria)
- [ ] ...
- [ ] ...

## 5. Issue liên quan / phụ thuộc
- Phụ thuộc:
- Có thể triển khai song song:
- Cần xác minh:
```

## 3. Quy tắc ngôn ngữ

- Input và Output phải dùng tiếng Việt rõ nghĩa, mô tả theo góc nhìn người sử dụng.
- Technical Approach dùng thuật ngữ kỹ thuật chính xác; giải thích từ viết tắt ở lần xuất hiện đầu tiên nếu cần.
- Không viết Input bằng tên bảng/API; không viết Output chỉ bằng tên công nghệ.
- Tên bảng/cột/API/class trong phần kỹ thuật phải nhất quán với tài liệu thiết kế.
- Không sao chép nguyên văn yêu cầu của dự án tham khảo; chỉ áp dụng cấu trúc trình bày phù hợp với bài toán Smart Parking.

## 4. Quy tắc liên kết Issue

- Ghi số Issue phụ thuộc trong phần liên quan.
- Dùng cú pháp `Closes #<số>` trong PR khi PR giải quyết trọn vẹn Issue.
- Không đóng Issue chỉ vì đã tạo branch, commit hoặc mở PR.
- Nếu công việc tách thành nhiều phần, tạo Issue con hoặc Issue liên quan và ghi rõ quan hệ.

## 5. Quy tắc Pull Request

Mỗi PR cần mô tả:
- Mục tiêu thay đổi.
- Nội dung đã thực hiện.
- Issue được giải quyết.
- Cách kiểm thử và kết quả thực tế.
- Ảnh minh họa hoặc video nếu có giao diện.
- Việc chưa làm hoặc giới hạn còn tồn tại.

Trước khi merge cần kiểm tra:
- [ ] Thay đổi đúng phạm vi Issue.
- [ ] Không chứa secret, file build, cache hoặc dữ liệu cá nhân.
- [ ] Kiểm thử phù hợp đã chạy.
- [ ] Tài liệu liên quan đã cập nhật.
- [ ] Diff đã được tự rà soát hoặc Mentor review khi cần.

## 6. Quy trình cá nhân và báo cáo

Đây là dự án cá nhân. Người phát triển tự tạo Issue, branch, commit, PR, kiểm thử và cập nhật tiến độ. Các yêu cầu dành cho nhóm trưởng như tổng hợp báo cáo công việc của các thành viên hoặc tạo sheet báo cáo nhóm không áp dụng.

Nếu Mentor yêu cầu báo cáo cá nhân, báo cáo chỉ phản ánh công việc thực tế của chủ dự án và dẫn liên kết Issue/PR làm bằng chứng.
