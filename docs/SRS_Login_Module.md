# SRS – Module Đăng nhập (Login) – Perfex CRM | Anh Tester Demo

| Thuộc tính | Giá trị |
|---|---|
| Hệ thống | Perfex CRM – Anh Tester Demo |
| URL | https://crm.anhtester.com/admin/authentication |
| Module | Authentication (Login, Forgot Password) – khu vực Admin |
| Phiên bản tài liệu | 1.0 (Draft) |
| Ngày lập | 2026-10-11 |
| Nguồn thông tin | Khảo sát trực tiếp giao diện + DOM trang Login và Forgot Password (không đăng nhập thực tế) |

> **Quy ước nguồn gốc yêu cầu**
> - **[OBS]** – Đã quan sát trực tiếp trên hệ thống (cấu trúc form, thuộc tính HTML, link, nhãn).
> - **[ASM]** – Giả định dựa trên hành vi chuẩn của Perfex CRM / CodeIgniter hoặc thực tế phổ biến; **chưa xác minh**, cần chạy thử để xác nhận trước khi dùng làm expected result.

---

## 1. Giới thiệu

### 1.1 Mục đích
Tài liệu mô tả đặc tả yêu cầu phần mềm cho chức năng **Đăng nhập** (và chức năng liên quan **Quên mật khẩu**) của trang quản trị Perfex CRM, làm đầu vào cho thiết kế test case thủ công.

### 1.2 Phạm vi
- **Trong phạm vi:** màn hình Login, xác thực email/mật khẩu, "Remember me", điều hướng sau đăng nhập, màn hình Forgot Password.
- **Ngoài phạm vi:** đăng nhập Client Portal, đặt lại mật khẩu qua link email (reset), quản lý người dùng/phân quyền, các module sau đăng nhập.

### 1.3 Đối tượng sử dụng
| Vai trò | Mô tả |
|---|---|
| Staff / Admin | Nhân viên/quản trị viên có tài khoản trong hệ thống, đăng nhập vào `/admin` |
| Khách vô danh | Người chưa đăng nhập truy cập các URL quản trị |

### 1.4 Dữ liệu kiểm thử được cung cấp
| Mục | Giá trị |
|---|---|
| Email | `admin@example.com` |
| Mật khẩu | Do người yêu cầu cung cấp (không lưu trong tài liệu) |

---

## 2. Mô tả tổng quan

### 2.1 Luồng chính
```
Truy cập /admin/authentication → Nhập Email + Password → (tuỳ chọn) tick Remember me
→ Nhấn Login → Hệ thống xác thực → Thành công: vào Dashboard (/admin)
                                  → Thất bại: ở lại trang Login + hiển thị thông báo lỗi
```

### 2.2 Màn hình liên quan
| Mã | Màn hình | URL | Nguồn |
|---|---|---|---|
| SCR-01 | Login | `/admin/authentication` | [OBS] |
| SCR-02 | Forgot Password | `/admin/authentication/forgot_password` | [OBS] |
| SCR-03 | Dashboard (đích sau đăng nhập) | `/admin` | [ASM] |

### 2.3 Ràng buộc & giả định
- Ứng dụng web chạy trên trình duyệt, giao tiếp HTTPS [OBS].
- Form gửi bằng phương thức **POST** tới chính URL hiện tại, kèm **CSRF token** (`csrf_token_name`) [OBS].
- Hệ thống là bản demo công khai, dữ liệu có thể bị thay đổi/reset bởi người dùng khác [ASM].

---

## 3. Đặc tả giao diện

### 3.1 SCR-01 – Màn hình Login [OBS]

| # | Thành phần | Loại | Thuộc tính HTML | Ghi chú |
|---|---|---|---|---|
| 1 | Logo Anh Tester | Image + link | link tới `https://crm.anhtester.com/` | Click về trang chủ |
| 2 | Tiêu đề **Login** | Heading | – | |
| 3 | **Email Address** | Input | `type="email"`, `id="email"`, `name="email"`, `autofocus` | Không có `required`, `maxlength`, `placeholder` |
| 4 | **Password** | Input | `type="password"`, `id="password"`, `name="password"` | Không có `required`, `maxlength`, `autocomplete` |
| 5 | **Remember me** | Checkbox | `id="remember"`, `name="remember"`, mặc định **không tick** | |
| 6 | **Login** | Button | `type="submit"`, full-width, màu primary | |
| 7 | **Forgot Password?** | Link | → `/admin/authentication/forgot_password` | |
| 8 | CSRF token | Hidden input | `name="csrf_token_name"` | Sinh mới mỗi lần tải trang |

Hành vi mặc định khi tải trang: con trỏ tự động focus vào ô Email [OBS – thuộc tính `autofocus`].

