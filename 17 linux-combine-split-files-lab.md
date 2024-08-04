# Bài Lab: Kết hợp và Chia tách File trong Linux

## Phần 1: Lý thuyết

### Kết hợp file sử dụng cat
- Lệnh `cat` có thể được sử dụng để kết hợp nhiều file thành một file duy nhất.
- Cú pháp: `cat file1 file2 file3 > combined_file`

### Chia tách file sử dụng split
- Lệnh `split` được sử dụng để chia một file thành nhiều file nhỏ hơn.
- Cú pháp: `split [OPTIONS] [INPUT [PREFIX]]`
- Các option phổ biến:
  - `-l, --lines=NUMBER`: Chia theo số dòng
  - `-b, --bytes=SIZE`: Chia theo kích thước (bytes, KB, MB, etc.)
  - `-n, --number=CHUNKS`: Chia thành số phần cụ thể

## Phần 2: Ví dụ minh họa

### Kết hợp file

Trước tiên, hãy tạo một vài file mẫu:

```bash
echo "This is file 1" > file1.txt
echo "This is file 2" > file2.txt
echo "This is file 3" > file3.txt
```

Kết hợp các file:

```bash
cat file1.txt file2.txt file3.txt > combined.txt

# Kiểm tra kết quả
cat combined.txt
```

### Chia tách file

Tạo một file lớn hơn để chia tách:

```bash
for i in {1..1000}; do
    echo "This is line $i" >> large_file.txt
done
```

Chia tách file:

```bash
# Chia thành các file nhỏ, mỗi file 300 dòng
split -l 300 large_file.txt split_file_

# Kiểm tra kết quả
ls split_file_*
wc -l split_file_*
```

## Phần 3: Bài tập thực hành

### Bài tập 1: Kết hợp và chia tách log files

Giả sử bạn có nhiều file log hàng ngày và muốn kết hợp chúng thành một file duy nhất, sau đó chia nhỏ để phân tích.

1. Tạo các file log mẫu:

```bash
for day in {1..5}; do
    for i in {1..100}; do
        echo "Day $day - Log entry $i: $(date)" >> log_day_$day.txt
    done
done
```

2. Kết hợp tất cả các file log thành một file duy nhất.
3. Chia file kết hợp thành các file nhỏ hơn, mỗi file chứa 200 dòng.

Bài giải:

```bash
# Kết hợp các file log
cat log_day_*.txt > combined_log.txt

# Chia file kết hợp
split -l 200 combined_log.txt split_log_

# Kiểm tra kết quả
wc -l split_log_*
```

### Bài tập 2: Chia tách file theo kích thước

Giả sử bạn cần chia một file lớn thành các phần nhỏ hơn để dễ dàng truyền qua mạng.

1. Tạo một file lớn (khoảng 10MB):

```bash
dd if=/dev/urandom of=large_file.bin bs=1M count=10
```

2. Chia file này thành các phần, mỗi phần 2MB.
3. Kiểm tra kích thước của các file đã chia.

Bài giải:

```bash
# Chia file
split -b 2M large_file.bin chunk_

# Kiểm tra kích thước các file đã chia
ls -lh chunk_*
```

## Phần 4: Ứng dụng thực tế

### Tình huống 1: Hợp nhất và phân tích log files

Trong quản trị hệ thống, việc hợp nhất log files từ nhiều nguồn và sau đó phân tích chúng là một tác vụ phổ biến.

```bash
#!/bin/bash

# Hợp nhất log files từ nhiều server
cat /var/log/server1/*.log /var/log/server2/*.log > combined_server_logs.txt

# Chia nhỏ file log hợp nhất để phân tích
split -l 50000 combined_server_logs.txt analyzed_log_

# Phân tích từng phần
for file in analyzed_log_*; do
    echo "Analyzing $file"
    grep "ERROR" $file | wc -l
done
```

### Tình huống 2: Backup và truyền file lớn

Khi cần backup hoặc truyền các file rất lớn qua mạng, việc chia nhỏ file có thể hữu ích.

```bash
#!/bin/bash

LARGE_FILE="/path/to/large_database_dump.sql"
CHUNK_SIZE="100M"
BACKUP_DIR="/path/to/backup"

# Chia file lớn thành các phần nhỏ hơn
split -b $CHUNK_SIZE $LARGE_FILE $BACKUP_DIR/db_chunk_

# Tạo file checksum cho mỗi phần
for chunk in $BACKUP_DIR/db_chunk_*; do
    md5sum $chunk > $chunk.md5
done

# Nén mỗi phần
for chunk in $BACKUP_DIR/db_chunk_*; do
    gzip $chunk
done

echo "Backup completed. Files are ready for transmission."
```

### Tình huống 3: Xử lý dữ liệu lớn

Khi làm việc với các tập dữ liệu lớn, việc chia nhỏ để xử lý song song có thể cải thiện hiệu suất.

```bash
#!/bin/bash

LARGE_DATASET="big_data.csv"
NUM_PARTS=4

# Đếm số dòng và tính số dòng cho mỗi phần
TOTAL_LINES=$(wc -l < $LARGE_DATASET)
LINES_PER_PART=$((TOTAL_LINES / NUM_PARTS + 1))

# Chia dataset
split -l $LINES_PER_PART $LARGE_DATASET data_part_

# Xử lý song song
for part in data_part_*; do
    python process_data.py $part &
done

wait
echo "All parts processed."
```

## Kết luận

Trong bài lab này, chúng ta đã khám phá cách sử dụng lệnh `cat` để kết hợp files và `split` để chia tách files trong Linux. Những kỹ thuật này rất hữu ích trong nhiều tình huống thực tế, bao gồm:

1. Quản lý và phân tích log files
2. Backup và truyền dữ liệu lớn
3. Xử lý dữ liệu quy mô lớn

Khi kết hợp các lệnh này với shell scripting và các công cụ khác trong Linux, bạn có thể tạo ra các giải pháp mạnh mẽ cho việc xử lý dữ liệu và quản lý hệ thống.

Lưu ý rằng khi làm việc với dữ liệu lớn hoặc quan trọng, luôn đảm bảo bạn có bản sao lưu và kiểm tra tính toàn vẹn của dữ liệu sau khi thực hiện các thao tác kết hợp hoặc chia tách.

