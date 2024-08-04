# Bài Lab: So Sánh File trong Linux với diff và cmp

## Phần 1: Lý thuyết

### diff (Line by line)
- `diff` so sánh files theo từng dòng.
- Cú pháp: `diff [OPTIONS] FILE1 FILE2`
- Các option phổ biến:
  - `-u`: Output định dạng unified
  - `-i`: Bỏ qua sự khác biệt chữ hoa/thường
  - `-w`: Bỏ qua khoảng trắng
  - `-r`: So sánh đệ quy các thư mục

### cmp (Byte by byte)
- `cmp` so sánh files theo từng byte.
- Cú pháp: `cmp [OPTIONS] FILE1 FILE2`
- Các option phổ biến:
  - `-b`: In giá trị khác biệt dưới dạng octal
  - `-l`: Hiển thị tất cả các byte khác nhau
  - `-s`: Chế độ im lặng, chỉ trả về trạng thái exit

## Phần 2: Ví dụ minh họa

Trước tiên, hãy tạo hai file mẫu để so sánh:

```bash
# File 1
cat > file1.txt << EOL
This is line 1.
This is line 2.
This is line 3.
This is line 4.
EOL

# File 2
cat > file2.txt << EOL
This is line 1.
This is a different line 2.
This is line 3.
This is an additional line.
This is line 4.
EOL
```

### Sử dụng diff

```bash
# So sánh cơ bản
diff file1.txt file2.txt

# Sử dụng định dạng unified
diff -u file1.txt file2.txt

# Bỏ qua khoảng trắng
diff -w file1.txt file2.txt
```

### Sử dụng cmp

```bash
# So sánh cơ bản
cmp file1.txt file2.txt

# Hiển thị tất cả các byte khác nhau
cmp -l file1.txt file2.txt

# Chế độ im lặng
cmp -s file1.txt file2.txt && echo "Files are identical" || echo "Files are different"
```

## Phần 3: Bài tập thực hành

### Bài tập 1: So sánh file cấu hình

Giả sử bạn đang quản lý một máy chủ web và muốn so sánh file cấu hình của Apache trên hai server khác nhau.

1. Tạo hai file cấu hình mẫu:

```bash
# File cấu hình server 1
cat > apache_server1.conf << EOL
<VirtualHost *:80>
    ServerName www.example.com
    DocumentRoot /var/www/html
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
EOL

# File cấu hình server 2
cat > apache_server2.conf << EOL
<VirtualHost *:80>
    ServerName www.example.com
    DocumentRoot /var/www/html
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
    # Additional SSL configuration
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/ssl-cert-snakeoil.pem
    SSLCertificateKeyFile /etc/ssl/private/ssl-cert-snakeoil.key
</VirtualHost>
EOL
```

2. Sử dụng `diff` để so sánh hai file cấu hình và hiển thị sự khác biệt.
3. Sử dụng `cmp` để kiểm tra xem hai file có giống nhau không.

Bài giải:

```bash
# Sử dụng diff
diff -u apache_server1.conf apache_server2.conf

# Sử dụng cmp
cmp -s apache_server1.conf apache_server2.conf && echo "Configurations are identical" || echo "Configurations are different"
```

### Bài tập 2: So sánh output của hai lệnh

Giả sử bạn muốn so sánh danh sách các process đang chạy tại hai thời điểm khác nhau.

1. Lấy danh sách process tại thời điểm 1:
```bash
ps aux > processes_t1.txt
```

2. Đợi một lúc (có thể chạy một số lệnh để tạo sự khác biệt), sau đó lấy danh sách process tại thời điểm 2:
```bash
ps aux > processes_t2.txt
```

3. Sử dụng `diff` để so sánh hai danh sách và tìm ra sự khác biệt.

Bài giải:

```bash
# So sánh hai danh sách process
diff -u processes_t1.txt processes_t2.txt | grep "^[+-]" | grep -v "^[+-]USER"
```

## Phần 4: Ứng dụng thực tế

### Tình huống 1: Kiểm tra thay đổi trong file log

Giả sử bạn muốn theo dõi những thay đổi trong file log của hệ thống giữa hai lần kiểm tra.

```bash
# Lấy mẫu log ban đầu
sudo tail -n 100 /var/log/syslog > syslog_sample1.txt

# Đợi một lúc, sau đó lấy mẫu log mới
sudo tail -n 100 /var/log/syslog > syslog_sample2.txt

# So sánh hai mẫu log
diff -u syslog_sample1.txt syslog_sample2.txt
```

### Tình huống 2: So sánh backup và file hiện tại

Giả sử bạn muốn kiểm tra xem một file cấu hình quan trọng có bị thay đổi so với bản backup không.

```bash
# Tạo một file cấu hình mẫu và bản backup
echo "important_setting=value1" > config.ini
cp config.ini config.ini.bak

# Giả lập thay đổi trong file cấu hình
echo "important_setting=value2" > config.ini

# So sánh file hiện tại với bản backup
diff config.ini config.ini.bak

# Kiểm tra nhanh xem có sự khác biệt không
cmp -s config.ini config.ini.bak && echo "No changes" || echo "File has been modified"
```

### Tình huống 3: So sánh nội dung của hai thư mục

Giả sử bạn cần so sánh nội dung của hai thư mục dự án để tìm ra sự khác biệt.

```bash
# Tạo hai thư mục dự án mẫu
mkdir project_v1 project_v2

# Thêm một số file vào mỗi thư mục
echo "Version 1 content" > project_v1/file1.txt
echo "Version 1 content" > project_v1/file2.txt
echo "Version 2 content" > project_v2/file1.txt
echo "Version 2 content" > project_v2/file2.txt
echo "New file in v2" > project_v2/file3.txt

# So sánh hai thư mục
diff -r project_v1 project_v2
```

## Kết luận

Trong bài lab này, chúng ta đã khám phá cách sử dụng lệnh `diff` và `cmp` trong Linux để so sánh files và directories. Chúng ta đã thấy rằng:

- `diff` rất hữu ích cho việc so sánh nội dung của các file văn bản, hiển thị sự khác biệt theo từng dòng.
- `cmp` phù hợp cho việc so sánh nhanh xem hai file có giống nhau hoàn toàn hay không, hoặc để tìm ra vị trí byte đầu tiên khác nhau.

Các lệnh này đặc biệt hữu ích trong nhiều tình huống thực tế như:
- So sánh file cấu hình
- Kiểm tra thay đổi trong log files
- Verify backups
- So sánh các phiên bản khác nhau của code hoặc documents

Khi kết hợp `diff` và `cmp` với các lệnh Linux khác, bạn có thể tạo ra các scripts mạnh mẽ để tự động hóa các tác vụ so sánh và báo cáo sự khác biệt trong hệ thống của mình.