### 3.2 SCR-02 – Màn hình Forgot Password [OBS]

| # | Thành phần | Loại | Thuộc tính HTML |
|---|---|---|---|
| 1 | Logo Anh Tester | Image + link | → `https://crm.anhtester.com/` |
| 2 | Tiêu đề **Forgot Password** | Heading | – |
| 3 | **Email Address** | Input | `type="email"`, `id="email"`, `name="email"` |
| 4 | **Confirm** | Button | `type="submit"` |
| 5 | CSRF token | Hidden input | `name="csrf_token_name"` |

Lưu ý [OBS]: màn hình Forgot Password **không có** link quay lại trang Login (chỉ có link logo về trang chủ).

---

## 4. Yêu cầu chức năng

### 4.1 FR-LOGIN – Đăng nhập

| ID | Yêu cầu | Độ ưu tiên | Nguồn |
|---|---|---|---|
| FR-LOGIN-01 | Hệ thống hiển thị form Login gồm Email, Password, Remember me, nút Login và link Forgot Password khi truy cập `/admin/authentication`. | Cao | [OBS] |
| FR-LOGIN-02 | Người dùng nhập Email hợp lệ + Password đúng và nhấn **Login** → hệ thống xác thực thành công và chuyển tới Dashboard quản trị. | Cao | [ASM] |
| FR-LOGIN-03 | Email đúng nhưng Password sai → không đăng nhập, ở lại trang Login, hiển thị thông báo lỗi chung (ví dụ *Invalid email or password*). Không tiết lộ email có tồn tại hay không. | Cao | [ASM] |
| FR-LOGIN-04 | Email không tồn tại trong hệ thống → hiển thị cùng thông báo lỗi như FR-LOGIN-03. | Cao | [ASM] |
| FR-LOGIN-05 | Bỏ trống Email và/hoặc Password rồi nhấn Login → không đăng nhập, hiển thị thông báo bắt buộc nhập (ví dụ *The Email Address field is required.* / *The Password field is required.*). Kiểm tra phía server vì input không có `required`. | Cao | [ASM] |
| FR-LOGIN-06 | Email sai định dạng (thiếu `@`, thiếu domain…) → trình duyệt chặn gửi (do `type="email"`) hoặc server trả lỗi định dạng email. | Trung bình | [OBS: type=email] / [ASM: thông báo] |
| FR-LOGIN-07 | Tài khoản bị vô hiệu hoá (inactive) không được phép đăng nhập; hiển thị thông báo tài khoản không hoạt động. | Trung bình | [ASM] |
| FR-LOGIN-08 | Khi tick **Remember me**, phiên đăng nhập được duy trì sau khi đóng và mở lại trình duyệt (cookie lưu dài hạn). Khi không tick, phiên kết thúc khi đóng trình duyệt/hết hạn. | Trung bình | [ASM] |
| FR-LOGIN-09 | Người dùng đã đăng nhập truy cập lại `/admin/authentication` → được chuyển thẳng tới Dashboard, không hiện lại form Login. | Trung bình | [ASM] |
| FR-LOGIN-10 | Người dùng chưa đăng nhập truy cập trực tiếp URL quản trị (ví dụ `/admin`, `/admin/customers`) → bị chuyển hướng về trang Login. | Cao | [ASM] |
| FR-LOGIN-11 | Nhấn phím **Enter** trong ô Email/Password có tác dụng như nhấn nút Login (submit form). | Thấp | [ASM] |
| FR-LOGIN-12 | Mỗi lần gửi form phải kèm CSRF token hợp lệ; request thiếu/sai token bị từ chối. | Cao | [OBS: có token] / [ASM: hành vi từ chối] |
| FR-LOGIN-13 | Hệ thống giới hạn số lần đăng nhập sai liên tiếp (khoá tạm thời theo IP/tài khoản) để chống brute-force. | Trung bình | [ASM – cần xác minh ngưỡng] |
| FR-LOGIN-14 | Nếu bật xác thực hai lớp (2FA) hoặc reCAPTCHA trong cấu hình, bước tương ứng được hiển thị sau/trong khi đăng nhập. Mặc định trên màn hình hiện tại **không thấy** 2FA hay reCAPTCHA. | Thấp | [OBS: không hiển thị] / [ASM: cấu hình] |
| FR-LOGIN-15 | Click link **Forgot Password?** → chuyển tới SCR-02. | Cao | [OBS] |

### 4.2 FR-FORGOT – Quên mật khẩu

