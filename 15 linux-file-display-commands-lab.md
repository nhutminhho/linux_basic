# Bài Lab: Các Lệnh Hiển Thị File trong Linux

## Phần 1: Lý thuyết

### 1. cat (concatenate)
- Hiển thị toàn bộ nội dung của file
- Cú pháp: `cat [OPTIONS] [FILE]...`

### 2. more
- Hiển thị nội dung file theo từng trang, chỉ có thể cuộn xuống
- Cú pháp: `more [OPTIONS] [FILE]...`

### 3. less
- Hiển thị nội dung file theo từng trang, có thể cuộn lên xuống
- Cú pháp: `less [OPTIONS] [FILE]...`

### 4. head
- Hiển thị phần đầu của file (mặc định là 10 dòng đầu)
- Cú pháp: `head [OPTIONS] [FILE]...`

### 5. tail
- Hiển thị phần cuối của file (mặc định là 10 dòng cuối)
- Cú pháp: `tail [OPTIONS] [FILE]...`

## Phần 2: Ví dụ minh họa

Để minh họa các lệnh này, trước tiên chúng ta sẽ tạo một file mẫu:

```bash
# Tạo file mẫu
seq 1 100 > sample.txt
```

### 1. cat

```bash
# Hiển thị toàn bộ nội dung file
cat sample.txt

# Hiển thị nội dung với số dòng
cat -n sample.txt
```

### 2. more

```bash
# Hiển thị nội dung theo trang
more sample.txt
```
Nhấn Space để chuyển trang, q để thoát.

### 3. less

```bash
# Hiển thị nội dung với khả năng cuộn
less sample.txt
```
Sử dụng mũi tên lên/xuống để di chuyển, q để thoát.

### 4. head

```bash
# Hiển thị 10 dòng đầu tiên
head sample.txt

# Hiển thị 5 dòng đầu tiên
head -n 5 sample.txt
```

### 5. tail

```bash
# Hiển thị 10 dòng cuối cùng
tail sample.txt

# Hiển thị 5 dòng cuối cùng
tail -n 5 sample.txt

# Theo dõi file trong thời gian thực
tail -f sample.txt
```

## Phần 3: Bài tập thực hành

### Bài tập 1: Phân tích Log File

Giả sử bạn có một file log của web server (/var/log/apache2/access.log) và muốn phân tích nó.

```bash
# 1. Xem 10 request gần nhất
tail /var/log/apache2/access.log

# 2. Xem 20 request đầu tiên
head -n 20 /var/log/apache2/access.log

# 3. Tìm các request có status code 404 (Not Found)
cat /var/log/apache2/access.log | grep "404" | less

# 4. Theo dõi log file trong thời gian thực
tail -f /var/log/apache2/access.log

# 5. Đếm số lượng request
cat /var/log/apache2/access.log | wc -l
```

### Bài tập 2: Làm việc với File Cấu hình

Giả sử bạn cần kiểm tra và chỉnh sửa file cấu hình của SSH (/etc/ssh/sshd_config).

```bash
# 1. Xem toàn bộ nội dung file
cat /etc/ssh/sshd_config

# 2. Tìm cấu hình liên quan đến port
cat /etc/ssh/sshd_config | grep "Port"

# 3. Xem 20 dòng đầu tiên của file cấu hình
head -n 20 /etc/ssh/sshd_config

# 4. Tìm và hiển thị cấu hình về authentication
less /etc/ssh/sshd_config
# Trong less, gõ "/PasswordAuthentication" để tìm kiếm

# 5. Thêm một cấu hình mới vào cuối file
echo "AllowUsers yourusername" | sudo tee -a /etc/ssh/sshd_config

# 6. Kiểm tra cấu hình vừa thêm
tail -n 5 /etc/ssh/sshd_config
```

## Phần 4: Ứng dụng thực tế

### Tình huống 1: Debugging Application Logs

Giả sử bạn đang gỡ lỗi cho một ứng dụng và cần phân tích file log của nó.

```bash
# Xem các error gần đây nhất
tail -n 50 /path/to/app.log | grep "ERROR"

# Theo dõi log trong thời gian thực, chỉ hiển thị các dòng có chứa "Exception"
tail -f /path/to/app.log | grep "Exception"

# Đếm số lượng mỗi loại log level
cat /path/to/app.log | awk '{print $3}' | sort | uniq -c | sort -nr
```

### Tình huống 2: Kiểm tra Cấu hình Hệ thống

Khi cần kiểm tra nhanh các cấu hình hệ thống:

```bash
# Xem thông tin về bộ nhớ
head -n 20 /proc/meminfo

# Kiểm tra các process đang chạy
ps aux | less

# Xem 10 dòng cuối của file history
tail ~/.bash_history

# Kiểm tra cấu hình network
cat /etc/network/interfaces | grep -v "#" | grep -v "^$"
```

### Tình huống 3: Phân tích Dữ liệu CSV

Giả sử bạn có một file CSV lớn chứa dữ liệu bán hàng và cần phân tích nó:

```bash
# Xem cấu trúc file
head -n 5 sales_data.csv

# Đếm số lượng bản ghi
cat sales_data.csv | wc -l

# Tìm 10 giao dịch có giá trị cao nhất
sort -t',' -k5 -nr sales_data.csv | head -n 10

# Xem tất cả giao dịch của một khách hàng cụ thể
cat sales_data.csv | grep "Customer Name" | less
```

## Kết luận

Trong bài lab này, chúng ta đã học và thực hành với các lệnh hiển thị file cơ bản trong Linux như cat, more, less, head, và tail. Chúng ta đã thấy cách áp dụng các lệnh này trong các tình huống thực tế như phân tích log files, làm việc với file cấu hình, debugging ứng dụng, kiểm tra cấu hình hệ thống, và phân tích dữ liệu.

Các lệnh này là công cụ quan trọng giúp bạn nhanh chóng xem và phân tích nội dung của files trong Linux. Việc thành thạo các lệnh này sẽ giúp bạn làm việc hiệu quả hơn trong môi trường Linux, đặc biệt là trong các tác vụ quản trị hệ thống, phát triển phần mềm và phân tích dữ liệu.

