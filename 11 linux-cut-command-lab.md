# Bài Lab: Lệnh 'cut' trong Linux

## Phần 1: Lý thuyết

Lệnh `cut` trong Linux là một công cụ dòng lệnh cho phép bạn cắt các phần của dòng từ các file được chỉ định hoặc dữ liệu được pipe và in kết quả ra standard output. Nó có thể được sử dụng để cắt các phần của dòng theo delimiter, vị trí byte, và ký tự.

Cú pháp cơ bản:
```
cut OPTION... [FILE]...
```

Các option phổ biến:
- `-c`: Cắt dựa trên ký tự
- `-b`: Cắt dựa trên byte
- `-f`: Cắt dựa trên trường (field)
- `-d`: Chỉ định delimiter (mặc định là TAB)

## Phần 2: Ví dụ minh họa

Trước tiên, hãy tạo một file mẫu để thực hành:

```bash
echo "1:John Doe:30:New York:Developer" > sample.txt
echo "2:Jane Smith:28:London:Designer" >> sample.txt
echo "3:Bob Johnson:35:Paris:Manager" >> sample.txt
```

### 1. Cắt dựa trên ký tự

```bash
# Hiển thị ký tự đầu tiên của mỗi dòng
cut -c1 sample.txt

# Hiển thị ký tự 1, 2, và 4 của mỗi dòng
cut -c1,2,4 sample.txt

# Hiển thị 3 ký tự đầu tiên của mỗi dòng
cut -c1-3 sample.txt

# Hiển thị ký tự 1-3 và 6-8 của mỗi dòng
cut -c1-3,6-8 sample.txt
```

### 2. Cắt dựa trên byte

```bash
# Hiển thị 3 byte đầu tiên của mỗi dòng
cut -b1-3 sample.txt
```

### 3. Cắt dựa trên trường (field)

```bash
# Hiển thị trường thứ 2 (tên) sử dụng ':' làm delimiter
cut -d: -f2 sample.txt

# Hiển thị trường 2 và 3 (tên và tuổi)
cut -d: -f2-3 sample.txt
```

### 4. Kết hợp với các lệnh khác

```bash
# Hiển thị quyền của user cho các file/directory
ls -l | cut -c2-4
```

## Phần 3: Bài tập thực hành

### Bài tập 1: Phân tích file /etc/passwd

1. Hiển thị tên người dùng (trường đầu tiên) từ file /etc/passwd.
2. Hiển thị tên người dùng và thư mục home (trường cuối cùng) từ file /etc/passwd.
3. Hiển thị 10 ký tự đầu tiên của mỗi dòng trong file /etc/passwd.

Bài giải:

```bash
# 1. Hiển thị tên người dùng
cut -d: -f1 /etc/passwd

# 2. Hiển thị tên người dùng và thư mục home
cut -d: -f1,6 /etc/passwd

# 3. Hiển thị 10 ký tự đầu tiên của mỗi dòng
cut -c1-10 /etc/passwd
```

### Bài tập 2: Xử lý dữ liệu CSV

Tạo một file CSV mẫu:

```bash
echo "ID,Name,Age,City,Salary" > employees.csv
echo "1,John Doe,30,New York,50000" >> employees.csv
echo "2,Jane Smith,28,London,45000" >> employees.csv
echo "3,Bob Johnson,35,Paris,55000" >> employees.csv
```

1. Hiển thị chỉ tên và tuổi của nhân viên.
2. Hiển thị ID và lương của nhân viên.
3. Hiển thị 5 ký tự đầu tiên của mỗi dòng.

Bài giải:

```bash
# 1. Hiển thị tên và tuổi
cut -d, -f2,3 employees.csv

# 2. Hiển thị ID và lương
cut -d, -f1,5 employees.csv

# 3. Hiển thị 5 ký tự đầu tiên
cut -c1-5 employees.csv
```

## Phần 4: Ứng dụng thực tế

### Tình huống 1: Phân tích Log Apache

Giả sử bạn có một file log Apache (/var/log/apache2/access.log) và muốn trích xuất thông tin cụ thể:

```bash
# Trích xuất địa chỉ IP của clients
cut -d' ' -f1 /var/log/apache2/access.log | sort | uniq -c | sort -nr | head -10

# Trích xuất URL được truy cập
cut -d' ' -f7 /var/log/apache2/access.log | sort | uniq -c | sort -nr | head -10

# Trích xuất User Agent
cut -d'"' -f6 /var/log/apache2/access.log | sort | uniq -c | sort -nr | head -10
```

### Tình huống 2: Xử lý dữ liệu hệ thống

```bash
# Liệt kê tất cả các shell đang được sử dụng trên hệ thống
cut -d: -f7 /etc/passwd | sort | uniq

# Hiển thị tên và ID của tất cả các process đang chạy
ps aux | cut -c1-5,66-

# Hiển thị tên và dung lượng của các partition
df -h | cut -c1-5,23-
```

### Tình huống 3: Xử lý dữ liệu mạng

```bash
# Trích xuất địa chỉ IP và hostname từ file /etc/hosts
cut -f1,2 /etc/hosts

# Hiển thị tên interface và địa chỉ IP của các network interfaces
ifconfig | grep -E "^[a-z]" | cut -d: -f1
ifconfig | grep "inet " | cut -d: -f2 | cut -d' ' -f1
```

## Kết luận

Trong bài lab này, chúng ta đã học về lệnh `cut` trong Linux, một công cụ mạnh mẽ để xử lý và trích xuất dữ liệu từ file văn bản. Chúng ta đã thực hành với các ví dụ cơ bản và áp dụng `cut` vào các tình huống thực tế như phân tích log, xử lý dữ liệu hệ thống và mạng.

Lệnh `cut` đặc biệt hữu ích khi làm việc với dữ liệu có cấu trúc như CSV, TSV, hoặc các file log. Nó cho phép bạn nhanh chóng trích xuất và xử lý thông tin cần thiết mà không cần sử dụng các công cụ phức tạp hơn.

Khi kết hợp `cut` với các lệnh khác như `sort`, `uniq`, hoặc `grep`, bạn có thể tạo ra các pipeline mạnh mẽ để xử lý và phân tích dữ liệu trong môi trường dòng lệnh Linux.

