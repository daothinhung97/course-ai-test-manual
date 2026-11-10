# SRS – Module Đăng nhập (Login)

| Mục | Nội dung |
|---|---|
| Hệ thống | Perfex CRM – Anh Tester Demo |
| URL | https://crm.anhtester.com/authentication/login (và `/admin/authentication` cho khu vực admin) |
| Phiên bản tài liệu | 1.0 (Draft) |
| Ngày | 2026-11-09 |
| Nguồn | Khảo sát giao diện công khai của trang Login (chưa đăng nhập vào hệ thống) |

> **Ghi chú phạm vi khảo sát:** Các mục đánh dấu **[Giả định]** là hành vi chưa được xác minh trực tiếp (ví dụ thông báo lỗi, màn hình sau đăng nhập, giới hạn số lần thử). Cần xác nhận bằng test thực tế hoặc với BA/Dev.

---

## 1. Giới thiệu

### 1.1 Mục đích
Mô tả yêu cầu chức năng và phi chức năng của module Đăng nhập, làm cơ sở cho phát triển và thiết kế test case.

### 1.2 Phạm vi
Module cho phép người dùng (nhân viên/admin) xác thực bằng Email + Mật khẩu để truy cập hệ thống CRM. Bao gồm: chọn ngôn ngữ, ghi nhớ đăng nhập, liên kết quên mật khẩu.

### 1.3 Đối tượng sử dụng
| Vai trò | Mô tả |
|---|---|
| Admin / Staff | Người dùng nội bộ đăng nhập vào trang quản trị |
| Khách (chưa đăng nhập) | Truy cập trang Login, Knowledge Base |

### 1.4 Thuật ngữ
- **CRM**: Customer Relationship Management.
- **Remember me**: Duy trì phiên đăng nhập lâu hơn phiên trình duyệt.

---

## 2. Mô tả tổng quan

### 2.1 Bối cảnh
Trang Login là điểm vào duy nhất của khu vực quản trị. Thanh điều hướng phía trên có logo (về trang chủ), liên kết **Knowledge Base** và nút **Login**.

### 2.2 Ràng buộc & giả định
- Giao diện web, truy cập qua trình duyệt hiện đại.
- Hỗ trợ đa ngôn ngữ (26 ngôn ngữ).
- Xác thực bằng Email + Mật khẩu (không thấy đăng nhập SSO/Social trên trang).

---

## 3. Giao diện màn hình

### 3.1 Danh sách thành phần UI

| # | Thành phần | Loại | Bắt buộc | Mô tả / Quy tắc |
|---|---|---|---|---|
| 1 | Logo "Perfex CRM \| Anh Tester Demo" | Link/Image | – | Click → trang chủ `/` |
| 2 | Knowledge Base | Link | – | Điều hướng `/knowledge-base` |
| 3 | Nút Login (header) | Link/Button | – | Điều hướng `/authentication/login` |
| 4 | Tiêu đề "Please login" | Heading | – | Hiển thị tiêu đề form |
| 5 | Language | Dropdown (có ô tìm kiếm) | Không | Mặc định **English**. Đổi ngôn ngữ giao diện |
| 6 | Email Address | Textbox | Có | Nhập email tài khoản |
| 7 | Password | Textbox (password) | Có | Ký tự bị che |
| 8 | Remember me | Checkbox | Không | Mặc định không chọn |
| 9 | Login | Button (submit) | – | Gửi form xác thực |
| 10 | Forgot Password? | Link | – | Điều hướng `/authentication/forgot_password` |
| 11 | Footer | Text | – | "2026 Copyright Perfex CRM \| Anh Tester Demo" |

### 3.2 Danh sách ngôn ngữ hỗ trợ
English (mặc định), Chinese, Vietnamese, Indonesia, Bulgarian, Portuguese_br, Swedish, Dutch, Persian, Turkish, Catalan, Spanish, Portuguese, French, Czech, Japanese, Italian, Slovak, Russian, Polish, Greek, Finnish, Norwegian, German, Ukrainian, Romanian.

### 3.3 Bố cục
Form căn giữa trang, thứ tự từ trên xuống: Language → Email Address → Password → Remember me → Login → Forgot Password?. Form chứa trường ẩn (CSRF token).

