# Bài 2: Khởi tạo User thường và thiết lập đặc quyền quản trị (Sudoers Configuration)

## Mục tiêu
- Tạo một tài khoản người dùng thường (Non-root user) trên Ubuntu Server để hạn chế rủi ro khi thao tác trực tiếp bằng quyền root tối cao.
- Cấp đặc quyền quản trị an toàn cho user thường thông qua nhóm sudo và sao chép cấu hình SSH Key để đăng nhập.

## Các bước thực hiện chi tiết

### Bước 1: Tạo tài khoản người dùng mới (`devops`)
Từ máy cá nhân, đăng nhập vào Droplet bằng tài khoản `root` qua SSH như đã làm ở bài trước. 
Sau khi kết nối thành công, chạy lệnh sau để tạo một user mới có tên là `devops`:
```bash
adduser devops
```
Hệ thống sẽ yêu cầu bạn nhập mật khẩu cho tài khoản mới này và nhập lại để xác nhận. Đối với các thông tin bổ sung (Full Name, Room Number, ...), bạn chỉ cần nhấn `Enter` để bỏ qua và chọn `Y` để xác nhận thông tin.

### Bước 2: Cấp quyền quản trị (Sudo) cho user mới
Để tài khoản `devops` có thể thực thi các lệnh hệ thống yêu cầu quyền quản trị, ta cần thêm tài khoản này vào nhóm `sudo` bằng lệnh sau:
```bash
usermod -aG sudo devops
```

### Bước 3: Cấu hình SSH Key cho user mới
Để cho phép đăng nhập vào tài khoản `devops` bằng SSH Key (giống như user root), ta cần đưa khóa công khai (Public Key) vào thư mục cấu hình của `devops`. 
Vì ta đã gán SSH Key cho `root` lúc khởi tạo máy chủ, ta sẽ dùng lệnh `rsync` sao chép thư mục `.ssh` từ root sang thư mục home của user `devops` một cách an toàn và tự động cấp lại quyền sở hữu:
```bash
rsync --archive --chown=devops:devops ~/.ssh /home/devops/
```
Lệnh trên thực hiện việc:
- Copy toàn bộ cấu hình xác thực SSH (`.ssh/authorized_keys`).
- Thay đổi quyền sở hữu (owner) của thư mục và file cấu hình thành `devops:devops` (để tránh lỗi permission denied do SSH rất khắt khe về bảo mật).

### Bước 4: Đăng nhập kiểm tra
Thoát khỏi phiên kết nối SSH hiện tại (bằng cách gõ lệnh `exit` hoặc mở tab Terminal mới trên máy cá nhân). 
Thử đăng nhập lại vào máy chủ với user `devops` thay vì `root`:
```bash
ssh -i ~/.ssh/id_ed25519 devops@<IP_ADDRESS_DROPLET>
```
*(Nhớ thay đổi `<IP_ADDRESS_DROPLET>` bằng địa chỉ IP của Droplet và trỏ đúng đường dẫn tới file Private Key của bạn).*

Khi đăng nhập thành công vào `devops`, kiểm tra đặc quyền quản trị (sudo) bằng lệnh:
```bash
sudo whoami
```
Nếu đây là lần đầu tiên sử dụng `sudo` ở phiên đăng nhập hiện tại, hệ thống sẽ yêu cầu bạn nhập mật khẩu của user `devops`. Kết quả in ra trên màn hình phải là `root`.

> *Chèn ảnh chụp màn hình log Terminal đăng nhập bằng devops và chạy thành công lệnh `sudo whoami` vào đây:*
> 
> `[Chèn ảnh chụp log Terminal tại đây]`
