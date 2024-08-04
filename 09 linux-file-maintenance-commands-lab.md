# Bài Lab: Các Lệnh Bảo Trì File trong Linux

## Phần 1: Lý thuyết

### 1. cp (copy)
- Sao chép files và directories
- Cú pháp: `cp [OPTIONS] SOURCE DESTINATION`

### 2. rm (remove)
- Xóa files và directories
- Cú pháp: `rm [OPTIONS] FILE...`

### 3. mv (move)
- Di chuyển hoặc đổi tên files và directories
- Cú pháp: `mv [OPTIONS] SOURCE DESTINATION`

### 4. mkdir (make directory)
- Tạo directories mới
- Cú pháp: `mkdir [OPTIONS] DIRECTORY...`

### 5. rmdir (remove directory)
- Xóa directories trống
- Cú pháp: `rmdir [OPTIONS] DIRECTORY...`

### 6. chgrp (change group)
- Thay đổi group sở hữu của file hoặc directory
- Cú pháp: `chgrp [OPTIONS] GROUP FILE...`

### 7. chown (change owner)
- Thay đổi user và/hoặc group sở hữu của file hoặc directory
- Cú pháp: `chown [OPTIONS] [OWNER][:[GROUP]] FILE...`

## Phần 2: Ví dụ minh họa

### 1. cp (copy)

```bash
# Sao chép một file
cp file1.txt file2.txt

# Sao chép một directory và nội dung của nó
cp -r dir1 dir2
```

### 2. rm (remove)

```bash
# Xóa một file
rm file.txt

# Xóa một directory và nội dung của nó
rm -r directory
```

### 3. mv (move)

```bash
# Di chuyển một file
mv file.txt /path/to/destination/

# Đổi tên một file
mv oldname.txt newname.txt
```

### 4. mkdir (make directory)

```bash
# Tạo một directory
mkdir new_directory

# Tạo nhiều directories cùng lúc
mkdir dir1 dir2 dir3
```

### 5. rmdir (remove directory)

```bash
# Xóa một directory trống
rmdir empty_directory

# Xóa nhiều directories trống
rmdir dir1 dir2 dir3
```

### 6. chgrp (change group)

```bash
# Thay đổi group của một file
chgrp new_group file.txt

# Thay đổi group của một directory và nội dung bên trong
chgrp -R new_group directory
```

### 7. chown (change owner)

```bash
# Thay đổi owner của một file
chown new_owner file.txt

# Thay đổi cả owner và group của một file
chown new_owner:new_group file.txt
```

## Phần 3: Bài tập thực hành

### Bài tập 1: Quản lý cấu trúc thư mục dự án

Giả sử bạn đang làm việc trên một dự án web và cần tổ chức lại cấu trúc thư mục.

```bash
# 1. Tạo cấu trúc thư mục dự án
mkdir -p project/{css,js,images}

# 2. Tạo một số file mẫu
touch project/index.html
touch project/css/style.css
touch project/js/script.js

# 3. Sao chép file index.html thành about.html
cp project/index.html project/about.html

# 4. Di chuyển file style.css vào thư mục gốc của dự án
mv project/css/style.css project/

# 5. Xóa thư mục css (giờ đã trống)
rmdir project/css

# 6. Đổi tên thư mục js thành javascript
mv project/js project/javascript
```

### Bài tập 2: Quản lý quyền và sở hữu file

Giả sử bạn cần cấu hình quyền truy cập cho một ứng dụng web.

```bash
# 1. Tạo một thư mục cho ứng dụng web
sudo mkdir /var/www/myapp

# 2. Tạo một file cấu hình
sudo touch /var/www/myapp/config.ini

# 3. Thay đổi owner của thư mục và nội dung bên trong thành user 'www-data'
sudo chown -R www-data:www-data /var/www/myapp

# 4. Đặt quyền cho thư mục (755) và file (644)
sudo chmod 755 /var/www/myapp
sudo chmod 644 /var/www/myapp/config.ini

# 5. Tạo một group mới cho developers
sudo groupadd webdev

# 6. Thêm group 'webdev' làm group sở hữu của thư mục ứng dụng
sudo chgrp -R webdev /var/www/myapp

# 7. Đặt SGID bit cho thư mục để đảm bảo các file mới tạo kế thừa group
sudo chmod g+s /var/www/myapp
```

## Phần 4: Ứng dụng thực tế

### Tình huống 1: Backup và Restore

Giả sử bạn cần tạo một backup của thư mục home và restore nó.

```bash
# Tạo backup
sudo tar -czvf home_backup.tar.gz /home

# Di chuyển file backup đến thư mục an toàn
sudo mv home_backup.tar.gz /mnt/backup_drive/

# Nếu cần restore
sudo tar -xzvf /mnt/backup_drive/home_backup.tar.gz -C /
```

### Tình huống 2: Quản lý Log Files

Giả sử bạn cần quản lý các file log, bao gồm việc nén và xóa các file cũ.

```bash
# Nén các file log cũ hơn 7 ngày
find /var/log -name "*.log" -mtime +7 -exec gzip {} \;

# Di chuyển các file log đã nén vào thư mục archive
mkdir -p /var/log/archive
find /var/log -name "*.gz" -exec mv {} /var/log/archive/ \;

# Xóa các file log trong thư mục archive cũ hơn 30 ngày
find /var/log/archive -name "*.gz" -mtime +30 -delete
```

### Tình huống 3: Cấu hình phân quyền cho một ứng dụng multi-user

Giả sử bạn đang cấu hình một ứng dụng nơi nhiều users cần truy cập và chỉnh sửa files.

```bash
# Tạo thư mục cho ứng dụng
sudo mkdir /opt/multiuser-app

# Tạo group cho users của ứng dụng
sudo groupadd app-users

# Thay đổi group ownership của thư mục
sudo chgrp app-users /opt/multiuser-app

# Đặt permissions cho thư mục (770 - rwxrwx---)
sudo chmod 770 /opt/multiuser-app

# Đặt SGID bit
sudo chmod g+s /opt/multiuser-app

# Thêm users vào group
sudo usermod -aG app-users user1
sudo usermod -aG app-users user2

# Tạo một shared configuration file
sudo touch /opt/multiuser-app/config.json
sudo chmod 660 /opt/multiuser-app/config.json
```

## Kết luận

Trong bài lab này, chúng ta đã học và thực hành với các lệnh bảo trì file cơ bản trong Linux như cp, rm, mv, mkdir, rmdir, chgrp, và chown. Chúng ta đã thấy cách áp dụng các lệnh này trong các tình huống thực tế như quản lý dự án, backup và restore, quản lý log files, và cấu hình phân quyền cho ứng dụng multi-user.

Các lệnh này là nền tảng cho việc quản lý file và thư mục trong Linux. Việc thành thạo các lệnh này sẽ giúp bạn làm việc hiệu quả hơn trong môi trường Linux, đặc biệt là trong các tác vụ quản trị hệ thống và phát triển phần mềm.