---

## 4. Yêu cầu chức năng

### FR-LOGIN-01: Đăng nhập bằng Email & Mật khẩu
- **Mô tả:** Người dùng nhập email và mật khẩu hợp lệ rồi nhấn **Login** để vào hệ thống.
- **Tiền điều kiện:** Có tài khoản đang hoạt động; chưa đăng nhập.
- **Luồng chính:**
  1. Mở trang `/authentication/login`.
  2. Nhập Email Address.
  3. Nhập Password.
  4. (Tuỳ chọn) Chọn Remember me.
  5. Nhấn Login.
  6. Hệ thống xác thực, tạo phiên và chuyển đến trang quản trị Dashboard `/admin/` (**đã xác minh** với tài khoản admin; người dùng hiển thị là "Admin Example").
- **Hậu điều kiện:** Phiên đăng nhập được tạo; người dùng truy cập được chức năng theo quyền.
- **Luồng thay thế / ngoại lệ:**
  - E1: Để trống Email hoặc Password → báo lỗi bắt buộc nhập (FR-LOGIN-03).
  - E2: Email/Password sai → báo lỗi thông tin đăng nhập không đúng, ở lại trang Login (FR-LOGIN-04).
  - E3: Tài khoản bị vô hiệu hoá → từ chối đăng nhập **[Giả định]**.

### FR-LOGIN-02: Remember me
- Khi chọn, phiên đăng nhập được duy trì sau khi đóng trình duyệt (qua cookie) **[Giả định]**.
- Khi không chọn, phiên kết thúc khi đóng trình duyệt hoặc hết hạn.

### FR-LOGIN-03: Kiểm tra dữ liệu đầu vào (Validation)
| Trường | Quy tắc |
|---|---|
| Email Address | Bắt buộc; đúng định dạng email |
| Password | Bắt buộc |
- Thông báo lỗi hiển thị rõ ràng, không làm mất dữ liệu Email đã nhập.

### FR-LOGIN-04: Xử lý đăng nhập thất bại
- Hiển thị thông báo lỗi chung (không tiết lộ email hay mật khẩu sai) **[Giả định]**.
- Giữ người dùng ở trang Login, xoá trường Password.
- Cân nhắc khoá tạm / giới hạn số lần thử sai liên tiếp **[Giả định – cần xác nhận]**.

### FR-LOGIN-05: Chọn ngôn ngữ
- Dropdown Language liệt kê 26 ngôn ngữ, mặc định English.
- Hỗ trợ tìm kiếm trong dropdown.
- Chọn ngôn ngữ → nhãn/giao diện trang Login và hệ thống hiển thị theo ngôn ngữ đó **[Giả định]**.

### FR-LOGIN-06: Quên mật khẩu
- Nhấn **Forgot Password?** → chuyển đến `/authentication/forgot_password` để yêu cầu đặt lại mật khẩu.

### FR-LOGIN-07: Điều hướng khi đã đăng nhập
- Người dùng đã đăng nhập truy cập lại `/authentication/login` → chuyển thẳng đến Dashboard **[Giả định]**.
- Truy cập trang yêu cầu đăng nhập khi chưa xác thực → chuyển về trang Login.

### FR-LOGIN-08: Đăng xuất (liên quan)
- Sau khi đăng xuất, phiên bị huỷ, quay lại trang Login; nút Back của trình duyệt không truy cập lại được trang đã bảo vệ.

---

## 5. Yêu cầu phi chức năng

| Mã | Loại | Yêu cầu |
|---|---|---|
| NFR-01 | Bảo mật | Truyền dữ liệu qua HTTPS |
| NFR-02 | Bảo mật | Mật khẩu được che khi nhập; không lưu/hiển thị dạng rõ |
| NFR-03 | Bảo mật | Form có CSRF token; chống SQL Injection / XSS ở các trường nhập |
| NFR-04 | Bảo mật | Cookie phiên đặt cờ HttpOnly/Secure **[Giả định]** |
| NFR-05 | Hiệu năng | Trang Login tải ≤ 3 giây; xác thực phản hồi ≤ 3 giây trong điều kiện bình thường |
| NFR-06 | Khả dụng | Hiển thị đúng trên Chrome, Firefox, Edge, Safari bản mới; responsive desktop/tablet/mobile |
| NFR-07 | Trải nghiệm | Hỗ trợ phím Enter để submit; Tab di chuyển hợp lý giữa các trường |
| NFR-08 | Đa ngôn ngữ | Hỗ trợ 26 ngôn ngữ, mã hoá UTF-8 |

