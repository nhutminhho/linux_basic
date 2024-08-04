# Bài Lab: File Permissions và File Ownership trong Linux

## Phần 1: Lý thuyết

### File Permissions
- 3 loại quyền:
  - r (read): Quyền đọc
  - w (write): Quyền ghi
  - x (execute): Quyền thực thi (chạy chương trình)

- 3 cấp độ kiểm soát:
  - u (user): Chủ sở hữu file
  - g (group): Nhóm
  - o (other): Người dùng khác

- Hiển thị quyền: `ls -l`
  Ví dụ: `-rwxrwxrwx`

- Thay đổi quyền: `chmod`

### File Ownership
- 2 loại chủ sở hữu:
  - User
  - Group

- Thay đổi chủ sở hữu:
  - `chown`: Thay đổi chủ sở hữu user
  - `chgrp`: Thay đổi chủ sở hữu group

- Tùy chọn đệ quy: `-R`

## Phần 2: Ví dụ minh họa

### Ví dụ 1: Hiển thị và giải thích quyền file

```bash
ls -l /etc/passwd
```

Kết quả có thể như sau:
```
-rw-r--r-- 1 root root 2890 May 28 2022 /etc/passwd
```

Giải thích:
- `-`: File thông thường (d nếu là thư mục)
- `rw-`: User có quyền đọc và ghi
- `r--`: Group có quyền đọc
- `r--`: Others có quyền đọc
- `root root`: Chủ sở hữu user và group là root

### Ví dụ 2: Thay đổi quyền file

```bash
# Tạo file test
echo "Hello World" > test.txt

# Thêm quyền thực thi cho user
chmod u+x test.txt

# Xóa quyền đọc cho others
chmod o-r test.txt

# Đặt quyền cụ thể (rwx cho user, rx cho group và other)
chmod 755 test.txt
```

## Phần 3: Bài tập thực hành

### Bài tập 1: Quản lý quyền file

1. Tạo một thư mục có tên `secure_files`.
2. Trong thư mục `secure_files`, tạo một file có tên `confidential.txt`.
3. Đặt quyền cho `confidential.txt` sao cho:
   - User có toàn quyền
   - Group chỉ có quyền đọc
   - Others không có quyền gì
4. Tạo một script `secret.sh` trong `secure_files` với nội dung:
   ```bash
   #!/bin/bash
   echo "This is a secret script"
   ```
5. Đặt quyền cho `secret.sh` sao cho chỉ user mới có thể đọc và thực thi.

Bài giải:

```bash
mkdir secure_files
cd secure_files
echo "Top secret information" > confidential.txt
chmod 640 confidential.txt
echo '#!/bin/bash
echo "This is a secret script"' > secret.sh
chmod 500 secret.sh
```

### Bài tập 2: Thay đổi chủ sở hữu

1. Tạo một user mới có tên `projectx`.
2. Tạo một group mới có tên `developers`.
3. Thêm user `projectx` vào group `developers`.
4. Tạo một thư mục `project_files` và một file `readme.txt` trong đó.
5. Thay đổi chủ sở hữu của `project_files` và tất cả nội dung bên trong thành `projectx:developers`.

Bài giải:

```bash
sudo useradd projectx
sudo groupadd developers
sudo usermod -aG developers projectx
mkdir project_files
echo "Welcome to Project X" > project_files/readme.txt
sudo chown -R projectx:developers project_files
```

## Phần 4: Ứng dụng thực tế

### Tình huống 1: Thiết lập môi trường phát triển web

Giả sử bạn đang thiết lập một máy chủ web cho một nhóm phát triển. Bạn cần đảm bảo rằng các file web có thể được đọc bởi web server, nhưng chỉ có thể được chỉnh sửa bởi nhóm phát triển.

```bash
# Tạo thư mục cho website
sudo mkdir -p /var/www/mywebsite

# Tạo group cho developers
sudo groupadd webdev

# Thêm user hiện tại vào group webdev
sudo usermod -aG webdev $USER

# Đặt quyền sở hữu cho thư mục web
sudo chown -R $USER:webdev /var/www/mywebsite

# Đặt quyền cho thư mục (755 - rwxr-xr-x)
sudo chmod -R 755 /var/www/mywebsite

# Đặt GID bit để đảm bảo các file mới tạo thuộc về group webdev
sudo chmod g+s /var/www/mywebsite

# Tạo file index.html
echo "<html><body><h1>Welcome to My Website</h1></body></html>" > /var/www/mywebsite/index.html

# Đặt quyền cho file (644 - rw-r--r--)
chmod 644 /var/www/mywebsite/index.html
```

### Tình huống 2: Bảo vệ file cấu hình nhạy cảm

Giả sử bạn có một file cấu hình chứa thông tin nhạy cảm (ví dụ: thông tin đăng nhập database) và bạn muốn chỉ cho phép một số user cụ thể truy cập.

```bash
# Tạo file cấu hình
echo "DB_PASSWORD=supersecret" > db_config.ini

# Tạo group cho những người được phép truy cập
sudo groupadd dbadmins

# Thêm users vào group (giả sử có users: john và sarah)
sudo usermod -aG dbadmins john
sudo usermod -aG dbadmins sarah

# Thay đổi group sở hữu của file
sudo chgrp dbadmins db_config.ini

# Đặt quyền chỉ cho phép user và group đọc và ghi (660 - rw-rw----)
sudo chmod 660 db_config.ini

# Kiểm tra kết quả
ls -l db_config.ini
```

## Kết luận

Trong bài lab này, chúng ta đã học về cách quản lý File Permissions và File Ownership trong Linux. Chúng ta đã thực hành việc đặt và thay đổi quyền file, tạo users và groups, và áp dụng những kiến thức này vào các tình huống thực tế như thiết lập môi trường phát triển web và bảo vệ file cấu hình nhạy cảm.

Hiểu và sử dụng đúng cách File Permissions và File Ownership là rất quan trọng trong việc bảo mật hệ thống Linux và quản lý quyền truy cập của người dùng. Bằng cách áp dụng các nguyên tắc này, bạn có thể đảm bảo rằng mỗi user chỉ có quyền truy cập vào những resources cần thiết cho công việc của họ, giúp tăng cường bảo mật và tổ chức hệ thống tốt hơn.

