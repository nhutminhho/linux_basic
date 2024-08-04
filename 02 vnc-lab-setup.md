# Hướng dẫn chi tiết thiết lập Lab VNC giữa Windows và AlmaLinux

## Phần 1: Cài đặt và cấu hình VNC Server trên AlmaLinux

### 1. Cập nhật hệ thống và cài đặt các gói cần thiết

```bash
# Cập nhật hệ thống
sudo dnf update -y

# Cài đặt giao diện đồ họa (nếu chưa có)
sudo dnf groupinstall "Server with GUI" -y

# Cài đặt TigerVNC server
sudo dnf install tigervnc-server -y

# Cài đặt GNOME (nếu muốn sử dụng GNOME thay vì mặc định)
sudo dnf install @gnome-desktop -y
```

### 2. Cấu hình VNC Server

```bash
# Tạo thư mục cấu hình VNC
mkdir -p ~/.vnc

# Tạo mật khẩu VNC (bạn sẽ được yêu cầu nhập mật khẩu hai lần)
vncpasswd

# Tạo file xstartup với nội dung cơ bản
cat > ~/.vnc/xstartup << EOF
#!/bin/sh
unset SESSION_MANAGER
unset DBUS_SESSION_BUS_ADDRESS
/etc/X11/xinit/xinitrc
vncconfig -iconic &
dbus-launch --exit-with-session gnome-session &
EOF

# Đặt quyền thực thi cho file xstartup
chmod +x ~/.vnc/xstartup
```

### 3. Cấu hình systemd service cho VNC

```bash
# Tạo file service cho VNC
sudo bash -c 'cat > /etc/systemd/system/vncserver@.service << EOF
[Unit]
Description=Remote desktop service (VNC)
After=syslog.target network.target

[Service]
Type=forking
User=$USER
WorkingDirectory=$HOME
PIDFile=$HOME/.vnc/%H:%i.pid
ExecStartPre=/bin/sh -c "/usr/bin/vncserver -kill :%i > /dev/null 2>&1 || :"
ExecStart=/usr/bin/vncserver :%i -geometry 1920x1080 -depth 24
ExecStop=/usr/bin/vncserver -kill :%i

[Install]
WantedBy=multi-user.target
EOF'
```

### 4. Khởi động VNC Server

```bash
# Reload systemd để nhận cấu hình mới
sudo systemctl daemon-reload

# Kích hoạt service VNC
sudo systemctl enable vncserver@1.service

# Khởi động service VNC
sudo systemctl start vncserver@1.service

# Kiểm tra trạng thái
sudo systemctl status vncserver@1.service
```

### 5. Cấu hình tường lửa

```bash
# Mở port cho VNC trên tường lửa
sudo firewall-cmd --permanent --add-service=vnc-server

# Nếu bạn muốn mở port cụ thể (ví dụ: 5901)
sudo firewall-cmd --permanent --add-port=5901/tcp

# Reload tường lửa để áp dụng cấu hình mới
sudo firewall-cmd --reload

# Kiểm tra cấu hình
sudo firewall-cmd --list-all
```

### 6. Cấu hình SELinux (nếu được bật)

```bash
# Cho phép VNC hoạt động với SELinux
sudo setsebool -P vnc_server_bind_vnc_port 1

# Nếu bạn thay đổi port mặc định, hãy thêm port mới
sudo semanage port -a -t vnc_port_t -p tcp 5901
```

## Phần 2: Cài đặt VNC Viewer trên Windows

1. Tải VNC Viewer từ trang chủ RealVNC: https://www.realvnc.com/en/connect/download/viewer/

2. Chạy file cài đặt và làm theo hướng dẫn trên màn hình.

3. Sau khi cài đặt, bạn có thể chạy VNC Viewer từ Start menu hoặc desktop shortcut.

## Phần 3: Kết nối từ Windows đến AlmaLinux

1. Mở VNC Viewer trên máy Windows.

2. Trong trường "VNC Server", nhập địa chỉ IP của máy AlmaLinux và cổng VNC (mặc định là 5901 cho phiên :1):
   ```
   IP_ADDRESS:5901
   ```

3. Nhấn Enter hoặc click "Connect".

4. Khi được hỏi, nhập mật khẩu VNC bạn đã tạo ở bước 2 của phần 1.

5. Chấp nhận cảnh báo bảo mật (nếu có).

6. Bạn sẽ thấy màn hình desktop của AlmaLinux.

## Phần 4: Bảo mật kết nối VNC (Tùy chọn nhưng khuyến nghị)

### 1. Cài đặt và cấu hình SSH trên AlmaLinux (nếu chưa có)

```bash
# Cài đặt OpenSSH server (nếu chưa có)
sudo dnf install openssh-server -y

# Khởi động và kích hoạt SSH service
sudo systemctl start sshd
sudo systemctl enable sshd

# Mở port SSH trên tường lửa
sudo firewall-cmd --permanent --add-service=ssh
sudo firewall-cmd --reload
```

### 2. Tạo tunnel SSH từ Windows

Trên Windows, bạn có thể sử dụng PuTTY hoặc OpenSSH:

Sử dụng OpenSSH (có sẵn trên Windows 10 trở lên):

```bash
ssh -L 5901:localhost:5901 YOUR_USERNAME@IP_ADDRESS
```

Hoặc sử dụng PuTTY:
1. Mở PuTTY
2. Nhập địa chỉ IP của máy AlmaLinux
3. Đi tới Connection > SSH > Tunnels
4. Source port: 5901
5. Destination: localhost:5901
6. Click "Add", sau đó "Open"
7. Đăng nhập bằng tài khoản AlmaLinux của bạn

### 3. Kết nối qua tunnel

Trong VNC Viewer, kết nối đến:

```
localhost:5901
```

## Phần 5: Xử lý sự cố

1. Kiểm tra trạng thái VNC Server:
   ```bash
   sudo systemctl status vncserver@1.service
   ```

2. Xem log VNC Server:
   ```bash
   tail -f ~/.vnc/*.log
   ```

3. Kiểm tra port đang lắng nghe:
   ```bash
   sudo ss -tulpn | grep vnc
   ```

4. Kiểm tra cấu hình tường lửa:
   ```bash
   sudo firewall-cmd --list-all
   ```

5. Nếu gặp vấn đề với giao diện, thử thay đổi desktop environment trong file `~/.vnc/xstartup`. Ví dụ, để sử dụng GNOME:
   ```bash
   echo "exec /usr/bin/gnome-session" >> ~/.vnc/xstartup
   ```

6. Nếu màn hình xám hoặc đen, thử khởi động lại VNC server:
   ```bash
   sudo systemctl restart vncserver@1.service
   ```

7. Kiểm tra log hệ thống để tìm lỗi liên quan đến VNC:
   ```bash
   sudo journalctl -u vncserver@1.service
   ```

8. Nếu kết nối chậm, thử giảm chất lượng hình ảnh trong VNC Viewer hoặc thay đổi cấu hình mạng.

