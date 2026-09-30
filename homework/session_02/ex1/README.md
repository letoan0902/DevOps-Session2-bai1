# Bài 1: Khởi tạo Droplet trên DigitalOcean

## Mục lục

1. [Mục tiêu](#1-mục-tiêu)
2. [Bước 1: Hướng dẫn tạo Droplet trên DigitalOcean Console](#2-bước-1-hướng-dẫn-tạo-droplet-trên-digitalocean-console)
3. [Bước 2: Tạo cặp khóa SSH trên máy cá nhân](#3-bước-2-tạo-cặp-khóa-ssh-trên-máy-cá-nhân)
4. [Bước 3: Thêm public key vào tài khoản DigitalOcean và gán vào Droplet](#4-bước-3-thêm-public-key-vào-tài-khoản-digitalocean-và-gán-vào-droplet)
5. [Bước 4: Kết nối SSH kiểm tra](#5-bước-4-kết-nối-ssh-kiểm-tra)
6. [Ghi chú về việc thay ảnh DO Console bằng log Terminal](#6-ghi-chú-về-việc-thay-ảnh-do-console-bằng-log-terminal)
7. [Kết luận](#7-kết-luận)

## 1. Mục tiêu

- Nắm quy trình đăng ký tài khoản, chọn cấu hình phần cứng phù hợp và khởi tạo Droplet Ubuntu Server trên DigitalOcean.
- Cấu hình xác thực an toàn bằng cặp khóa SSH (ed25519) thay cho mật khẩu.
- Kết nối thành công từ máy cá nhân tới máy chủ bằng người dùng `root`, không cần nhập mật khẩu, và thấy thông điệp chào mừng Ubuntu.

Các ràng buộc của đề bài:

| Ràng buộc | Yêu cầu |
|---|---|
| Hệ điều hành | Ubuntu 22.04 LTS (hoặc phiên bản LTS mới nhất) |
| Gói dịch vụ | Basic, Regular SSD, CPU Shared rẻ nhất (4 USD hoặc 6 USD mỗi tháng) |
| Region | Singapore |
| Xác thực | Bắt buộc dùng SSH Key, tạo mới từ máy cá nhân, thêm public key vào tài khoản DO và gán vào Droplet |
| Kiểm tra | `ssh -i /path/to/private_key root@<IP>` thành công, không cần mật khẩu |

## 2. Bước 1: Hướng dẫn tạo Droplet trên DigitalOcean Console

Phần này là hướng dẫn thao tác trên giao diện DigitalOcean (xem thêm mục 6 về việc thực hiện thực tế).

### 2.1. Đăng ký tài khoản và đăng nhập

1. Truy cập `https://www.digitalocean.com` và chọn **Sign up**.
2. Đăng ký bằng email, tài khoản Google hoặc GitHub, sau đó xác minh email.
3. Khai báo phương thức thanh toán (thẻ tín dụng/ghi nợ quốc tế hoặc PayPal) để kích hoạt tài khoản. DigitalOcean có thể tạm giữ một khoản nhỏ để xác minh thẻ.
4. Đăng nhập vào Control Panel (`cloud.digitalocean.com`) và tạo hoặc chọn một **Project** (ví dụ `devops-session02`) để quản lý tài nguyên.

### 2.2. Mở màn hình tạo Droplet

Tại Control Panel, chọn nút **Create** màu xanh ở góc trên bên phải, sau đó chọn **Droplets**. Màn hình **Create Droplets** hiển thị các mục cấu hình theo thứ tự từ trên xuống dưới.

### 2.3. Các mục cấu hình

1. **Choose Region**: chọn **Singapore**, Datacenter **SGP1**. Đây là region gần Việt Nam nhất nên độ trễ thấp nhất.
2. **Choose an image**: ở tab **OS**, chọn **Ubuntu**, sau đó chọn phiên bản **22.04 (LTS) x64** (có thể chọn bản LTS mới hơn nếu được liệt kê).
3. **Choose Size**:
   - **Droplet Type**: chọn **Basic**.
   - **CPU options**: chọn **Regular** với đĩa **SSD** (Regular Disk type: SSD).
   - Chọn gói rẻ nhất: **4 USD/tháng** (512 MiB RAM, 1 vCPU, 10 GB SSD, 500 GB truyền tải) hoặc **6 USD/tháng** (1 GiB RAM, 1 vCPU, 25 GB SSD, 1 TB truyền tải). Tùy thời điểm, gói 4 USD có thể không còn hiển thị tại một số region; khi đó chọn gói 6 USD.
4. **Choose Authentication Method**: chọn **SSH Key** (không chọn Password).
   - Nhấn **Add SSH Key** (hoặc **New SSH Key**).
   - Dán toàn bộ nội dung file public key (`~/.ssh/id_ed25519_ptit_s02.pub`, bắt đầu bằng `ssh-ed25519 ...`) vào ô **SSH key content**.
   - Đặt tên cho khóa ở ô **Name**, ví dụ `ptit-devops-session02`, rồi nhấn **Add SSH Key**.
   - Đảm bảo khóa vừa thêm được **tích chọn** để gán vào Droplet.
5. **Additional options** (tùy chọn): bỏ qua, không cần bật Monitoring, Backups hay IPv6 cho bài này.
6. **Finalize Details**:
   - **Quantity**: 1 Droplet.
   - **Hostname**: đặt tên dễ nhớ, ví dụ `ubuntu-s-1vcpu-1gb-sgp1-devops02`.
   - **Project**: chọn project đã tạo.
7. Kiểm tra lại tóm tắt chi phí bên phải màn hình, sau đó nhấn **Create Droplet**.

Sau khoảng 30 đến 60 giây, Droplet xuất hiện trong danh sách kèm địa chỉ IPv4 public. Sao chép địa chỉ IP này để dùng ở Bước 4.

### 2.4. Bảng cấu hình lựa chọn và lý do

| Mục | Lựa chọn | Lý do |
|---|---|---|
| Region | Singapore (SGP1) | Theo yêu cầu đề bài; gần Việt Nam nên độ trễ SSH thấp |
| Image | Ubuntu 22.04 LTS x64 | Bản LTS có hỗ trợ dài hạn, ổn định, tài liệu phong phú |
| Droplet Type | Basic | Phù hợp học tập và workload nhỏ, theo yêu cầu đề bài |
| CPU option | Regular (Shared CPU), đĩa SSD | Gói rẻ nhất, đủ cho bài thực hành |
| Kích thước | 4 USD hoặc 6 USD mỗi tháng | Tối thiểu hóa chi phí; 1 vCPU là đủ để SSH và chạy lệnh cơ bản |
| Authentication | SSH Key (ed25519) | An toàn hơn mật khẩu: chống dò mật khẩu (brute force), không truyền bí mật qua mạng; ed25519 ngắn, nhanh và an toàn |
| Hostname | Tên gợi nhớ, không chứa thông tin nhạy cảm | Dễ phân biệt khi quản lý nhiều Droplet |

## 3. Bước 2: Tạo cặp khóa SSH trên máy cá nhân

Máy cá nhân chạy Windows 11, sử dụng Git Bash. Lệnh tạo khóa (không đặt passphrase để phục vụ kiểm tra không tương tác; trong thực tế nên đặt passphrase):

```bash
ssh-keygen -t ed25519 -C "ptit-devops-session02" -f ~/.ssh/id_ed25519_ptit_s02 -N ""
```

Trong đó:

- `-t ed25519`: thuật toán khóa Ed25519.
- `-C "ptit-devops-session02"`: phần chú thích (comment) gắn vào public key để nhận diện.
- `-f ~/.ssh/id_ed25519_ptit_s02`: đường dẫn file private key; file public key là `id_ed25519_ptit_s02.pub`.
- `-N ""`: passphrase rỗng.

Output thực tế:

```text
Generating public/private ed25519 key pair.
Your identification has been saved in /c/Users/gigabyte/.ssh/id_ed25519_ptit_s02
Your public key has been saved in /c/Users/gigabyte/.ssh/id_ed25519_ptit_s02.pub
The key fingerprint is:
SHA256:O7vKiG2yD6qmCoRboJ8jFJ4aMVWlP11gbIqWirlAOSo ptit-devops-session02
+--[ED25519 256]--+
|  ......o        |
| .   . .o.       |
|+.. .o o  .      |
|+Bo +... .       |
|*Boo  o S        |
|E=..   . .       |
|B.=     o        |
|o+.=.o   o       |
|X o=+ o.o.       |
+----[SHA256]-----+
```

Kiểm tra fingerprint và nội dung public key:

```bash
ssh-keygen -lf ~/.ssh/id_ed25519_ptit_s02.pub
cat ~/.ssh/id_ed25519_ptit_s02.pub
```

```text
256 SHA256:O7vKiG2yD6qmCoRboJ8jFJ4aMVWlP11gbIqWirlAOSo ptit-devops-session02 (ED25519)
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIBi1p0hUZj4uTUzIuDJEZeHgd0gf0RJkK4pMRjo/9dlN ptit-devops-session02
```

| Thông tin | Giá trị |
|---|---|
| Thuật toán | Ed25519 (256 bit) |
| Private key (không nộp, không commit) | `~/.ssh/id_ed25519_ptit_s02` |
| Public key (được nộp) | `~/.ssh/id_ed25519_ptit_s02.pub`, bản sao tại [ssh_public_key.pub](ssh_public_key.pub) |
| Fingerprint | `SHA256:O7vKiG2yD6qmCoRboJ8jFJ4aMVWlP11gbIqWirlAOSo` |

Lưu ý bảo mật: chỉ public key (file `.pub`) được đưa vào báo cáo và repository. File private key tuyệt đối không được chia sẻ hay commit; repository đã có `.gitignore` chặn các file `*.pem` và `id_*` (trừ `*.pub`).

## 4. Bước 3: Thêm public key vào tài khoản DigitalOcean và gán vào Droplet

### 4.1. Hướng dẫn trên DigitalOcean

Có hai cách thêm public key vào tài khoản DO:

**Cách 1: thêm ngay trong lúc tạo Droplet** (đã mô tả ở mục 2.3, bước 4): tại phần **Authentication Method**, chọn **SSH Key**, nhấn **Add SSH Key**, dán nội dung public key, đặt tên và lưu. Khóa được lưu vào tài khoản để tái sử dụng cho các Droplet sau.

**Cách 2: thêm trước trong phần cài đặt tài khoản**:

1. Vào **Settings** (menu bên trái) và chọn tab **Security**.
2. Ở mục **SSH keys**, nhấn **Add SSH Key**.
3. Dán nội dung public key (một dòng duy nhất bắt đầu bằng `ssh-ed25519`), đặt tên `ptit-devops-session02`, nhấn **Add SSH Key**.
4. Khi tạo Droplet, tại phần Authentication chọn **SSH Key** và tích chọn khóa này để gán vào Droplet.

Khi Droplet được khởi tạo, DigitalOcean tự ghi public key vào file `/root/.ssh/authorized_keys` của người dùng `root`, và đăng nhập bằng mật khẩu không được bật khi đã chọn SSH Key, nên không cần thao tác thêm trên máy chủ.

### 4.2. Thao tác thực tế trên VPS có sẵn

Vì không có Droplet, bước "DO ghi public key vào `authorized_keys`" được thực hiện thủ công trên VPS Ubuntu có sẵn (`root@103.116.104.228`, hostname `halo`). Thao tác này tương đương cơ chế DO thực hiện khi gán SSH key. Các nguyên tắc áp dụng: sao lưu file trước khi sửa, thêm idempotent (không thêm trùng), giữ quyền `600`, không chạm vào bất kỳ cấu hình hay dịch vụ nào khác của máy chủ.

Lệnh đã chạy trên máy chủ (kết nối bằng khóa cũ đã được máy chủ chấp nhận):

```bash
cp -n /root/.ssh/authorized_keys /root/.ssh/authorized_keys.bak-session02
if grep -qF 'AAAAC3NzaC1lZDI1NTE5AAAAIBi1p0hUZj4uTUzIuDJEZeHgd0gf0RJkK4pMRjo/9dlN' /root/.ssh/authorized_keys; then
  echo 'DA_TON_TAI'
else
  echo '<noi dung public key>' >> /root/.ssh/authorized_keys
  echo 'DA_THEM'
fi
chmod 600 /root/.ssh/authorized_keys
```

Output thực tế (lược bỏ các dòng liệt kê không liên quan; phần cuối là cột loại khóa và comment của từng khóa trong `authorized_keys`):

```text
-rw------- 1 root root 98 Jul 27 02:54 /root/.ssh/authorized_keys
cp: warning: behavior of -n is non-portable and may change in future; use --update=none instead
DA_THEM
-rw------- 1 root root 201 Sep 30 19:21 authorized_keys
-rw------- 1 root root  98 Sep 30 19:21 authorized_keys.bak-session02
  2 /root/.ssh/authorized_keys
  1 /root/.ssh/authorized_keys.bak-session02
  3 total
ssh-ed25519 gigabyte@LETOAN
ssh-ed25519 ptit-devops-session02
```

Kết quả: dòng đầu là trạng thái file trước khi thêm (98 byte, 1 dòng); sau khi thêm, file `authorized_keys` có 2 dòng (201 byte), khóa cũ được giữ nguyên, khóa mới `ptit-devops-session02` được thêm vào, quyền file vẫn là `600`, và có bản sao lưu `authorized_keys.bak-session02`. Cảnh báo của `cp -n` chỉ là thông báo về tùy chọn không còn khuyến nghị, không ảnh hưởng kết quả.

## 5. Bước 4: Kết nối SSH kiểm tra

Lệnh kiểm tra theo đúng cú pháp của đề bài, chỉ định rõ khóa mới và ép SSH chỉ dùng khóa này (`IdentitiesOnly=yes`), chế độ không tương tác (`BatchMode=yes`) để chứng minh không có bước nhập mật khẩu hay passphrase:

```bash
ssh -i ~/.ssh/id_ed25519_ptit_s02 -o BatchMode=yes -o IdentitiesOnly=yes root@103.116.104.228 \
  'hostname; whoami; lsb_release -a; uptime; run-parts /etc/update-motd.d/ 2>/dev/null | head -30'
```

Log thực tế từ Terminal:

```text
halo
root
Distributor ID:	Ubuntu
Description:	Ubuntu 24.04.3 LTS
Release:	24.04
Codename:	noble
 19:21:45 up 65 days, 17:21,  8 users,  load average: 0.40, 0.27, 0.28
Welcome to Ubuntu 24.04.3 LTS (GNU/Linux 6.8.0-87-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

 System information as of Wed Sep 30 07:21:35 PM UTC 2026

  System load:  0.38               Temperature:            100.0 C
  Usage of /:   69.9% of 47.29GB   Processes:              247
  Memory usage: 14%                Users logged in:        0
  Swap usage:   0%                 IPv4 address for ens32: 103.116.104.228

 * Canonical Workshop gives developers fast, composable, reproducible, and
   secure developer environments that are perfect for agentic workflows.

   https://ubuntu.com/workshop

Expanded Security Maintenance for Applications is not enabled.

96 updates can be applied immediately.
To see these additional updates run: apt list --upgradable

16 additional security updates can be applied with ESM Apps.
Learn more about enabling ESM Apps service at https://ubuntu.com/esm

New release '26.04.1 LTS' available.
Run 'do-release-upgrade' to upgrade to it.
```

Phân tích kết quả:

- Lệnh chạy xong với `BatchMode=yes` mà không có lời nhắc nhập mật khẩu, chứng tỏ xác thực bằng khóa SSH thành công.
- `whoami` trả về `root`, xác nhận kết nối bằng người dùng `root` như đề bài yêu cầu.
- Thông điệp `Welcome to Ubuntu 24.04.3 LTS ...` (lấy từ `/etc/update-motd.d/`) chính là thông tin chào mừng Ubuntu. Lệnh `run-parts` được dùng vì khi chạy SSH kèm một lệnh cụ thể, máy chủ không in MOTD như khi đăng nhập shell tương tác.
- Khi đăng nhập shell tương tác (`ssh -i ~/.ssh/id_ed25519_ptit_s02 root@103.116.104.228`), cùng nội dung này được hiển thị ngay sau khi kết nối.

## 6. Ghi chú về việc thay ảnh DO Console bằng log Terminal

Đề bài cho phép nộp ảnh chụp DO Console hoặc đoạn log kết nối thành công từ Terminal. Do người thực hiện không có tài khoản DigitalOcean, bài làm này không tạo Droplet thật. Thay vào đó:

- Phần tạo Droplet (Bước 1 và Bước 3, mục 4.1) được viết thành hướng dẫn chi tiết từng bước theo giao diện DigitalOcean.
- Phần ảnh chụp DO Console được thay bằng log Terminal thật, chạy trên một VPS Ubuntu có sẵn của người thực hiện (`root@103.116.104.228`, hostname `halo`).
- Khác biệt cần lưu ý: VPS này chạy **Ubuntu 24.04.3 LTS** (bản LTS mới hơn 22.04, vẫn thỏa yêu cầu "22.04 LTS hoặc mới nhất"), không phải Droplet tại Singapore với gói Basic 4/6 USD. Các phần tạo khóa, thêm public key và kết nối SSH bằng `root` không dùng mật khẩu là thao tác thật, tương đương với quy trình trên Droplet.
- Toàn bộ output trong báo cáo là kết quả thực tế của các lệnh đã chạy, không bịa đặt.

## 7. Kết luận

- Đã tạo mới cặp khóa SSH Ed25519 trên máy cá nhân (fingerprint `SHA256:O7vKiG2yD6qmCoRboJ8jFJ4aMVWlP11gbIqWirlAOSo`), private key được giữ kín, chỉ public key được nộp.
- Đã mô tả chi tiết quy trình tạo Droplet trên DigitalOcean đúng ràng buộc: Ubuntu 22.04 LTS, Basic, Regular SSD, CPU Shared gói 4 hoặc 6 USD, region Singapore, xác thực bằng SSH Key.
- Đã thêm public key vào máy chủ theo cách an toàn (sao lưu, không trùng lặp, giữ quyền `600`) và kết nối thành công bằng `ssh -i <private_key> root@<IP>` mà không cần mật khẩu, hiển thị thông điệp chào mừng Ubuntu.
- Kinh nghiệm rút ra: xác thực bằng khóa SSH loại bỏ rủi ro dò mật khẩu, cho phép tự động hóa kết nối (CI/CD, Ansible). Trong môi trường thực tế nên đặt passphrase cho private key, vô hiệu hóa đăng nhập bằng mật khẩu và hạn chế đăng nhập `root` trực tiếp.
