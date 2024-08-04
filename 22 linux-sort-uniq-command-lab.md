# Bài Lab: Lệnh 'sort' và 'uniq' trong Linux

## Phần 1: Lý thuyết

### sort
- Lệnh `sort` sắp xếp các dòng của văn bản theo thứ tự alphabetical.
- Cú pháp cơ bản: `sort [OPTIONS] [FILE]...`
- Các option phổ biến:
  - `-r`: Sắp xếp ngược
  - `-n`: Sắp xếp số
  - `-k`: Sắp xếp theo trường cụ thể

### uniq
- Lệnh `uniq` loại bỏ hoặc báo cáo các dòng lặp lại liền kề.
- Cú pháp cơ bản: `uniq [OPTIONS] [INPUT [OUTPUT]]`
- Các option phổ biến:
  - `-c`: Đếm số lần xuất hiện
  - `-d`: Chỉ hiển thị các dòng lặp lại
  - `-u`: Chỉ hiển thị các dòng không lặp lại

## Phần 2: Ví dụ minh họa

Trước tiên, hãy tạo một file mẫu để thực hành:

```bash
cat > sample.txt << EOL
banana
apple
cherry
apple
date
banana
cherry
EOL
```

### Ví dụ với sort

```bash
# Sắp xếp theo thứ tự alphabetical
sort sample.txt

# Sắp xếp ngược
sort -r sample.txt
```

### Ví dụ với uniq

```bash
# Loại bỏ các dòng trùng lặp liền kề
sort sample.txt | uniq

# Đếm số lần xuất hiện của mỗi dòng
sort sample.txt | uniq -c

# Chỉ hiển thị các dòng lặp lại
sort sample.txt | uniq -d
```

## Phần 3: Bài tập thực hành

### Bài tập 1: Phân tích log file

Giả sử bạn có một file log đơn giản (access.log) với định dạng: IP Address, Timestamp, URL

```bash
cat > access.log << EOL
192.168.1.100 2023-05-01 10:00:00 /home
192.168.1.101 2023-05-01 10:01:00 /about
192.168.1.100 2023-05-01 10:02:00 /products
192.168.1.102 2023-05-01 10:03:00 /home
192.168.1.101 2023-05-01 10:04:00 /products
192.168.1.100 2023-05-01 10:05:00 /home
EOL
```

1. Liệt kê các IP address duy nhất, sắp xếp theo thứ tự alphabetical.
2. Đếm số lần truy cập của mỗi IP address.
3. Liệt kê các URL được truy cập, loại bỏ trùng lặp và sắp xếp.

Bài giải:

```bash
# 1. Liệt kê các IP address duy nhất
cut -d ' ' -f1 access.log | sort | uniq

# 2. Đếm số lần truy cập của mỗi IP
cut -d ' ' -f1 access.log | sort | uniq -c | sort -nr

# 3. Liệt kê các URL duy nhất
cut -d ' ' -f4 access.log | sort | uniq
```

### Bài tập 2: Xử lý dữ liệu CSV

Tạo một file CSV đơn giản chứa thông tin về nhân viên:

```bash
cat > employees.csv << EOL
Name,Department,Salary
John Doe,IT,5000
Jane Smith,HR,4500
Bob Johnson,IT,5500
Alice Brown,Marketing,4800
John Doe,IT,5000
Charlie Davis,HR,4200
EOL
```

1. Sắp xếp nhân viên theo tên.
2. Liệt kê các phòng ban duy nhất.
3. Tìm và hiển thị các bản ghi trùng lặp.

Bài giải:

```bash
# 1. Sắp xếp nhân viên theo tên
sort -k1 employees.csv

# 2. Liệt kê các phòng ban duy nhất
cut -d ',' -f2 employees.csv | sort | uniq

# 3. Tìm và hiển thị các bản ghi trùng lặp
sort employees.csv | uniq -d
```

## Phần 4: Ứng dụng thực tế

### Tình huống 1: Phân tích log Apache

Giả sử bạn có một file log Apache (/var/log/apache2/access.log) và muốn phân tích nó:

```bash
# Tìm 10 IP address truy cập nhiều nhất
awk '{print $1}' /var/log/apache2/access.log | sort | uniq -c | sort -nr | head -10

# Liệt kê các URL được truy cập, loại bỏ trùng lặp và sắp xếp
awk '{print $7}' /var/log/apache2/access.log | sort | uniq

# Tìm các request có status code 404 (Not Found)
awk '$9 == "404" {print $7}' /var/log/apache2/access.log | sort | uniq -c | sort -nr
```

### Tình huống 2: Quản lý package trong Linux

Giả sử bạn muốn phân tích các package đã cài đặt trên hệ thống:

```bash
# Liệt kê tất cả các package đã cài đặt, sắp xếp theo tên
dpkg --get-selections | sort

# Tìm các package trùng lặp (nếu có)
dpkg --get-selections | sort | uniq -d

# Đếm số lượng package cho mỗi trạng thái (installed, deinstall, etc.)
dpkg --get-selections | awk '{print $2}' | sort | uniq -c | sort -nr
```

### Tình huống 3: Phân tích sử dụng ổ đĩa

Bạn muốn phân tích việc sử dụng ổ đĩa trên hệ thống:

```bash
# Liệt kê 10 thư mục lớn nhất trong /home
du -sh /home/* | sort -rh | head -10

# Tìm các file trùng lặp trong một thư mục (dựa trên kích thước và tên)
find /path/to/directory -type f -printf "%s %p\n" | sort -rn | uniq -Dw9

# Đếm số lượng file theo phần mở rộng
find /path/to/directory -type f | sed 's/.*\.//' | sort | uniq -c | sort -nr
```

## Kết luận

Trong bài lab này, chúng ta đã khám phá cách sử dụng lệnh `sort` và `uniq` trong Linux. Những công cụ này cực kỳ hữu ích cho việc sắp xếp dữ liệu, loại bỏ các mục trùng lặp, và phân tích thông tin từ các file log, dữ liệu CSV, và nhiều nguồn khác.

Chúng ta đã thấy cách `sort` và `uniq` có thể được sử dụng để:
- Sắp xếp và tổ chức dữ liệu theo nhiều tiêu chí khác nhau
- Loại bỏ hoặc đếm các mục trùng lặp
- Phân tích log files để tìm các mẫu và xu hướng
- Quản lý và kiểm tra các package hệ thống
- Phân tích việc sử dụng ổ đĩa và tìm các file trùng lặp

Khi kết hợp `sort` và `uniq` với các lệnh Linux khác như `awk`, `cut`, hay `find`, bạn có thể tạo ra các pipeline mạnh mẽ để xử lý và phân tích dữ liệu một cách hiệu quả.

Việc thành thạo `sort` và `uniq` sẽ giúp bạn trở thành một quản trị viên hệ thống hoặc nhà phát triển Linux giỏi hơn, cho phép bạn nhanh chóng tổ chức và phân tích dữ liệu trong hệ thống của mình.

