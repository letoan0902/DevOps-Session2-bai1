# DevOps - Session 02 - Bài 1: Khởi tạo Droplet trên DigitalOcean

Repository này chứa bài nộp Bài 1 của môn DevOps, Session 02. Nội dung bài tập là đăng ký tài khoản, chọn cấu hình phần cứng phù hợp và khởi tạo Droplet chạy Ubuntu Server trên DigitalOcean. Droplet xác thực bằng cặp khóa SSH (ed25519) thay cho mật khẩu, và kết nối thành công từ máy cá nhân bằng người dùng `root`.

## Mục lục

| Nội dung | Đường dẫn |
|---|---|
| Báo cáo chi tiết (các bước, lệnh, log kết nối) | [homework/session_02/ex1/README.md](homework/session_02/ex1/README.md) |
| Public key dùng cho bài | [homework/session_02/ex1/ssh_public_key.pub](homework/session_02/ex1/ssh_public_key.pub) |

## Tóm tắt

- Cặp khóa SSH mới được tạo trên máy cá nhân bằng `ssh-keygen -t ed25519`.
- Public key được thêm vào máy chủ; private key chỉ nằm trên máy cá nhân và không được đưa vào repository (xem `.gitignore`).
- Kết nối kiểm tra bằng `ssh -i <private_key> root@<IP>` thành công, không cần mật khẩu, hiển thị thông điệp chào mừng Ubuntu.
- Lưu ý: do không có tài khoản DigitalOcean, phần ảnh chụp DO Console được thay bằng log Terminal thực tế trên một VPS Ubuntu có sẵn. Chi tiết xem trong báo cáo.
