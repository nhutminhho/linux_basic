# Bài Lab: So sánh Lệnh Linux và Windows

## Phần 1: Lý thuyết

Dưới đây là bảng so sánh các lệnh phổ biến giữa Linux và Windows:

| Chức năng | Windows | Linux |
|-----------|---------|-------|
| Liệt kê nội dung thư mục | dir | ls -l |
| Đổi tên file | ren | mv |
| Sao chép file | copy | cp |
| Di chuyển file | move | mv |
| Xóa màn hình | cls | clear |
| Xóa file | del | rm |
| So sánh nội dung file | fc | diff |
| Tìm kiếm chuỗi trong file | find | grep |
| Hiển thị trợ giúp lệnh | command /? | man command |
| Hiển thị vị trí hiện tại | chdir | pwd |
| Hiển thị thời gian | time | date |

## Phần 2: Ví dụ minh họa

Chúng ta sẽ tạo một số ví dụ để minh họa cách sử dụng các lệnh này trên cả hai hệ điều hành.

### 1. Liệt kê nội dung thư mục

Windows:
```
C:\> dir
```

Linux:
```bash
$ ls -l
```

### 2. Đổi tên file

Windows:
```
C:\> ren oldfile.txt newfile.txt
```

Linux:
```bash
$ mv oldfile.txt newfile.txt
```

### 3. Tìm kiếm chuỗi trong file

Windows:
```
C:\> find "error" logfile.txt
```

Linux:
```bash
$ grep "error" logfile.txt
```

## Phần 3: Bài tập thực hành

### Bài tập 1: Quản lý file và thư mục

Thực hiện các thao tác sau trên cả Windows và Linux:

1. Tạo một thư mục mới tên là "project".
2. Di chuyển vào thư mục "project".
3. Tạo một file text có tên "notes.txt" với nội dung "This is a test file".
4. Liệt kê nội dung của thư mục.
5. Đổi tên file "notes.txt" thành "project_notes.txt".
6. Sao chép file "project_notes.txt" thành "backup_notes.txt".
7. Xóa file "backup_notes.txt".

Bài giải:

Windows:
```
C:\> mkdir project
C:\> cd project
C:\project> echo This is a test file > notes.txt
C:\project> dir
C:\project> ren notes.txt project_notes.txt
C:\project> copy project_notes.txt backup_notes.txt
C:\project> del backup_notes.txt
```

Linux:
```bash
$ mkdir project
$ cd project
$ echo "This is a test file" > notes.txt
$ ls -l
$ mv notes.txt project_notes.txt
$ cp project_notes.txt backup_notes.txt
$ rm backup_notes.txt
```

### Bài tập 2: Tìm kiếm và so sánh

1. Tạo hai file: "file1.txt" và "file2.txt" với nội dung khác nhau.
2. Tìm kiếm một từ cụ thể trong "file1.txt".
3. So sánh nội dung của "file1.txt" và "file2.txt".

Bài giải:

Windows:
```
C:\project> echo This is file 1 > file1.txt
C:\project> echo This is file 2 > file2.txt
C:\project> find "file" file1.txt
C:\project> fc file1.txt file2.txt
```

Linux:
```bash
$ echo "This is file 1" > file1.txt
$ echo "This is file 2" > file2.txt
$ grep "file" file1.txt
$ diff file1.txt file2.txt
```

## Phần 4: Ứng dụng thực tế

### Tình huống 1: Quản lý Log Files

Giả sử bạn là quản trị viên hệ thống và cần xử lý các file log hàng ngày.

Windows:
```
C:\logs> for %i in (log*.txt) do @echo Processing %i && find "ERROR" %i
C:\logs> copy log*.txt combined_log.txt
```

Linux:
```bash
$ for file in log*.txt; do echo "Processing $file"; grep "ERROR" $file; done
$ cat log*.txt > combined_log.txt
```

### Tình huống 2: Backup Script

Tạo một script đơn giản để backup các file quan trọng.

Windows (batch script - backup.bat):
```batch
@echo off
set BACKUP_DIR=C:\backup
if not exist %BACKUP_DIR% mkdir %BACKUP_DIR%
for %%F in (*.doc *.xlsx *.ppt) do (
    copy %%F %BACKUP_DIR%\%%~nF_%date:~-4,4%%date:~-10,2%%date:~-7,2%%%~xF
)
echo Backup completed.
```

Linux (shell script - backup.sh):
```bash
#!/bin/bash
BACKUP_DIR="/home/user/backup"
mkdir -p $BACKUP_DIR
for file in *.doc *.xlsx *.ppt; do
    cp "$file" "$BACKUP_DIR/${file%.*}_$(date +%Y%m%d).${file##*.}"
done
echo "Backup completed."
```

### Tình huống 3: Monitoring System Resources

Tạo một script đơn giản để monitor tài nguyên hệ thống.

Windows (PowerShell script - monitor.ps1):
```powershell
While($true) {
    $cpu = (Get-WmiObject Win32_Processor).LoadPercentage
    $mem = (Get-WmiObject Win32_OperatingSystem).FreePhysicalMemory
    Write-Host "CPU: $cpu%, Free Memory: $mem KB"
    Start-Sleep -Seconds 5
}
```

Linux (shell script - monitor.sh):
```bash
#!/bin/bash
while true; do
    cpu=$(top -bn1 | grep "Cpu(s)" | awk '{print $2 + $4}')
    mem=$(free -m | awk 'NR==2{printf "%.2f%%", $3*100/$2 }')
    echo "CPU: $cpu%, Memory used: $mem"
    sleep 5
done
```

## Kết luận

Trong bài lab này, chúng ta đã khám phá sự tương đồng và khác biệt giữa các lệnh phổ biến trong Windows và Linux. Mặc dù có nhiều điểm khác nhau về cú pháp và cách sử dụng, nhưng các chức năng cơ bản thường có thể được thực hiện trên cả hai hệ điều hành với các lệnh tương ứng.

Một số điểm quan trọng cần nhớ:
1. Linux phân biệt chữ hoa chữ thường, trong khi Windows không.
2. Linux sử dụng dấu gạch chéo (/) cho đường dẫn, Windows sử dụng dấu gạch chéo ngược (\).
3. Linux có nhiều công cụ mạnh mẽ cho xử lý văn bản và tự động hóa (như grep, awk, sed), trong khi Windows thường dựa vào PowerShell cho các tác vụ nâng cao.

Việc hiểu và có thể làm việc trên cả hai hệ điều hành là một kỹ năng quý giá cho các chuyên gia IT, đặc biệt là trong môi trường doanh nghiệp nơi cả hai hệ thống có thể được sử dụng song song.

