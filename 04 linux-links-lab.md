# Bài Lab: Soft và Hard Links trong Linux

## Phần 1: Lý thuyết

### inode
- inode là một cấu trúc dữ liệu trong hệ thống tập tin Linux, chứa thông tin về một file hoặc thư mục.
- Mỗi inode có một số duy nhất (inode number) xác định nó trên ổ đĩa cứng.

### Soft Link (Symbolic Link)
- Giống như shortcut trong Windows.
- Trỏ đến tên file gốc.
- Có thể trỏ đến file trên filesystem khác.
- Bị ảnh hưởng nếu file gốc bị xóa hoặc đổi tên.

### Hard Link
- Trỏ trực tiếp đến inode của file gốc.
- Không thể tạo hard link cho thư mục hoặc file trên filesystem khác.
- Không bị ảnh hưởng khi file gốc bị xóa, đổi tên hoặc di chuyển.

### Lệnh `ln`
- Tạo hard link: `ln [OPTIONS] FILE LINK`
- Tạo soft link: `ln -s [OPTIONS] FILE LINK`

## Phần 2: Ví dụ minh họa

### Ví dụ 1: Tạo và kiểm tra Soft Link

```bash
# Tạo file gốc
echo "Đây là file gốc" > file_goc.txt

# Tạo soft link
ln -s file_goc.txt soft_link.txt

# Kiểm tra
ls -l
cat soft_link.txt

# Xóa file gốc
rm file_goc.txt

# Kiểm tra lại
cat soft_link.txt  # Sẽ báo lỗi
```

### Ví dụ 2: Tạo và kiểm tra Hard Link

```bash
# Tạo file gốc
echo "Đây là file gốc" > file_goc.txt

# Tạo hard link
ln file_goc.txt hard_link.txt

# Kiểm tra
ls -li  # Chú ý inode number giống nhau
cat hard_link.txt

# Xóa file gốc
rm file_goc.txt

# Kiểm tra lại
cat hard_link.txt  # Vẫn hoạt động bình thường
```

## Phần 3: Bài tập thực hành

### Bài tập 1: Tạo và quản lý Soft Links

1. Tạo một thư mục có tên `lab_links`.
2. Trong thư mục `lab_links`, tạo một file có tên `config.txt` với nội dung bất kỳ.
3. Tạo một soft link đến `config.txt` trong thư mục home của bạn.
4. Thay đổi nội dung của `config.txt` và kiểm tra thông qua soft link.
5. Đổi tên `config.txt` và kiểm tra soft link.

Bài giải:

```bash
mkdir lab_links
cd lab_links
echo "Đây là file cấu hình" > config.txt
ln -s $(pwd)/config.txt ~/config_link.txt
cat ~/config_link.txt
echo "Cấu hình đã được cập nhật" >> config.txt
cat ~/config_link.txt
mv config.txt new_config.txt
cat ~/config_link.txt  # Sẽ báo lỗi
```

### Bài tập 2: Tạo và quản lý Hard Links

1. Trong thư mục `lab_links`, tạo một file có tên `data.txt` với nội dung bất kỳ.
2. Tạo một hard link đến `data.txt` trong thư mục cha.
3. Kiểm tra inode của cả hai file.
4. Thay đổi nội dung của `data.txt` và kiểm tra thông qua hard link.
5. Xóa `data.txt` và kiểm tra hard link.

Bài giải:

```bash
cd lab_links
echo "Đây là dữ liệu quan trọng" > data.txt
ln data.txt ../data_hard.txt
ls -li data.txt ../data_hard.txt
echo "Dữ liệu đã được cập nhật" >> data.txt
cat ../data_hard.txt
rm data.txt
cat ../data_hard.txt  # Vẫn hoạt động bình thường
```

## Phần 4: Ứng dụng thực tế

### Tình huống 1: Quản lý phiên bản cấu hình

Giả sử bạn là quản trị hệ thống và muốn duy trì nhiều phiên bản của một file cấu hình, đồng thời luôn có một soft link trỏ đến phiên bản hiện tại.

```bash
# Tạo file cấu hình ban đầu
echo "Cấu hình v1" > config_v1.conf

# Tạo soft link đến phiên bản hiện tại
ln -s config_v1.conf current_config.conf

# Tạo phiên bản mới và cập nhật soft link
echo "Cấu hình v2" > config_v2.conf
ln -sf config_v2.conf current_config.conf

# Kiểm tra
cat current_config.conf
```

### Tình huống 2: Chia sẻ dữ liệu giữa các user

Giả sử bạn muốn chia sẻ một file dữ liệu giữa nhiều user mà không cần copy file.

```bash
# Tạo file dữ liệu trong thư mục của user1
sudo -u user1 bash -c 'echo "Dữ liệu chung" > /home/user1/shared_data.txt'

# Tạo hard link trong thư mục của user2
sudo ln /home/user1/shared_data.txt /home/user2/shared_data.txt

# Đặt quyền cho cả hai user
sudo chmod 664 /home/user1/shared_data.txt
sudo chown user1:users /home/user1/shared_data.txt
sudo chown user2:users /home/user2/shared_data.txt

# Kiểm tra
sudo -u user2 cat /home/user2/shared_data.txt
```

## Kết luận

Trong bài lab này, chúng ta đã học về soft links và hard links trong Linux, cách tạo và quản lý chúng, cũng như một số ứng dụng thực tế. Soft links hữu ích cho việc tạo shortcuts và quản lý phiên bản, trong khi hard links có thể được sử dụng để chia sẻ dữ liệu hiệu quả giữa các users hoặc locations khác nhau trên cùng một filesystem. Hiểu và sử dụng đúng cách các loại links này sẽ giúp bạn quản lý files và directories trong Linux hiệu quả hơn.

