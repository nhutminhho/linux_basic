# Hướng dẫn chi tiết thiết lập Lab SSH trên AlmaLinux

## 1. Cài đặt và khởi động SSH server

```bash
# Cài đặt OpenSSH server
sudo dnf install openssh-server -y

# Khởi động dịch vụ SSH
sudo systemctl start sshd

# Cấu hình SSH khởi động cùng hệ thống
sudo systemctl enable sshd

# Kiểm tra trạng thái dịch vụ SSH
sudo systemctl status sshd
```

## 2. Cấu hình SSH server

```bash
# Sao lưu file cấu hình gốc
sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.bak

# Chỉnh sửa file cấu hình SSH
sudo nano /etc/ssh/sshd_config
```

Thực hiện các thay đổi sau trong file cấu hình:

```
Port 22
PermitRootLogin no
PasswordAuthentication yes
PubkeyAuthentication yes
```

Để nhanh chóng áp dụng các thay đổi này, bạn có thể sử dụng các lệnh sed:

```bash
# Đặt Port
sudo sed -i 's/#Port 22/Port 22/' /etc/ssh/sshd_config

# Tắt đăng nhập root
sudo sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin no/' /etc/ssh/sshd_config

# Bật xác thực mật khẩu
sudo sed -i 's/#PasswordAuthentication yes/PasswordAuthentication yes/' /etc/ssh/sshd_config

# Bật xác thực khóa công khai
sudo sed -i 's/#PubkeyAuthentication yes/PubkeyAuthentication yes/' /etc/ssh/sshd_config

# Khởi động lại dịch vụ SSH để áp dụng thay đổi
sudo systemctl restart sshd
```

## 3. Tạo và quản lý khóa SSH

Trên máy client:

```bash
# Tạo cặp khóa SSH mới
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"

# Hiển thị và sao chép khóa công khai
cat ~/.ssh/id_rsa.pub
```

## 4. Thiết lập xác thực bằng khóa

Trên máy server:

```bash
# Tạo thư mục .ssh nếu chưa tồn tại
mkdir -p ~/.ssh

# Tạo file authorized_keys nếu chưa tồn tại
touch ~/.ssh/authorized_keys

# Đặt quyền chính xác
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys

# Thêm khóa công khai vào file authorized_keys
echo "your_public_key_here" >> ~/.ssh/authorized_keys
```

Thay thế `your_public_key_here` bằng nội dung khóa công khai bạn đã sao chép từ máy client.

Hoặc, bạn có thể sử dụng lệnh `ssh-copy-id` từ máy client:

```bash
ssh-copy-id username@server_ip
```

## 5. Cấu hình tường lửa

```bash
# Mở port SSH trên tường lửa
sudo firewall-cmd --permanent --add-service=ssh

# Nếu bạn thay đổi port SSH, sử dụng lệnh sau (thay 2222 bằng port bạn chọn)
# sudo firewall-cmd --permanent --add-port=2222/tcp

# Tải lại cấu hình tường lửa
sudo firewall-cmd --reload

# Kiểm tra cấu hình
sudo firewall-cmd --list-all
```

## 6. Cấu hình SELinux (nếu được bật)

Nếu bạn thay đổi port SSH mặc định:

```bash
# Cài đặt công cụ policycoreutils-python-utils
sudo dnf install policycoreutils-python-utils -y

# Cấu hình SELinux cho port SSH mới (thay 2222 bằng port bạn chọn)
sudo semanage port -a -t ssh_port_t -p tcp 2222

# Kiểm tra cấu hình
sudo semanage port -l | grep ssh
```

## 7. Cấu hình bảo mật bổ sung

### a. Cài đặt và cấu hình Fail2Ban

```bash
# Cài đặt Fail2Ban
sudo dnf install fail2ban -y

# Tạo file cấu hình local
sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local

# Chỉnh sửa file cấu hình
sudo nano /etc/fail2ban/jail.local
```

Thêm hoặc chỉnh sửa các dòng sau trong phần `[sshd]`:

```
[sshd]
enabled = true
port = ssh
filter = sshd
logpath = /var/log/secure
maxretry = 3
bantime = 3600
```

```bash
# Khởi động và kích hoạt Fail2Ban
sudo systemctl start fail2ban
sudo systemctl enable fail2ban

# Kiểm tra trạng thái Fail2Ban
sudo fail2ban-client status
```

### b. Tắt xác thực bằng mật khẩu (sau khi đã thiết lập xác thực khóa)

```bash
# Tắt xác thực bằng mật khẩu
sudo sed -i 's/PasswordAuthentication yes/PasswordAuthentication no/' /etc/ssh/sshd_config

# Khởi động lại dịch vụ SSH
sudo systemctl restart sshd
```

## 8. Kiểm tra và giám sát

```bash
# Kiểm tra log SSH
sudo tail -f /var/log/secure

# Xem các kết nối SSH hiện tại
sudo netstat -tnlp | grep ssh

# Kiểm tra cấu hình SSH
sudo sshd -T
```

## 9. Thử nghiệm kết nối

Từ máy client:

```bash
# Kết nối SSH (thay username và server_ip)
ssh username@server_ip

# Nếu bạn đã thay đổi port, sử dụng lệnh:
# ssh -p 2222 username@server_ip
```

Hãy đảm bảo thay thế `username`, `server_ip`, và số port (nếu đã thay đổi) bằng thông tin thực tế của bạn.

