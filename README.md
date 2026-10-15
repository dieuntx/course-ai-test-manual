# course-ai-test-manual

Repo thực hành của khóa học **AI Testing Manual**: dùng AI hỗ trợ phân tích yêu cầu (SRS) và thiết kế test case thủ công (manual test) cho hệ thống **Perfex CRM – Anh Tester Demo**.

- Hệ thống kiểm thử: <https://crm.anhtester.com/admin/authentication>
- Module đang thực hiện: **Authentication** (Login, Forgot Password) và **Dashboard**

## Cấu trúc thư mục

```
.
├── docs/
│   ├── SRS_Login_Module.md        # Đặc tả yêu cầu (SRS) module Login
│   └── testcase/
│       ├── TC_login.txt           # Test case module Login
│       └── TC_Dashboard.txt       # Test case module Dashboard (chưa có nội dung)
├── LICENSE                        # MIT License
└── README.md
```

## Nội dung chính

### `docs/SRS_Login_Module.md`
Tài liệu đặc tả yêu cầu cho chức năng đăng nhập và quên mật khẩu của trang quản trị Perfex CRM, gồm:

- Đặc tả giao diện (màn hình Login, Forgot Password) và thuộc tính HTML quan sát được
- Yêu cầu chức năng `FR-LOGIN-*`, `FR-FORGOT-*`
- Quy tắc nghiệp vụ, yêu cầu phi chức năng, use case, tiêu chí chấp nhận (Given/When/Then)
- Gợi ý điều kiện kiểm thử và các điểm cần làm rõ / rủi ro

Mỗi yêu cầu được gắn nhãn nguồn gốc:

| Nhãn | Ý nghĩa |
|---|---|
| **[OBS]** | Đã quan sát trực tiếp trên hệ thống |
| **[ASM]** | Giả định theo hành vi chuẩn, cần chạy thử xác minh trước khi dùng làm expected result |

### `docs/testcase/`
Chứa các file test case thủ công, hiện có:

| File | Trạng thái |
|---|---|
| `TC_login.txt` | Mới có kịch bản đăng nhập thành công (các bước cơ bản) |
| `TC_Dashboard.txt` | Trống, đang chờ bổ sung |

## Quy trình làm việc đề xuất

1. Đọc SRS trong `docs/` để nắm yêu cầu và các điểm còn là giả định `[ASM]`.
2. Chạy thử trên hệ thống thật để xác minh các giả định.
3. Viết/bổ sung test case vào `docs/testcase/`.
4. Commit và push lên GitHub.

## Lưu ý

- Mật khẩu tài khoản kiểm thử **không** được lưu trong repo; hãy lấy từ người cung cấp.
- Hệ thống demo là môi trường công khai, dữ liệu có thể bị người khác thay đổi hoặc reset.

## Giấy phép

Phát hành theo [MIT License](LICENSE).
