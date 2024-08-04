# Bài Lab: Lệnh 'awk' trong Linux

## Phần 1: Lý thuyết

`awk` là một công cụ và ngôn ngữ lập trình được thiết kế để xử lý và trích xuất dữ liệu. Nó đặc biệt hữu ích trong việc xử lý các file văn bản có cấu trúc và dữ liệu dạng bảng.

Cú pháp cơ bản:
```
awk 'pattern { action }' input-file
```

Các đặc điểm chính:
- `$0`: Toàn bộ dòng hiện tại
- `$1`, `$2`, ...: Trường thứ 1, 2, ... của dòng hiện tại
- `NF`: Số trường trong dòng hiện tại
- `NR`: Số dòng hiện tại
- `-F`: Chỉ định dấu phân cách trường

## Phần 2: Ví dụ minh họa

Trước tiên, hãy tạo một file mẫu để thực hành:

```bash
cat > employees.txt << EOL
John Doe,30,Developer,5000
Jane Smith,28,Designer,4500
Bob Johnson,35,Manager,6000
Alice Brown,32,Tester,4800
EOL
```

### 1. In trường đầu tiên

```bash
awk -F',' '{print $1}' employees.txt
```

### 2. In nhiều trường

```bash
awk -F',' '{print $1, $3}' employees.txt
```

### 3. In trường cuối cùng

```bash
awk -F',' '{print $NF}' employees.txt
```

### 4. Tìm kiếm cụ thể

```bash
awk '/Manager/' employees.txt
```

### 5. Thay thế từ

```bash
echo "Hello World" | awk '{$2="Linux"; print $0}'
```

### 6. Lọc dựa trên điều kiện

```bash
awk -F',' '$2 > 30 {print $0}' employees.txt
```

### 7. Đếm số trường

```bash
awk -F',' '{print NF}' employees.txt
```

## Phần 3: Bài tập thực hành

### Bài tập 1: Xử lý file /etc/passwd

1. In ra tên người dùng và shell của họ từ file /etc/passwd.
2. Đếm số lượng người dùng có shell là /bin/bash.
3. In ra tên người dùng có UID (trường thứ 3) lớn hơn 1000.

Bài giải:

```bash
# 1. In tên người dùng và shell
awk -F: '{print $1, $NF}' /etc/passwd

# 2. Đếm số lượng người dùng có shell là /bin/bash
awk -F: '$NF == "/bin/bash" {count++} END {print count}' /etc/passwd

# 3. In tên người dùng có UID > 1000
awk -F: '$3 > 1000 {print $1}' /etc/passwd
```

### Bài tập 2: Phân tích log Apache

Giả sử bạn có một file log Apache (/var/log/apache2/access.log). Hãy thực hiện các tác vụ sau:

1. In ra địa chỉ IP của các client.
2. Đếm số lượng request cho mỗi phương thức HTTP (GET, POST, etc.).
3. In ra các URL được truy cập nhiều hơn 10 lần.

Bài giải:

```bash
# 1. In địa chỉ IP của client
awk '{print $1}' /var/log/apache2/access.log | sort | uniq

# 2. Đếm số lượng request cho mỗi phương thức HTTP
awk '{count[$6]++} END {for (method in count) print method, count[method]}' /var/log/apache2/access.log

# 3. In URL được truy cập nhiều hơn 10 lần
awk '{count[$7]++} END {for (url in count) if (count[url] > 10) print url, count[url]}' /var/log/apache2/access.log
```

## Phần 4: Ứng dụng thực tế

### Tình huống 1: Phân tích hiệu suất hệ thống

Giả sử bạn có output từ lệnh `top` được lưu trong một file:

```bash
top -b -n 1 > system_status.txt
```

Bây giờ, hãy sử dụng `awk` để phân tích file này:

```bash
# In ra 5 process sử dụng nhiều CPU nhất
awk 'NR>7 {print $9, $12}' system_status.txt | sort -nr | head -5

# Tính tổng memory usage
awk 'NR>7 {sum += $10} END {print "Total Memory Usage:", sum}' system_status.txt
```

### Tình huống 2: Xử lý dữ liệu CSV

Giả sử bạn có một file CSV chứa dữ liệu bán hàng:

```bash
cat > sales.csv << EOL
Date,Product,Quantity,Price
2023-01-01,Apple,100,0.5
2023-01-02,Banana,150,0.3
2023-01-03,Orange,80,0.6
2023-01-04,Apple,120,0.5
EOL
```

Sử dụng `awk` để phân tích dữ liệu này:

```bash
# Tính tổng doanh thu
awk -F',' 'NR>1 {total += $3 * $4} END {print "Total Revenue: $" total}' sales.csv

# In ra sản phẩm có số lượng bán ra cao nhất
awk -F',' 'NR>1 {sales[$2] += $3} END {for (product in sales) print product, sales[product]}' sales.csv | sort -k2 -nr | head -1
```

### Tình huống 3: Phân tích log SSH

Giả sử bạn muốn phân tích file log SSH (/var/log/auth.log) để tìm các nỗ lực đăng nhập thất bại:

```bash
# Đếm số lần đăng nhập thất bại cho mỗi IP
awk '/Failed password/ {print $11}' /var/log/auth.log | sort | uniq -c | sort -nr

# In ra thời gian và IP của 10 nỗ lực đăng nhập thất bại gần nhất
awk '/Failed password/ {print $1, $2, $3, $11}' /var/log/auth.log | tail -10
```

## Kết luận

Trong bài lab này, chúng ta đã khám phá sức mạnh và tính linh hoạt của lệnh `awk` trong Linux. Từ việc xử lý dữ liệu đơn giản đến phân tích các file log phức tạp, `awk` đã chứng tỏ là một công cụ không thể thiếu cho việc xử lý văn bản và dữ liệu trong môi trường dòng lệnh.

Chúng ta đã thấy cách `awk` có thể được sử dụng để:
- Trích xuất và định dạng dữ liệu từ các file văn bản
- Thực hiện các phép tính và tổng hợp dữ liệu
- Lọc và tìm kiếm thông tin cụ thể
- Phân tích các file log hệ thống

Khi kết hợp `awk` với các lệnh Linux khác như `sort`, `uniq`, hay `head`, bạn có thể tạo ra các pipeline mạnh mẽ để xử lý và phân tích dữ liệu một cách hiệu quả.

Việc thành thạo `awk` sẽ giúp bạn trở thành một quản trị viên hệ thống hoặc nhà phát triển Linux giỏi hơn, cho phép bạn nhanh chóng trích xuất thông tin quan trọng từ các nguồn dữ liệu đa dạng.

