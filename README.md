---

##  Bài tập Tuần: Ứng dụng Tổng hợp & Tự Khởi Chạy 

Mục tiêu: Giao tiếp với Device Driver GPIO ở tầng thấp nhất của hệ điều hành, viết ứng dụng điều khiển ngoại vi (LED) và cấu hình ứng dụng tự động chạy nền (daemon) ngay khi hệ điều hành vừa khởi động xong thông qua `BusyBox init`.

###  Các Task đã thực hiện:
1. Tương tác trực tiếp với Device Driver: - Sử dụng lệnh Shell cơ bản (`echo`, `cat`) tương tác với thư mục ảo `/sys/class/gpio/`.
   - Thực hiện `export` GPIO 23 (Chân P8_13), cấu hình chiều (direction) là `out` và thay đổi giá trị (`value`) để test bật/tắt LED cắm ngoài thành công.

![6_1](https://github.com/user-attachments/assets/e3c7e622-c5bb-489a-9b41-09928a538945)

2. Viết chương trình điều khiển (C/C++): - Viết mã nguồn `blink_led.c` thao tác trực tiếp với file `value` của GPIO 23 bằng hàm `fopen`, `fprintf`.
   - Xây dựng vòng lặp vô tận kết hợp hàm `usleep` để tạo hiệu ứng nhấp nháy (chu kỳ 0.5s sáng, 0.5s tắt).

3. Xây dựng Kịch bản Tự khởi chạy (Init Script):
   - Tạo file script chuẩn của hệ thống `S99blinkled`.

   - Kịch bản được thiết kế để tự động `export` GPIO 23 (nếu chưa có), thiết lập `out`, và gọi chương trình `blink_led` chạy ngầm (background process) bằng toán tử `&`.

4. Đóng gói và Tích hợp Buildroot:
   - Tạo package `blink_led` trong Buildroot với `Config.in` và `blink_led.mk`.
   - Sử dụng chỉ thị `$(INSTALL)` để Buildroot tự động biên dịch và phân phối tệp thực thi vào `/usr/bin/`, đồng thời chép file kịch bản khởi động vào đúng thư mục `/etc/init.d/` của RootFS.
![6_3](https://github.com/user-attachments/assets/50fa1348-e122-4496-80df-3340f7cd7bd8)

> Kết quả Nghiệm thu cuối cùng:
> https://github.com/user-attachments/assets/45858351-11d6-45bf-8c01-c81ad9c362cd
> Bo mạch sau khi được cấp nguồn độc lập (không giao tiếp máy tính) đã tự động nạp OS, tiến trình `init` tự động quét thư mục `/etc/init.d/` và kích hoạt thành công ứng dụng nháy LED dưới nền.