---

## 6. Quy tắc nghiệp vụ

| Mã | Quy tắc |
|---|---|
| BR-01 | Email dùng làm định danh đăng nhập |
| BR-02 | Chỉ tài khoản đang hoạt động mới được đăng nhập |
| BR-03 | Sau đăng nhập, quyền truy cập phụ thuộc vai trò/phân quyền của tài khoản |
| BR-04 | Ngôn ngữ mặc định là English |

---

## 7. Gợi ý kịch bản kiểm thử (tham khảo)

| ID | Kịch bản | Kết quả mong đợi |
|---|---|---|
| TC-01 | Đăng nhập với email & mật khẩu đúng | Vào Dashboard |
| TC-02 | Email đúng, mật khẩu sai | Báo lỗi, ở lại Login |
| TC-03 | Email không tồn tại | Báo lỗi chung |
| TC-04 | Để trống cả hai trường | Báo lỗi bắt buộc nhập |
| TC-05 | Email sai định dạng | Báo lỗi định dạng |
| TC-06 | Chỉ nhập Email | Báo lỗi thiếu Password |
| TC-07 | Chọn Remember me, đóng và mở lại trình duyệt | Vẫn đăng nhập |
| TC-08 | Không chọn Remember me, đóng và mở lại trình duyệt | Phải đăng nhập lại |
| TC-09 | Đổi ngôn ngữ sang Vietnamese | Giao diện đổi sang tiếng Việt |
| TC-10 | Tìm kiếm ngôn ngữ trong dropdown | Danh sách lọc đúng |
| TC-11 | Nhấn Forgot Password? | Mở trang quên mật khẩu |
| TC-12 | Nhấn Enter khi đã nhập đủ thông tin | Submit form |
| TC-13 | Nhập `' OR 1=1 --` vào Email/Password | Không đăng nhập được, không lỗi hệ thống |
| TC-14 | Nhập script XSS vào Email | Không thực thi script |
| TC-15 | Email có khoảng trắng đầu/cuối | Xử lý nhất quán (trim hoặc báo lỗi) |
| TC-16 | Nhập sai nhiều lần liên tiếp | Kiểm tra cơ chế khoá/giới hạn |
| TC-17 | Truy cập URL bảo vệ khi chưa đăng nhập | Chuyển về Login |
| TC-18 | Đã đăng nhập, mở lại `/authentication/login` | Chuyển về Dashboard |
| TC-19 | Đăng xuất rồi nhấn Back | Không xem lại được trang bảo vệ |
| TC-20 | Responsive mobile/tablet | Bố cục hiển thị đúng |

---

## 8. Câu hỏi mở
1. Chính sách khoá tài khoản / số lần thử tối đa?
2. Thời hạn phiên và thời hạn cookie khi chọn Remember me?
3. Có hỗ trợ 2FA không?
4. Nội dung chính xác của các thông báo lỗi?
5. Trang đích sau đăng nhập theo từng vai trò? (Admin: Dashboard `/admin/` – đã xác minh; các vai trò khác chưa kiểm tra)

---

## 9. Kết quả khảo sát sau đăng nhập (admin)
- Đăng nhập thành công → `/admin/` (tiêu đề "Dashboard").
- Menu trái: Dashboard, Customers, Projects, Tasks, Contracts, Sales, Subscriptions, Expenses, Support, Leads, Estimate Request, Knowledge Base, Utilities, Reports.
- Dashboard hiển thị: tổng quan Invoice/Estimate/Proposal, số liệu doanh thu, bảng Tasks, lịch, To Do, Leads, Project Activity.
- Chưa kiểm tra: thông báo lỗi khi sai thông tin, Remember me, đăng xuất, khoá tài khoản.
