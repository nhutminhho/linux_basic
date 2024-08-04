# Bài Lab: Tìm kiếm Files và Directories trong Linux - Ứng dụng thực tế

## Phần 1: Sử dụng lệnh `find` trong tình huống thực tế

### Tình huống 1: Tìm và xóa các file log cũ

Giả sử bạn là quản trị viên hệ thống và cần giải phóng không gian đĩa bằng cách xóa các file log cũ hơn 30 ngày.

```bash
# Tìm và liệt kê các file log cũ hơn 30 ngày
find /var/log -name "*.log" -type f -mtime +30

# Xóa các file log cũ hơn 30 ngày
find /var/log -name "*.log" -type f -mtime +30 -delete
```

Giải thích:
- `-mtime +30` tìm các file được sửa đổi cách đây hơn 30 ngày
- `-delete` xóa các file tìm thấy

### Tình huống 2: Tìm các file lớn chiếm nhiều dung lượng

Khi hệ thống gần hết dung lượng, bạn cần tìm các file lớn để xem xét xóa hoặc nén.

```bash
# Tìm các file lớn hơn 100MB và hiển thị kích thước
find / -type f -size +100M -exec ls -lh {} \; | sort -rh | head -n 10
```

Giải thích:
- `-exec ls -lh {} \;` thực thi lệnh `ls -lh` cho mỗi file tìm thấy
- `sort -rh` sắp xếp kết quả theo kích thước giảm dần
- `head -n 10` hiển thị 10 kết quả đầu tiên

## Phần 2: Sử dụng lệnh `locate` trong tình huống thực tế

### Tình huống 3: Tìm kiếm nhanh các file cấu hình

Bạn cần tìm nhanh tất cả các file cấu hình của Apache trong hệ thống.

```bash
# Cập nhật database locate
sudo updatedb

# Tìm kiếm các file cấu hình Apache
locate apache | grep '\.conf$'
```

Giải thích:
- `locate apache` tìm tất cả các file có chứa từ "apache"
- `grep '\.conf$'` lọc kết quả chỉ giữ lại các file có đuôi .conf

## Phần 3: Sử dụng Wildcards trong tình huống thực tế

### Tình huống 4: Sao lưu các file quan trọng

Bạn cần sao lưu tất cả các file cấu hình trong thư mục /etc bắt đầu bằng "ssh".

```bash
# Sao chép tất cả các file bắt đầu bằng "ssh" trong /etc vào thư mục backup
cp /etc/ssh* /backup/

# Liệt kê các file đã sao chép
ls /backup/ssh*
```

Giải thích:
- `ssh*` đại diện cho tất cả các file bắt đầu bằng "ssh"

### Tình huống 5: Tìm và di chuyển các file ảnh

Bạn cần tìm và di chuyển tất cả các file ảnh (jpg, png, gif) trong thư mục Documents vào thư mục Pictures.

```bash
# Tìm và di chuyển các file ảnh
find ~/Documents -type f \( -name "*.jpg" -o -name "*.png" -o -name "*.gif" \) -exec mv {} ~/Pictures \;

# Kiểm tra kết quả
ls ~/Pictures
```

Giải thích:
- `\( ... \)` nhóm các điều kiện
- `-o` là toán tử OR
- `-exec mv {} ~/Pictures \;` di chuyển mỗi file tìm thấy vào thư mục Pictures

## Phần 4: Kết hợp `find` với các lệnh khác trong tình huống thực tế

### Tình huống 6: Tìm và thay thế nội dung trong nhiều file

Giả sử bạn cần thay đổi địa chỉ IP cũ (192.168.1.100) thành địa chỉ IP mới (192.168.1.200) trong tất cả các file cấu hình.

```bash
# Tìm các file chứa IP cũ
find /etc -type f -exec grep -l "192.168.1.100" {} +

# Thay thế IP cũ bằng IP mới
find /etc -type f -exec sed -i 's/192.168.1.100/192.168.1.200/g' {} +

# Kiểm tra kết quả
find /etc -type f -exec grep -l "192.168.1.200" {} +
```

Giải thích:
- `grep -l` liệt kê các file chứa chuỗi tìm kiếm
- `sed -i 's/old/new/g'` thay thế chuỗi trong file

### Tình huống 7: Tìm và nén các file log cũ

Bạn cần tìm tất cả các file log cũ hơn 7 ngày và nén chúng để tiết kiệm không gian.

```bash
# Tìm và nén các file log cũ
find /var/log -name "*.log" -type f -mtime +7 -exec gzip {} \;

# Kiểm tra kết quả
ls /var/log/*.gz
```

Giải thích:
- `-mtime +7` tìm các file được sửa đổi cách đây hơn 7 ngày
- `-exec gzip {} \;` nén mỗi file tìm thấy

## Kết luận:

Trong bài lab này, chúng ta đã áp dụng các kỹ thuật tìm kiếm files và directories vào các tình huống thực tế mà một quản trị viên hệ thống có thể gặp phải. Chúng ta đã sử dụng `find`, `locate`, và wildcards để thực hiện các tác vụ như tìm và xóa file log cũ, tìm file lớn, sao lưu file cấu hình, di chuyển file ảnh, thay thế nội dung trong nhiều file, và nén file log.

Những kỹ năng này rất hữu ích trong việc quản lý hệ thống, bảo trì, và xử lý sự cố. Bằng cách kết hợp các lệnh và kỹ thuật khác nhau, bạn có thể thực hiện các tác vụ phức tạp một cách hiệu quả trên hệ thống Linux.

