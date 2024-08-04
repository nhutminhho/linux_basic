# Bài Lab: Lệnh 'tee' trong Linux

## Phần 1: Lý thuyết

### Giới thiệu về lệnh 'tee'
- Lệnh 'tee' trong Linux được sử dụng để lưu trữ và xem đồng thời output của bất kỳ lệnh nào.
- Tên của lệnh này được đặt theo hình dạng của ống nước chữ T trong hệ thống ống nước.
- 'tee' chia output của một chương trình để có thể vừa hiển thị trên màn hình vừa lưu vào file.
- Nó thực hiện cả hai nhiệm vụ cùng một lúc: sao chép kết quả vào các file hoặc biến được chỉ định và hiển thị kết quả.

### Cú pháp cơ bản
```
command | tee [OPTION]... [FILE]...
```

### Các option phổ biến
- `-a`: Append vào file thay vì ghi đè
- `-i`: Bỏ qua tín hiệu ngắt (interrupt signals)

## Phần 2: Ví dụ minh họa

### Ví dụ 1: Sử dụng tee cơ bản

```bash
# Hiển thị và lưu output vào file
echo "Hello, tee command!" | tee output.txt

# Kiểm tra nội dung file
cat output.txt
```

### Ví dụ 2: Sử dụng tee với option append

```bash
# Append thêm nội dung vào file
echo "This is a new line" | tee -a output.txt

# Kiểm tra nội dung file
cat output.txt
```

### Ví dụ 3: Sử dụng tee với nhiều file

```bash
# Lưu output vào nhiều file cùng lúc
echo "Saving to multiple files" | tee file1.txt file2.txt

# Kiểm tra nội dung các file
cat file1.txt
cat file2.txt
```

## Phần 3: Bài tập thực hành

### Bài tập 1: Logging trong khi thực thi lệnh

1. Tạo một script `system_check.sh` với nội dung sau:
   ```bash
   #!/bin/bash
   echo "Checking system..."
   df -h
   free -m
   top -bn1 | head -n 5
   ```
2. Chạy script và sử dụng `tee` để vừa hiển thị kết quả vừa lưu vào file log.
3. Kiểm tra nội dung file log.

Bài giải:

```bash
# Tạo script
cat > system_check.sh << EOF
#!/bin/bash
echo "Checking system..."
df -h
free -m
top -bn1 | head -n 5
EOF

# Đặt quyền thực thi cho script
chmod +x system_check.sh

# Chạy script với tee
./system_check.sh | tee system_check.log

# Kiểm tra nội dung log
cat system_check.log
```

### Bài tập 2: Sử dụng tee trong pipeline

1. Tạo một file `data.txt` với một số dòng văn bản.
2. Sử dụng `cat`, `grep`, và `tee` trong một pipeline để:
   - Đọc nội dung file
   - Lọc các dòng chứa một từ khóa cụ thể
   - Hiển thị kết quả và lưu vào file mới

Bài giải:

```bash
# Tạo file data.txt
cat > data.txt << EOF
Apple is red
Banana is yellow
Cherry is red
Date is brown
EOF

# Sử dụng pipeline với tee
cat data.txt | grep "red" | tee filtered_data.txt

# Kiểm tra nội dung file mới
cat filtered_data.txt
```

## Phần 4: Ứng dụng thực tế

### Tình huống 1: Logging trong quá trình cài đặt phần mềm

Khi cài đặt phần mềm, bạn muốn vừa xem output trực tiếp vừa lưu log để phân tích sau này.

```bash
# Giả sử bạn đang cài đặt nginx
sudo apt-get update | tee installation.log
sudo apt-get install nginx -y | tee -a installation.log

# Kiểm tra log
tail installation.log
```

### Tình huống 2: Monitoring và logging hệ thống

Tạo một script để monitor hệ thống và log kết quả định kỳ.

```bash
#!/bin/bash

log_file="/var/log/system_monitor.log"

while true
do
    date | tee -a "$log_file"
    echo "CPU Usage:" | tee -a "$log_file"
    top -bn1 | head -n 3 | tail -n 2 | tee -a "$log_file"
    echo "Memory Usage:" | tee -a "$log_file"
    free -m | tee -a "$log_file"
    echo "Disk Usage:" | tee -a "$log_file"
    df -h | tee -a "$log_file"
    echo "------------------------" | tee -a "$log_file"
    sleep 3600  # Chạy mỗi giờ
done
```

Lưu script này với tên `system_monitor.sh`, đặt quyền thực thi (`chmod +x system_monitor.sh`), và chạy nó trong background (`./system_monitor.sh &`).

### Tình huống 3: Backup với xác nhận

Khi thực hiện backup, bạn muốn vừa xem quá trình backup vừa lưu log.

```bash
#!/bin/bash

source_dir="/path/to/source"
backup_dir="/path/to/backup"
log_file="backup.log"

echo "Starting backup at $(date)" | tee "$log_file"
rsync -av --delete "$source_dir" "$backup_dir" | tee -a "$log_file"
echo "Backup completed at $(date)" | tee -a "$log_file"

# Hiển thị xác nhận
echo "Backup completed. Log saved to $log_file"
```

## Kết luận

Trong bài lab này, chúng ta đã học về lệnh 'tee' trong Linux, cách sử dụng nó để vừa hiển thị output vừa lưu vào file. Chúng ta đã thực hành với các ví dụ cơ bản và áp dụng 'tee' vào các tình huống thực tế như logging quá trình cài đặt, monitoring hệ thống, và quản lý quá trình backup.

Lệnh 'tee' là một công cụ mạnh mẽ trong việc xử lý output, đặc biệt hữu ích trong các tác vụ quản trị hệ thống, debugging, và logging. Bằng cách kết hợp 'tee' với các lệnh khác trong pipeline, bạn có thể tạo ra các workflow hiệu quả để xử lý và lưu trữ thông tin hệ thống.