| ID | Yêu cầu | Độ ưu tiên | Nguồn |
|---|---|---|---|
| FR-FORGOT-01 | Hiển thị form gồm ô Email Address và nút **Confirm** khi truy cập `/admin/authentication/forgot_password`. | Cao | [OBS] |
| FR-FORGOT-02 | Nhập email của tài khoản hợp lệ và nhấn **Confirm** → hệ thống gửi email hướng dẫn đặt lại mật khẩu và hiển thị thông báo thành công. | Cao | [ASM] |
| FR-FORGOT-03 | Nhập email không tồn tại → hiển thị thông báo lỗi (ví dụ *Email not found*) hoặc thông báo trung tính, tuỳ chính sách. | Trung bình | [ASM] |
| FR-FORGOT-04 | Để trống email hoặc sai định dạng → không gửi yêu cầu, hiển thị lỗi tương ứng. | Trung bình | [ASM] |

---

## 5. Quy tắc nghiệp vụ & validation

| ID | Trường | Quy tắc | Nguồn |
|---|---|---|---|
| BR-01 | Email | Bắt buộc; đúng định dạng email; không phân biệt hoa/thường [ASM]; không giới hạn độ dài ở client (không có `maxlength`) | [OBS]/[ASM] |
| BR-02 | Password | Bắt buộc; phân biệt hoa/thường; che ký tự (`type=password`); không giới hạn độ dài ở client | [OBS]/[ASM] |
| BR-03 | Remember me | Tuỳ chọn; mặc định không chọn | [OBS] |
| BR-04 | Thông báo lỗi | Không phân biệt "sai email" và "sai mật khẩu" để tránh lộ thông tin tài khoản | [ASM] |
| BR-05 | Phiên | Sau đăng nhập thành công, phiên được tạo; đăng xuất/hết hạn phải quay lại Login | [ASM] |
| BR-06 | CSRF | Token sinh mới mỗi lần tải trang, bắt buộc khi submit | [OBS]/[ASM] |

### 5.1 Bảng thông báo (dự kiến – cần xác minh nội dung thực tế)

| Tình huống | Thông báo dự kiến | Nguồn |
|---|---|---|
| Thiếu Email | The Email Address field is required. | [ASM] |
| Thiếu Password | The Password field is required. | [ASM] |
| Sai email/mật khẩu | Invalid email or password | [ASM] |
| Tài khoản inactive | Your account is not active / inactive | [ASM] |
| Bị khoá do sai nhiều lần | Thông báo đăng nhập sai quá số lần cho phép | [ASM] |
| Forgot: gửi thành công | Thông báo đã gửi email hướng dẫn | [ASM] |
| Forgot: email không tồn tại | Email not found | [ASM] |

---

## 6. Yêu cầu phi chức năng

| ID | Nhóm | Yêu cầu | Nguồn |
|---|---|---|---|
| NFR-01 | Bảo mật | Truyền dữ liệu qua HTTPS; mật khẩu không hiển thị dạng plain text trên giao diện | [OBS] |
| NFR-02 | Bảo mật | Chống CSRF bằng token; chống brute-force; thông báo lỗi không lộ thông tin tài khoản | [OBS]/[ASM] |
| NFR-03 | Bảo mật | Chống SQL Injection / XSS trên ô Email & Password (input được lọc/escape) | [ASM] |
| NFR-04 | Bảo mật | Cookie phiên nên có cờ `HttpOnly`, `Secure` | [ASM] |
| NFR-05 | Tương thích | Hoạt động trên các trình duyệt phổ biến (Chrome, Firefox, Edge, Safari) | [ASM] |
| NFR-06 | Responsive | Form hiển thị đúng trên desktop, tablet, mobile (form căn giữa, nút full-width) | [OBS: layout căn giữa] |
| NFR-07 | Hiệu năng | Trang Login tải < 3 giây trên mạng thường; xác thực phản hồi < 3 giây | [ASM] |
| NFR-08 | Khả dụng | Có label gắn với từng ô nhập; focus ban đầu ở ô Email; điều hướng được bằng bàn phím (Tab/Enter) | [OBS]/[ASM] |

---

## 7. Use case

### UC-01: Đăng nhập thành công
| Mục | Nội dung |
|---|---|
| Tác nhân | Staff / Admin |
| Tiền điều kiện | Tài khoản tồn tại và đang hoạt động; người dùng chưa đăng nhập |
| Luồng chính | 1. Mở `/admin/authentication`. 2. Nhập Email. 3. Nhập Password. 4. (Tuỳ chọn) tick Remember me. 5. Nhấn Login. 6. Hệ thống xác thực và chuyển tới Dashboard. |
| Luồng thay thế | 3a. Bỏ trống trường → hiển thị lỗi bắt buộc nhập. 5a. Sai thông tin → hiển thị lỗi, giữ ở trang Login. |
| Hậu điều kiện | Phiên đăng nhập được tạo; người dùng truy cập được các chức năng theo quyền |

