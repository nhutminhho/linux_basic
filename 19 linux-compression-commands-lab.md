# Bài Lab: Nén và Giải nén File trong Linux với tar và gzip

## Phần 1: Lý thuyết

### tar (tape archive)
- `tar` được sử dụng để tạo và quản lý các file archive.
- Cú pháp: `tar [OPTIONS] [ARCHIVE_NAME] [FILES]`
- Các option phổ biến:
  - `-c`: Tạo archive mới
  - `-x`: Giải nén archive
  - `-v`: Hiển thị quá trình xử lý (verbose)
  - `-f`: Chỉ định tên file archive
  - `-z`: Sử dụng gzip để nén/giải nén

### gzip
- `gzip` được sử dụng để nén file.
- Cú pháp: `gzip [OPTIONS] [FILE]`
- Các option phổ biến:
  - `-d`: Giải nén (tương đương với `gunzip`)
  - `-k`: Giữ lại file gốc
  - `-r`: Nén đệ quy (cho thư mục)

### gunzip
- `gunzip` được sử dụng để giải nén file đã nén bởi gzip.
- Cú pháp: `gunzip [OPTIONS] [FILE]`
- Tương đương với `gzip -d`

## Phần 2: Ví dụ minh họa

Trước tiên, hãy tạo một số file mẫu để thực hành:

```bash
mkdir compression_lab
cd compression_lab
echo "This is file 1" > file1.txt
echo "This is file 2" > file2.txt
echo "This is file 3" > file3.txt
```

### Sử dụng tar

```bash
# Tạo archive không nén
tar -cvf archive.tar file1.txt file2.txt file3.txt

# Tạo archive có nén gzip
tar -czvf archive.tar.gz file1.txt file2.txt file3.txt

# Giải nén archive
tar -xvf archive.tar

# Giải nén archive đã nén gzip
tar -xzvf archive.tar.gz
```

### Sử dụng gzip

```bash
# Nén một file
gzip file1.txt

# Nén nhiều file
gzip file2.txt file3.txt

# Giải nén file
gzip -d file1.txt.gz

# Hoặc sử dụng gunzip
gunzip file2.txt.gz
```

## Phần 3: Bài tập thực hành

### Bài tập 1: Backup và Restore

Giả sử bạn cần tạo backup cho thư mục home của một user và sau đó restore nó.

1. Tạo một thư mục mẫu với một số file:

```bash
mkdir -p ~/backup_test/docs ~/backup_test/images
echo "Important document" > ~/backup_test/docs/important.txt
echo "Secret data" > ~/backup_test/docs/secret.txt
dd if=/dev/urandom of=~/backup_test/images/image1.jpg bs=1M count=1
dd if=/dev/urandom of=~/backup_test/images/image2.jpg bs=1M count=1
```

2. Tạo một backup nén của thư mục này.
3. Xóa thư mục gốc.
4. Restore backup.

Bài giải:

```bash
# Tạo backup
tar -czvf backup.tar.gz ~/backup_test

# Xóa thư mục gốc
rm -rf ~/backup_test

# Restore backup
tar -xzvf backup.tar.gz -C ~
```

### Bài tập 2: Nén log files

Giả sử bạn cần nén các file log cũ để tiết kiệm không gian đĩa.

1. Tạo một số file log mẫu:

```bash
mkdir logs
for i in {1..5}; do
    echo "Log entry $i" > logs/log$i.txt
done
```

2. Nén tất cả các file log, giữ lại bản gốc.
3. Xóa các file log gốc nếu file nén tồn tại và có kích thước nhỏ hơn file gốc.

Bài giải:

```bash
# Nén các file log
gzip -k logs/*.txt

# Xóa file gốc nếu file nén tồn tại và nhỏ hơn
for file in logs/*.txt; do
    if [ -f "$file.gz" ] && [ $(stat -c%s "$file") -gt $(stat -c%s "$file.gz") ]; then
        rm "$file"
    fi
done
```

## Phần 4: Ứng dụng thực tế

### Tình huống 1: Backup cấu hình hệ thống

Giả sử bạn cần tạo một backup cho các file cấu hình quan trọng trong thư mục /etc.

```bash
# Tạo backup
sudo tar -czvf etc_backup.tar.gz /etc/passwd /etc/group /etc/shadow /etc/ssh/sshd_config

# Kiểm tra nội dung của archive
tar -tvf etc_backup.tar.gz

# Giả sử bạn cần khôi phục file sshd_config
sudo tar -xzvf etc_backup.tar.gz etc/ssh/sshd_config -C /
```

### Tình huống 2: Nén log files cũ

Trong quản trị hệ thống, việc nén các file log cũ là một tác vụ phổ biến để tiết kiệm không gian đĩa.

```bash
# Tìm và nén các file log cũ hơn 7 ngày
find /var/log -name "*.log" -type f -mtime +7 -exec gzip {} \;

# Giải nén một file log cụ thể để kiểm tra
gunzip -c /var/log/syslog.1.gz | less
```

### Tình huống 3: Chia sẻ nhiều file qua mạng

Khi cần chia sẻ nhiều file qua mạng, việc nén chúng thành một file duy nhất sẽ tiện lợi hơn.

```bash
# Tạo một archive chứa tất cả các file .jpg trong thư mục hiện tại
tar -czvf images.tar.gz *.jpg

# Giả sử bạn nhận được file archive này và muốn giải nén
tar -xzvf images.tar.gz -C ~/received_images
```

## Kết luận

Trong bài lab này, chúng ta đã khám phá cách sử dụng các lệnh `tar`, `gzip`, và `gunzip` trong Linux để nén và giải nén files và directories. Chúng ta đã thấy rằng:

- `tar` rất hữu ích cho việc gom nhóm nhiều file và thư mục vào một archive duy nhất.
- `gzip` giúp nén các file riêng lẻ hoặc được sử dụng kết hợp với `tar` để tạo ra các archive nén.
- `gunzip` (hoặc `gzip -d`) được sử dụng để giải nén các file đã được nén bởi gzip.

Các lệnh này đặc biệt hữu ích trong nhiều tình huống thực tế như:
- Tạo và quản lý backups
- Tiết kiệm không gian đĩa bằng cách nén các file log cũ
- Chia sẻ nhiều file qua mạng một cách hiệu quả

Khi kết hợp các lệnh này với các công cụ khác như `find` hoặc shell scripting, bạn có thể tạo ra các quy trình tự động hóa mạnh mẽ cho việc quản lý và bảo trì hệ thống Linux của mình.

