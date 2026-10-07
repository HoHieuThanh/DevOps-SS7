# BÁO CÁO BÀI 2: QUẢN TRỊ TƯỜNG LỬA UFW CHO CỤM DỊCH VỤ MULTI-PORT

## 1. Mục tiêu

Cấu hình tường lửa **UFW** trên máy chủ Ubuntu nhằm bảo vệ hệ thống và kiểm soát các cổng dịch vụ:

* SSH: port `22`
* Web HTTP: port `80`
* Spring Boot: port `8082`
* MySQL: port `3306` bị chặn từ Internet.

## 2. Cấu hình chính sách mặc định

Thiết lập UFW chặn toàn bộ kết nối đi vào và cho phép kết nối đi ra:

```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing
```

Kết quả:

```text
Default: deny (incoming), allow (outgoing), disabled (routed)
```

## 3. Cho phép các cổng dịch vụ

Mở các cổng cần thiết:

```bash
sudo ufw allow 22/tcp
sudo ufw allow 80/tcp
sudo ufw allow 8082/tcp
```

Trong đó:

* `22/tcp`: cho phép kết nối SSH để quản trị VPS.
* `80/tcp`: cho phép truy cập Web HTTP.
* `8082/tcp`: cho phép truy cập ứng dụng Spring Boot.

Không mở port `3306/tcp` để ngăn kết nối MySQL trực tiếp từ Internet.

## 4. Kích hoạt UFW

Sau khi cấu hình các luật firewall, kích hoạt UFW:

```bash
sudo ufw enable
```

Kiểm tra trạng thái:

```bash
sudo ufw status verbose
```

## 5. Kết quả kiểm tra

Kết quả thực tế trên VPS:

```text
Status: active
Logging: on (low)
Default: deny (incoming), allow (outgoing), disabled (routed)

To                         Action      From
--                         ------      ----
22/tcp                     ALLOW IN    Anywhere
80/tcp                     ALLOW IN    Anywhere
8082/tcp                   ALLOW IN    Anywhere
22/tcp (v6)                ALLOW IN    Anywhere (v6)
80/tcp (v6)                ALLOW IN    Anywhere (v6)
8082/tcp                   ALLOW IN    Anywhere (v6)
```

