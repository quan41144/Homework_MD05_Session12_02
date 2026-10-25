## 1. Đoạn cấu hình Nginx sau khi cập nhật

```nginx
server {
    listen 80;
    server_name api.example.com;

    location /api/export/ {
        proxy_pass http://localhost:8080;

        # Cấu hình thời gian chờ backend xử lý lên đến 120s
        proxy_connect_timeout 120s;
        proxy_send_timeout    120s;
        proxy_read_timeout    120s;

        # Cấu hình thời gian gửi dữ liệu về client (tùy chọn)
        send_timeout          120s;
    }
}
```

## 2. Giải thích ý nghĩa các thông số Timeout

- **`proxy_read_timeout`**:
  - **Ý nghĩa**: Xác định thời gian tối đa Nginx chờ nhận phản hồi từ Upstream server (Spring Boot) giữa 2 lần đọc liên tiếp.
  - **Mặc định**: `60s`.
  - **Lý do lỗi 504**: Spring Boot mất khoảng 90s để xử lý xuất báo cáo, vượt quá 60s mặc định nên Nginx chủ động ngắt kết nối và trả về lỗi `504 Gateway Time-out`. Tăng lên `120s` giúp Nginx kiên nhẫn chờ backend xử lý xong.

- **`proxy_connect_timeout`**:
  - **Ý nghĩa**: Thời gian tối đa Nginx chờ để thiết lập kết nối TCP ban đầu với Upstream backend.
  - **Mặc định**: `60s`.

- **`proxy_send_timeout`**:
  - **Ý nghĩa**: Thời gian tối đa Nginx chờ khi truyền request từ Nginx sang Upstream backend (giữa 2 lần ghi liên tiếp).
  - **Mặc định**: `60s`.

- **`send_timeout`**:
  - **Ý nghĩa**: Thời gian tối đa Nginx chờ client nhận dữ liệu phản hồi (giữa 2 lần gửi liên tiếp về trình duyệt/người dùng).
