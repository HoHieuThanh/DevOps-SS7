# BÁO CÁO BÀI 4: CẤU HÌNH REVERSE PROXY NGINX CHO ỨNG DỤNG SPRING BOOT

## 1. Mục tiêu

Cấu hình Nginx làm Reverse Proxy trên VPS Ubuntu nhằm:

* Phục vụ trang web tĩnh tại `/var/www/html/`.
* Cho phép người dùng truy cập trang web thông qua port `80`.
* Chuyển tiếp các request có tiền tố `/api/` đến ứng dụng Spring Boot chạy trên port `8082`.
* Kiểm tra cú pháp và hoạt động của Nginx.

## 2. Tạo trang web tĩnh

Tạo file:

```text
/var/www/html/index.html
```

Nội dung trang hiển thị thông tin học viên:

```text
Họ tên: HỒ HIỂU THANH
Mã lớp: HN-KS24-CNTT2
```

Trang cũng hiển thị thông tin:

```text
Nginx Reverse Proxy
Trang web được phục vụ bởi Nginx.
```

## 3. Tạo cấu hình Nginx

Tạo file:

```text
/etc/nginx/sites-available/spring-proxy.conf
```

Nội dung cấu hình:

```nginx
server {
    listen 80;
    listen [::]:80;

    server_name _;

    root /var/www/html;
    index index.html;

    location / {
        try_files $uri $uri/ /index.html;
    }

    location /api/ {
        proxy_pass http://127.0.0.1:8082/;

        proxy_http_version 1.1;

        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

Trong đó:

* `/` được phục vụ trực tiếp từ `/var/www/html/`.
* `/api/` được chuyển tiếp đến Spring Boot tại `127.0.0.1:8082`.
* Các HTTP header như `Host`, `X-Real-IP` và `X-Forwarded-For` được chuyển tiếp đến backend.

## 4. Kích hoạt cấu hình

Tạo symbolic link:

```bash
sudo ln -s /etc/nginx/sites-available/spring-proxy.conf /etc/nginx/sites-enabled/spring-proxy.conf
```

Sau đó reload Nginx để áp dụng cấu hình.

## 5. Kiểm tra cú pháp Nginx

Thực hiện:

```bash
sudo nginx -t
```

Kết quả thực tế:

```text
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```

Như vậy cấu hình Nginx **không có lỗi cú pháp** và có thể sử dụng.

## 6. Kiểm tra truy cập trang web

Thực hiện:

```bash
curl -I http://localhost/
```

Kết quả:

```text
HTTP/1.1 200 OK
Server: nginx/1.24.0 (Ubuntu)
Content-Type: text/html
```

Mã trạng thái `200 OK` cho thấy Nginx đã phục vụ thành công trang HTML tĩnh.

Kiểm tra nội dung:

```bash
curl http://localhost/
```

Kết quả hiển thị đúng thông tin:

```text
Thông tin học viên

Họ tên: HỒ HIỂU THANH
Mã lớp: HN-KS24-CNTT2

Nginx Reverse Proxy
Trang web được phục vụ bởi Nginx.
```