### UC-02: Yêu cầu đặt lại mật khẩu
| Mục | Nội dung |
|---|---|
| Tác nhân | Staff / Admin |
| Tiền điều kiện | Người dùng quên mật khẩu |
| Luồng chính | 1. Tại Login, nhấn Forgot Password?. 2. Nhập Email. 3. Nhấn Confirm. 4. Hệ thống gửi email hướng dẫn và thông báo thành công. |
| Luồng thay thế | 3a. Email không tồn tại/sai định dạng → hiển thị lỗi. |
| Hậu điều kiện | Email đặt lại mật khẩu được gửi tới hộp thư |

---

## 8. Tiêu chí chấp nhận (Acceptance Criteria)

| ID | Tiêu chí (Given / When / Then) |
|---|---|
| AC-01 | **Given** đang ở trang Login, **when** nhập đúng Email + Password và nhấn Login, **then** được chuyển tới Dashboard. |
| AC-02 | **Given** đang ở trang Login, **when** nhập sai Password, **then** vẫn ở trang Login và thấy thông báo lỗi. |
| AC-03 | **Given** đang ở trang Login, **when** để trống cả hai trường và nhấn Login, **then** không đăng nhập và thấy thông báo bắt buộc nhập. |
| AC-04 | **Given** chưa đăng nhập, **when** truy cập trực tiếp `/admin`, **then** bị chuyển về trang Login. |
| AC-05 | **Given** đang ở trang Login, **when** nhấn Forgot Password?, **then** hiển thị màn hình Forgot Password. |
| AC-06 | **Given** đã đăng nhập, **when** truy cập `/admin/authentication`, **then** được chuyển thẳng tới Dashboard. |

---

## 9. Gợi ý điều kiện kiểm thử (Test Conditions)

| Nhóm | Điều kiện cần kiểm thử | Liên kết yêu cầu |
|---|---|---|
| Chức năng | Đăng nhập hợp lệ; Remember me tick/không tick; Enter để submit; Logout rồi Back | FR-LOGIN-02, 08, 11 |
| Negative | Sai password; email không tồn tại; bỏ trống từng trường; email sai định dạng; chỉ có khoảng trắng; email có khoảng trắng đầu/cuối | FR-LOGIN-03…06 |
| Biên | Email/Password rất dài (255+, 1000+ ký tự); ký tự Unicode/đặc biệt; phân biệt hoa/thường | BR-01, BR-02 |
| Bảo mật | SQLi (`' OR '1'='1`), XSS (`<script>`), brute-force nhiều lần, CSRF token thiếu/sai, truy cập URL admin khi chưa login, Password không lộ trong URL/DOM | FR-LOGIN-10, 12, 13, NFR-02…04 |
| Điều hướng | Link Forgot Password, link logo, Back/Forward, refresh sau khi submit | FR-LOGIN-09, 15 |
| UI/UX | Label, thứ tự Tab, focus ban đầu, hiển thị mobile/tablet, thông báo lỗi dễ hiểu | NFR-06, 08 |
| Forgot Password | Email hợp lệ/không tồn tại/trống/sai định dạng | FR-FORGOT-01…04 |
| Tương thích | Chrome, Firefox, Edge, Safari | NFR-05 |

---

## 10. Điểm cần làm rõ / rủi ro

| # | Vấn đề | Ghi chú |
|---|---|---|
| 1 | Nội dung chính xác các thông báo lỗi, vị trí hiển thị (alert/inline) | Chưa xác minh vì chưa submit form |
| 2 | Hành vi sau đăng nhập thành công (trang đích, 2FA, bắt đổi mật khẩu) | Chưa đăng nhập thực tế |
| 3 | Ngưỡng và thời gian khoá khi sai mật khẩu nhiều lần | Cần xác nhận cấu hình |
| 4 | Thời hạn phiên và thời hạn cookie "Remember me" | Cần xác nhận |
| 5 | Input không có `required`/`maxlength` → phụ thuộc hoàn toàn vào validate server | Rủi ro cần kiểm tra |
| 6 | Link logo trỏ về trang chủ `/` thay vì Login | Cần xác nhận có đúng thiết kế |
| 7 | Màn hình Forgot Password thiếu link quay lại Login | Điểm UX cần xác nhận |
| 8 | Tiêu đề tab của Forgot Password vẫn là "…- Login" | Có thể là lỗi nhỏ về nội dung |
| 9 | Giá trị `value="estimate"` của checkbox Remember me không khớp ngữ nghĩa | Có thể là bug/ghi chú kỹ thuật, ít ảnh hưởng chức năng |

---

## 11. Lịch sử thay đổi

| Phiên bản | Ngày | Nội dung | Tác giả |
|---|---|---|---|
| 1.0 | 2026-10-11 | Khởi tạo SRS dựa trên khảo sát giao diện Login & Forgot Password | Claude |
