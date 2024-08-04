# Bài Lab: Input và Output Redirects trong Linux

## Phần 1: Lý thuyết

### Các loại Redirects trong Linux:
1. Standard input (stdin) - File descriptor 0
2. Standard output (stdout) - File descriptor 1
3. Standard error (stderr) - File descriptor 2

### Cách thêm văn bản vào file:
1. Sử dụng trình soạn thảo như `vi`
2. Sử dụng redirect output (`>` hoặc `>>`)
3. Sử dụng lệnh `echo` với redirect

### Các ký hiệu Redirect:
- `>`: Ghi đè (overwrite)
- `>>`: Thêm vào cuối file (append)
- `<`: Input redirect
- `2>`: Error redirect

## Phần 2: Ví dụ minh họa

### Ví dụ 1: Output Redirect

```bash
# Ghi đè nội dung của lệnh ls vào file
ls -l > file_list.txt

# Thêm nội dung vào cuối file
echo "Thêm dòng mới" >> file_list.txt

# Xem nội dung file
cat file_list.txt
```

### Ví dụ 2: Input Redirect

```bash
# Tạo file với nội dung
echo "Hello World" > input.txt

# Sử dụng input redirect với lệnh cat
cat < input.txt
```

### Ví dụ 3: Error Redirect

```bash
# Chuyển hướng lỗi vào file
ls /khongcothumucnay 2> error.log

# Xem nội dung file lỗi
cat error.log
```

## Phần 3: Bài tập thực hành

### Bài tập 1: Tạo và quản lý log file

1. Tạo một script `system_info.sh` với nội dung sau:
   ```bash
   #!/bin/bash
   echo "Thông tin hệ thống:"
   date
   echo "Uptime:"
   uptime
   echo "Disk usage:"
   df -h
   ```
2. Chạy script và lưu output vào file `system_log.txt`.
3. Thêm thông tin về memory usage vào cuối file log.
4. Tạo một file `errors.log` và chuyển hướng các lỗi có thể xảy ra khi chạy script vào file này.

Bài giải:

```bash
# Tạo script
cat > system_info.sh << EOF
#!/bin/bash
echo "Thông tin hệ thống:"
date
echo "Uptime:"
uptime
echo "Disk usage:"
df -h
EOF

# Đặt quyền thực thi cho script
chmod +x system_info.sh

# Chạy script và lưu output
./system_info.sh > system_log.txt

# Thêm thông tin về memory usage
echo "Memory usage:" >> system_log.txt
free -h >> system_log.txt

# Chạy script với error redirect
./system_info.sh 2> errors.log
```

### Bài tập 2: Xử lý dữ liệu với input redirect

1. Tạo một file `names.txt` chứa danh sách tên.
2. Viết một script `sort_names.sh` để sắp xếp và in ra các tên duy nhất.
3. Sử dụng input redirect để xử lý file `names.txt`.

Bài giải:

```bash
# Tạo file names.txt
cat > names.txt << EOF
Alice
Bob
Charlie
Alice
David
Bob
EOF

# Tạo script sort_names.sh
cat > sort_names.sh << EOF
#!/bin/bash
sort | uniq
EOF

chmod +x sort_names.sh

# Sử dụng input redirect
./sort_names.sh < names.txt
```

## Phần 4: Ứng dụng thực tế

### Tình huống 1: Tạo báo cáo hệ thống tự động

Giả sử bạn cần tạo một báo cáo hàng ngày về tình trạng hệ thống và gửi nó qua email.

```bash
#!/bin/bash

# Tạo file báo cáo
report_file="/tmp/system_report_$(date +%Y%m%d).txt"

# Thu thập thông tin hệ thống
echo "Báo cáo Hệ thống - $(date)" > $report_file
echo "-----------------------------" >> $report_file
echo "Disk Usage:" >> $report_file
df -h >> $report_file
echo "-----------------------------" >> $report_file
echo "Memory Usage:" >> $report_file
free -h >> $report_file
echo "-----------------------------" >> $report_file
echo "Top 5 CPU-consuming processes:" >> $report_file
ps aux --sort=-%cpu | head -n 6 >> $report_file

# Gửi email (giả định rằng 'mail' command đã được cấu hình)
mail -s "Daily System Report" admin@example.com < $report_file

# Log kết quả
echo "Report sent at $(date)" >> /var/log/system_reports.log 2>&1
```

### Tình huống 2: Log rotating và error handling

Giả sử bạn có một ứng dụng web tạo ra nhiều log files và bạn cần quản lý chúng hiệu quả.

```bash
#!/bin/bash

# Định nghĩa các biến
LOG_DIR="/var/log/myapp"
MAX_LOG_SIZE=10M
ROTATE_COUNT=5

# Hàm rotate log
rotate_log() {
    local log_file=$1
    if [ -f "$log_file" ] && [ $(du -b "$log_file" | cut -f1) -gt $(numfmt --from=iec $MAX_LOG_SIZE) ]; then
        for i in $(seq $((ROTATE_COUNT-1)) -1 1); do
            [ -f "${log_file}.$i" ] && mv "${log_file}.$i" "${log_file}.$((i+1))"
        done
        mv "$log_file" "${log_file}.1"
        touch "$log_file"
        echo "Log rotated: $log_file" >> "$LOG_DIR/rotate.log"
    fi
}

# Rotate access log
rotate_log "$LOG_DIR/access.log"

# Rotate error log
rotate_log "$LOG_DIR/error.log"

# Chạy ứng dụng với log redirect
/path/to/your/app >> "$LOG_DIR/access.log" 2>> "$LOG_DIR/error.log"
```

## Kết luận

Trong bài lab này, chúng ta đã học về Input và Output Redirects trong Linux. Chúng ta đã thực hành việc sử dụng các redirects để quản lý output, input, và errors. Qua các ví dụ thực tế, chúng ta đã thấy cách áp dụng những kỹ thuật này vào việc tạo báo cáo hệ thống tự động và quản lý log files.

Hiểu và sử dụng đúng cách Input và Output Redirects là kỹ năng quan trọng trong quản trị hệ thống Linux. Nó cho phép bạn tự động hóa nhiều tác vụ, quản lý logs hiệu quả, và xử lý errors một cách có hệ thống.

