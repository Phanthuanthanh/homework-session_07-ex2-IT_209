# Bài 2: Quản trị Tường lửa UFW cho Cụm Dịch vụ Multi-port

## 1. Mục Tiêu
- Thiết lập tường lửa UFW để bảo mật hệ thống máy chủ chạy nhiều cổng dịch vụ đồng thời.
- Cấu hình mở rộng cổng cho các dịch vụ web và cô lập các cổng cơ sở dữ liệu theo nguyên tắc Least Privilege.

## 2. Bối Cảnh & Ràng Buộc
- **Bối cảnh:** Máy chủ chạy 4 dịch vụ: SSH (port 22), Nginx (port 80), Spring Boot (port 8082), MySQL (port 3306).
- **Ràng buộc:**
  - Chặn mặc định toàn bộ lưu lượng đi vào (`deny incoming`).
  - Cho phép mặc định toàn bộ lưu lượng đi ra (`allow outgoing`).
  - Cho phép kết nối SSH qua cổng `22/tcp`.
  - Cho phép kết nối Web HTTP qua cổng `80/tcp`.
  - Cho phép kết nối ứng dụng Spring Boot qua cổng `8082/tcp`.
  - Chặn hoàn toàn cổng MySQL (`3306/tcp`) từ Internet (bị chặn bởi chính sách mặc định, không thêm luật allow).

## 3. Các Lệnh Cấu Hình UFW
```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow 22/tcp
sudo ufw allow 80/tcp
sudo ufw allow 8082/tcp
sudo ufw enable
```

## 4. Kiểm Tra Trạng Thái Tường Lửa
Lệnh kiểm tra:
```bash
sudo ufw status verbose
```

Đầu ra thực tế:
```text
Status: active
Logging: on (low)
Default: deny (incoming), allow (outgoing), disabled (routed)
New profiles: skip

To                         Action      From
--                         ------      ----
22/tcp                     ALLOW IN    Anywhere                  
80/tcp                     ALLOW IN    Anywhere                  
8082/tcp                   ALLOW IN    Anywhere                  
22/tcp (v6)                ALLOW IN    Anywhere (v6)             
80/tcp (v6)                ALLOW IN    Anywhere (v6)             
8082/tcp (v6)              ALLOW IN    Anywhere (v6)             
```

## 5. Đối Chiếu Kết Quả Mong Đợi
- **Trạng thái tường lửa:** `Status: active`.
- **Chính sách mặc định:** `deny (incoming)` và `allow (outgoing)`.
- **Cổng được phép truy cập từ bên ngoài:** `22/tcp`, `80/tcp`, `8082/tcp` đều ở trạng thái `ALLOW IN Anywhere`.
- **Cổng cơ sở dữ liệu:** Cổng `3306/tcp` (MySQL) không có trong danh sách cho phép và bị chặn hoàn toàn từ Internet theo chính sách `default deny incoming`.
