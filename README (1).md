# Bài 2: Cấu hình phân quyền Nhóm và sudoers bằng visudo

## 1. Mục tiêu

* Tạo group `devops-admin`.
* Tạo user `deployer`.
* Đưa `deployer` vào group `devops-admin`.
* Cấu hình sudoers để group `devops-admin` được phép quản lý dịch vụ bằng `systemctl`.
* Các lệnh được cấp quyền không yêu cầu nhập password.

## 2. Tạo group

Lệnh đã sử dụng:

```bash
sudo groupadd devops-admin
```

Kiểm tra:

```bash
getent group devops-admin
```

## 3. Tạo user deployer

```bash
sudo adduser deployer
```

## 4. Thêm deployer vào group

```bash
sudo usermod -aG devops-admin deployer
```

Kiểm tra:

```bash
groups deployer
```

Kết quả cho thấy user `deployer` thuộc group `devops-admin`.

## 5. Cấu hình sudoers

Mở file sudoers bằng:

```bash
sudo visudo
```

Thêm dòng:

```text
%devops-admin ALL=(ALL) NOPASSWD: /usr/bin/systemctl start *, /usr/bin/systemctl stop *, /usr/bin/systemctl restart *, /usr/bin/systemctl status *
```

### Giải thích

* `%devops-admin`: áp dụng quyền cho group `devops-admin`.
* `ALL=(ALL)`: cho phép thực thi lệnh với quyền sudo.
* `NOPASSWD`: không yêu cầu nhập mật khẩu.
* `systemctl start *`: cho phép khởi động service.
* `systemctl stop *`: cho phép dừng service.
* `systemctl restart *`: cho phép restart service.
* `systemctl status *`: cho phép kiểm tra trạng thái service.

## 6. Kiểm tra bằng user deployer

Chuyển sang user:

```bash
su - deployer
```

Kiểm tra:

```bash
whoami
```

Kết quả:

```text
deployer
```

Kiểm tra quyền sudo:

```bash
sudo -l
```

Kết quả:

```text
User deployer may run the following commands on ubuntu:

    (ALL) NOPASSWD: /usr/bin/systemctl start *,
        /usr/bin/systemctl stop *,
        /usr/bin/systemctl restart *,
        /usr/bin/systemctl status *
```

## 7. Kiểm tra restart service

Thực hiện:

```bash
sudo systemctl restart cron
```

Lệnh thực hiện thành công mà không yêu cầu nhập password.

Kiểm tra trạng thái:

```bash
sudo systemctl status cron
```

Kết quả mong đợi:

```text
Active: active (running)
```

## 8. Kết luận

Đã tạo thành công group `devops-admin` và user `deployer`.

User `deployer` được cấp quyền quản lý các service bằng các lệnh:

```text
systemctl start
systemctl stop
systemctl restart
systemctl status
```

Các lệnh trên được thực thi thông qua `sudo` mà không cần nhập password, trong khi user không được cấp toàn quyền quản trị root.
