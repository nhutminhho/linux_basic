# Bài Lab: Lệnh 'wc' trong Linux

## Phần 1: Lý thuyết

Lệnh `wc` (word count) trong Linux được sử dụng để đếm số dòng, số từ, số byte và số ký tự trong một file hoặc input stream.

Cú pháp cơ bản:
```
wc [OPTIONS] [FILE...]
```

Các option phổ biến:
- `-l`: Đếm số dòng
- `-w`: Đếm số từ
- `-c`: Đếm số byte
- `-m`: Đếm số ký tự
- `-L`: Hiển thị độ dài của dòng dài nhất

## Phần 2: Ví dụ minh họa

Trước tiên, hãy tạo một file mẫu để thực hành:

```bash
cat > sample.txt << EOL
The quick brown fox
jumps over the lazy dog.
This is a sample text file
for demonstrating the wc command.
EOL
```

### 1. Sử dụng wc cơ bản

```bash
# Đếm số dòng, từ và byte
wc sample.txt

# Chỉ đếm số dòng
wc -l sample.txt

# Chỉ đếm số từ
wc -w sample.txt

# Chỉ đếm số byte
wc -c sample.txt
```

### 2. Kết hợp với các lệnh khác

```bash
# Đếm số file trong thư mục hiện tại
ls -l | wc -l

# Đếm số dòng chứa từ "the" trong file
grep "the" sample.txt | wc -l
```

## Phần 3: Bài tập thực hành

### Bài tập 1: Phân tích file log

Giả sử bạn có một file log đơn giản (access.log):

```bash
cat > access.log << EOL
192.168.1.100 - - [01/May/2023:10:00:00 +0000] "GET /index.html HTTP/1.1" 200 1234
192.168.1.101 - - [01/May/2023:10:01:00 +0000] "GET /about.html HTTP/1.1" 200 2345
192.168.1.100 - - [01/May/2023:10:02:00 +0000] "GET /products.html HTTP/1.1" 404 345
192.168.1.102 - - [01/May/2023:10:03:00 +0000] "POST /login HTTP/1.1" 302 0
192.168.1.101 - - [01/May/2023:10:04:00 +0000] "GET /products.html HTTP/1.1" 200 3456
EOL
```

1. Đếm tổng số request trong log file.
2. Đếm số request GET.
3. Đếm số request có status code 200.

Bài giải:

```bash
# 1. Tổng số request
wc -l access.log

# 2. Số request GET
grep "GET" access.log | wc -l

# 3. Số request có status code 200
grep " 200 " access.log | wc -l
```

### Bài tập 2: Phân tích mã nguồn

Giả sử bạn có một thư mục chứa các file Python và muốn phân tích chúng:

```bash
mkdir python_project
cat > python_project/main.py << EOL
def main():
    print("Hello, World!")

if __name__ == "__main__":
    main()
EOL

cat > python_project/utils.py << EOL
def add(a, b):
    return a + b

def subtract(a, b):
    return a - b
EOL
```

1. Đếm tổng số dòng code trong tất cả các file Python.
2. Đếm số hàm (bắt đầu bằng "def") trong tất cả các file.
3. Tìm file có nhiều dòng nhất.

Bài giải:

```bash
# 1. Tổng số dòng code
wc -l python_project/*.py | grep "total"

# 2. Số hàm
grep -r "def " python_project | wc -l

# 3. File có nhiều dòng nhất
wc -l python_project/*.py | sort -nr | head -n 1
```

## Phần 4: Ứng dụng thực tế

### Tình huống 1: Phân tích nhật ký hệ thống

Giả sử bạn cần phân tích file /var/log/syslog:

```bash
# Đếm tổng số dòng log
sudo wc -l /var/log/syslog

# Đếm số lần xuất hiện của từ "error" (không phân biệt chữ hoa/thường)
sudo grep -i "error" /var/log/syslog | wc -l

# Tìm 5 dịch vụ ghi log nhiều nhất
sudo awk '{print $5}' /var/log/syslog | sort | uniq -c | sort -nr | head -5
```

### Tình huống 2: Kiểm tra sử dụng ổ đĩa

Bạn muốn kiểm tra việc sử dụng ổ đĩa và tìm các file lớn:

```bash
# Đếm số file trong thư mục /etc
sudo find /etc -type f | wc -l

# Tìm 10 file lớn nhất trong /home và đếm số dòng của chúng
sudo find /home -type f -exec du -sh {} + | sort -rh | head -10 | while read size file; do
    echo -n "$size $file - Lines: "
    wc -l "$file" | cut -d' ' -f1
done

# Đếm tổng số dòng trong tất cả các file .txt trong /home
sudo find /home -name "*.txt" -exec wc -l {} + | awk '{total += $1} END {print total}'
```

### Tình huống 3: Phân tích mã nguồn dự án

Giả sử bạn đang làm việc trên một dự án phát triển web và muốn phân tích mã nguồn:

```bash
# Đếm tổng số dòng code trong các file JavaScript
find . -name "*.js" -exec wc -l {} + | awk '{total += $1} END {print "Total lines in JS files:", total}'

# Đếm số hàm trong các file JavaScript
grep -r "function" --include="*.js" . | wc -l

# Tìm file HTML lớn nhất (theo số dòng)
find . -name "*.html" -exec wc -l {} + | sort -nr | head -1

# Đếm số TODO trong toàn bộ dự án
grep -r "TODO" . | wc -l
```

## Kết luận

Trong bài lab này, chúng ta đã khám phá cách sử dụng lệnh `wc` trong Linux để đếm số dòng, từ, và byte trong các file và streams. Chúng ta đã thấy cách `wc` có thể được sử dụng độc lập và kết hợp với các lệnh khác như `grep`, `find`, và `awk` để thực hiện các phân tích phức tạp hơn.

Lệnh `wc` là một công cụ đơn giản nhưng mạnh mẽ, đặc biệt hữu ích trong các tác vụ như:
- Phân tích log files
- Kiểm tra kích thước và cấu trúc của các file văn bản
- Đếm số lượng các phần tử trong output của các lệnh khác
- Phân tích mã nguồn và dự án phát triển

Khi kết hợp `wc` với các lệnh Linux khác, bạn có thể tạo ra các pipeline mạnh mẽ để xử lý và phân tích dữ liệu một cách hiệu quả. Việc thành thạo `wc` và các công cụ liên quan sẽ giúp bạn trở thành một quản trị viên hệ thống hoặc nhà phát triển Linux giỏi hơn, cho phép bạn nhanh chóng trích xuất thông tin hữu ích từ các nguồn dữ liệu đa dạng.

