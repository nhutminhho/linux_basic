# Bài Lab: Lệnh 'grep' và 'egrep' trong Linux

## Phần 1: Lý thuyết

`grep` (global regular expression print) là một công cụ mạnh mẽ trong Linux để tìm kiếm và lọc văn bản dựa trên các mẫu (pattern). Nó xử lý văn bản theo từng dòng và in ra các dòng khớp với mẫu được chỉ định.

`egrep` là phiên bản mở rộng của `grep`, cho phép sử dụng các biểu thức chính quy mở rộng.

Cú pháp cơ bản:
```
grep [OPTIONS] PATTERN [FILE...]
egrep [OPTIONS] PATTERN [FILE...]
```

Các option phổ biến:
- `-c`: Đếm số dòng khớp
- `-i`: Ignore case (không phân biệt chữ hoa/thường)
- `-n`: Hiển thị số dòng
- `-v`: Hiển thị các dòng không khớp
- `-r`: Tìm kiếm đệ quy trong thư mục

## Phần 2: Ví dụ minh họa

Trước tiên, hãy tạo một file mẫu để thực hành:

```bash
cat > sample.txt << EOL
The quick brown fox jumps over the lazy dog.
A quick brown dog jumps over the lazy fox.
The lazy cat sleeps all day.
Quick, catch the brown rabbit!
THE FOX IS QUICK AND BROWN.
EOL
```

### 1. Tìm kiếm cơ bản

```bash
# Tìm các dòng chứa từ "quick"
grep "quick" sample.txt

# Tìm kiếm không phân biệt chữ hoa/thường
grep -i "quick" sample.txt
```

### 2. Đếm số dòng khớp

```bash
# Đếm số dòng chứa từ "brown"
grep -c "brown" sample.txt
```

### 3. Hiển thị số dòng

```bash
# Hiển thị số dòng cho kết quả tìm kiếm
grep -n "lazy" sample.txt
```

### 4. Hiển thị dòng không khớp

```bash
# Hiển thị các dòng không chứa từ "the"
grep -v "the" sample.txt
```

### 5. Sử dụng egrep để tìm nhiều mẫu

```bash
# Tìm các dòng chứa "fox" hoặc "dog"
egrep "fox|dog" sample.txt

# Tương đương với grep -E "fox|dog" sample.txt
```

### 6. Kết hợp với các lệnh khác

```bash
# Tìm kiếm và chỉ hiển thị từ đầu tiên của mỗi dòng
grep "quick" sample.txt | awk '{print $1}'
```

## Phần 3: Bài tập thực hành

### Bài tập 1: Tìm kiếm trong file log

Giả sử bạn có một file log (/var/log/syslog) và muốn tìm kiếm các thông tin liên quan đến SSH.

1. Tìm tất cả các dòng liên quan đến SSH.
2. Đếm số lần xuất hiện của "Failed password" trong log.
3. Hiển thị 5 dòng log SSH gần đây nhất với số dòng.

Bài giải:

```bash
# 1. Tìm các dòng liên quan đến SSH
sudo grep "sshd" /var/log/syslog

# 2. Đếm số lần xuất hiện của "Failed password"
sudo grep -c "Failed password" /var/log/auth.log

# 3. Hiển thị 5 dòng log SSH gần đây nhất với số dòng
sudo grep "sshd" /var/log/syslog | tail -n 5 | grep -n ""
```

### Bài tập 2: Tìm kiếm trong nhiều file

Giả sử bạn có một thư mục chứa nhiều file Python và muốn tìm kiếm một hàm cụ thể.

1. Tìm tất cả các file chứa hàm `def process_data()`.
2. Đếm số lần sử dụng hàm `print()` trong tất cả các file Python.
3. Tìm tất cả các dòng chứa `TODO` hoặc `FIXME` trong các file Python.

Bài giải:

```bash
# 1. Tìm files chứa hàm process_data()
grep -r "def process_data()" /path/to/python/files

# 2. Đếm số lần sử dụng hàm print()
grep -r "print(" /path/to/python/files | wc -l

# 3. Tìm các dòng chứa TODO hoặc FIXME
egrep -r "TODO|FIXME" /path/to/python/files
```

## Phần 4: Ứng dụng thực tế

### Tình huống 1: Phân tích log Apache

Giả sử bạn là quản trị viên web server và cần phân tích file access log của Apache.

```bash
# Tìm tất cả các request POST
grep "POST" /var/log/apache2/access.log

# Đếm số lượng request từ mỗi IP
grep -oE "\b([0-9]{1,3}\.){3}[0-9]{1,3}\b" /var/log/apache2/access.log | sort | uniq -c | sort -nr

# Tìm tất cả các request có status code 404 (Not Found)
grep " 404 " /var/log/apache2/access.log
```

### Tình huống 2: Kiểm tra cấu hình hệ thống

Bạn cần kiểm tra và tìm kiếm thông tin trong các file cấu hình hệ thống.

```bash
# Tìm tất cả các port đang lắng nghe
sudo grep "Listen" /etc/apache2/ports.conf

# Kiểm tra cấu hình SSH
grep -v "^#" /etc/ssh/sshd_config | grep -v "^$"

# Tìm tất cả các user có shell là /bin/bash
grep "/bin/bash" /etc/passwd
```

### Tình huống 3: Tìm kiếm trong mã nguồn

Bạn đang làm việc trên một dự án lớn và cần tìm kiếm một số thông tin cụ thể trong mã nguồn.

```bash
# Tìm tất cả các hàm deprecated
grep -r "@deprecated" /path/to/project

# Tìm tất cả các TODO trong mã nguồn
grep -r "TODO" /path/to/project

# Tìm tất cả các file chứa một chuỗi connection string cụ thể
grep -r "jdbc:mysql://localhost:3306/mydb" /path/to/project
```

## Kết luận

Trong bài lab này, chúng ta đã khám phá sức mạnh và tính linh hoạt của lệnh `grep` và `egrep` trong Linux. Từ việc tìm kiếm đơn giản trong một file đến phân tích log phức tạp và tìm kiếm trong mã nguồn, `grep` và `egrep` đã chứng tỏ là những công cụ không thể thiếu cho việc xử lý và tìm kiếm văn bản trong môi trường dòng lệnh Linux.

Chúng ta đã thấy cách `grep` và `egrep` có thể được sử dụng để:
- Tìm kiếm nhanh chóng các mẫu văn bản trong file hoặc output của các lệnh khác
- Lọc và trích xuất thông tin cụ thể từ các file log lớn
- Kiểm tra và phân tích cấu hình hệ thống
- Tìm kiếm trong mã nguồn của các dự án lớn

Khi kết hợp `grep` và `egrep` với các lệnh Linux khác như `awk`, `sort`, hay `uniq`, bạn có thể tạo ra các pipeline mạnh mẽ để xử lý và phân tích dữ liệu văn bản một cách hiệu quả.

Việc thành thạo `grep` và `egrep` sẽ giúp bạn trở thành một quản trị viên hệ thống hoặc nhà phát triển Linux giỏi hơn, cho phép bạn nhanh chóng tìm kiếm và xử lý thông tin quan trọng trong hệ thống của mình.

