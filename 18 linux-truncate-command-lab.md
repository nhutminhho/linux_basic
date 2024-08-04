# Bài Lab: Lệnh 'truncate' trong Linux

## Phần 1: Lý thuyết

Lệnh `truncate` trong Linux được sử dụng để thay đổi kích thước của một file. Nó có thể làm giảm hoặc tăng kích thước của file đến một kích thước cụ thể.

Cú pháp cơ bản:
```
truncate [OPTION]... FILE...
```

Option phổ biến nhất:
- `-s, --size=SIZE`: Đặt hoặc điều chỉnh kích thước file

SIZE có thể được chỉ định với các đơn vị sau:
- K: Kilobytes
- M: Megabytes
- G: Gigabytes
- T: Terabytes

Lưu ý quan trọng:
- Khi giảm kích thước file, dữ liệu ở cuối file sẽ bị mất.
- Khi tăng kích thước file, khoảng trống mới được thêm vào sẽ chứa dữ liệu null.

## Phần 2: Ví dụ minh họa

Trước tiên, hãy tạo một file mẫu để thực hành:

```bash
# Tạo một file 1MB với dữ liệu ngẫu nhiên
dd if=/dev/urandom of=sample.txt bs=1M count=1
```

### Ví dụ 1: Giảm kích thước file

```bash
# Kiểm tra kích thước ban đầu
ls -lh sample.txt

# Giảm kích thước xuống 500KB
truncate -s 500K sample.txt

# Kiểm tra kích thước mới
ls -lh sample.txt
```

### Ví dụ 2: Tăng kích thước file

```bash
# Tăng kích thước lên 2MB
truncate -s 2M sample.txt

# Kiểm tra kích thước mới
ls -lh sample.txt
```

### Ví dụ 3: Sử dụng giá trị tương đối

```bash
# Giảm kích thước đi 250KB
truncate -s -250K sample.txt

# Kiểm tra kích thước mới
ls -lh sample.txt
```

## Phần 3: Bài tập thực hành

### Bài tập 1: Quản lý file log

Giả sử bạn có một file log đang phát triển quá lớn và bạn muốn giữ nó ở một kích thước cố định.

1. Tạo một file log mẫu:
```bash
for i in {1..1000}; do
    echo "Log entry $i: $(date)" >> app.log
done
```

2. Kiểm tra kích thước hiện tại của file log.
3. Sử dụng `truncate` để giữ file log ở kích thước 10KB.
4. Kiểm tra nội dung của file log sau khi truncate.

Bài giải:

```bash
# Kiểm tra kích thước ban đầu
ls -lh app.log

# Truncate file xuống 10KB
truncate -s 10K app.log

# Kiểm tra kích thước mới
ls -lh app.log

# Xem nội dung file sau khi truncate
tail app.log
```

### Bài tập 2: Tạo file có kích thước cố định

Đôi khi trong quá trình testing, bạn cần tạo các file có kích thước cụ thể.

1. Sử dụng `truncate` để tạo một file trống có kích thước 100MB.
2. Kiểm tra kích thước của file.
3. Thêm một số dữ liệu vào đầu file.
4. Kiểm tra xem kích thước file có thay đổi không.

Bài giải:

```bash
# Tạo file 100MB
truncate -s 100M large_file.bin

# Kiểm tra kích thước
ls -lh large_file.bin

# Thêm dữ liệu vào đầu file
echo "Some data at the beginning" > temp.txt
cat temp.txt large_file.bin > temp.bin && mv temp.bin large_file.bin

# Kiểm tra kích thước sau khi thêm dữ liệu
ls -lh large_file.bin
```

## Phần 4: Ứng dụng thực tế

### Tình huống 1: Quản lý dung lượng log files

Trong quản trị hệ thống, việc kiểm soát kích thước của log files là rất quan trọng để tránh làm đầy ổ đĩa.

```bash
#!/bin/bash

LOG_FILE="/var/log/myapp.log"
MAX_SIZE=$((10 * 1024 * 1024))  # 10MB

# Kiểm tra kích thước hiện tại
current_size=$(stat -c %s "$LOG_FILE")

if [ $current_size -gt $MAX_SIZE ]; then
    echo "Log file exceeds maximum size. Truncating..."
    truncate -s $MAX_SIZE "$LOG_FILE"
    echo "Log truncated at $(date)" >> "$LOG_FILE"
fi
```

### Tình huống 2: Chuẩn bị file để testing

Khi testing hiệu suất của ứng dụng, đôi khi bạn cần các file có kích thước cụ thể.

```bash
#!/bin/bash

# Tạo các file test với kích thước khác nhau
truncate -s 1M test_1mb.bin
truncate -s 10M test_10mb.bin
truncate -s 100M test_100mb.bin
truncate -s 1G test_1gb.bin

# Kiểm tra kích thước các file
ls -lh test_*.bin
```

### Tình huống 3: Dọn dẹp ổ đĩa nhanh chóng

Khi ổ đĩa gần đầy và bạn cần giải phóng không gian nhanh chóng, `truncate` có thể hữu ích.

```bash
#!/bin/bash

# Danh sách các file log cần giảm kích thước
log_files=("/var/log/syslog" "/var/log/auth.log" "/var/log/kern.log")

for file in "${log_files[@]}"; do
    if [ -f "$file" ]; then
        original_size=$(stat -c %s "$file")
        truncate -s 1M "$file"
        new_size=$(stat -c %s "$file")
        saved=$((original_size - new_size))
        echo "Reduced $file from $original_size to $new_size bytes (saved $saved bytes)"
    fi
done
```

## Kết luận

Trong bài lab này, chúng ta đã khám phá cách sử dụng lệnh `truncate` trong Linux. Lệnh này là một công cụ mạnh mẽ để quản lý kích thước file, đặc biệt hữu ích trong các tình huống như:

1. Quản lý kích thước của log files để tránh tràn ổ đĩa.
2. Chuẩn bị các file có kích thước cụ thể cho mục đích testing.
3. Nhanh chóng giải phóng không gian ổ đĩa trong trường hợp khẩn cấp.

Tuy nhiên, cần lưu ý rằng việc sử dụng `truncate` có thể dẫn đến mất dữ liệu nếu không cẩn thận, đặc biệt khi giảm kích thước file. Vì vậy, luôn đảm bảo bạn hiểu rõ hậu quả và có backup khi cần thiết.

Khi kết hợp `truncate` với các công cụ khác như shell scripting, bạn có thể tạo ra các giải pháp tự động hóa mạnh mẽ cho việc quản lý không gian đĩa và bảo trì hệ thống.

