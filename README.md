# reverse-proxy
## Nội dung bài viết.
- [I. Trước tiên chúng ta cần phải biết reverse proxy là gì?](#1)
- [II. II.Cách cấu hình reverse proxy.](#2)

Bài viết này sẽ giúp bạn xây dựng 1 reverse proxy thông qua NGINX

## I.Trước tiên chúng ta cần phải biết reverse proxy là gì?<a name="1"></a>
Một proxy server hoạt động với vai trò trung gian giữa máy khách và máy chủ khác. Proxy server lấy tài nguyên từ máy chủ mà bạn muốn kết nối và gửi nó cho bạn để xem. Một reverse proxy hoạt động theo cùng một cách, ngoại trừ vai trò bị đảo ngược. Khi bạn yêu cầu thông tin từ máy chủ, reverse proxy sẽ giữ yêu cầu và gửi nó đến máy chủ backend thích hợp. Điều này cho phép quản trị viên hệ thống sử dụng máy chủ cho nhiều ứng dụng, cũng như đảm bảo luồng lưu lượng truy cập mượt mà hơn giữa máy khách và máy chủ.

Ưu điểm lớn nhất của việc sử dụng Reverse proxy là khả năng quản lý tập trung. Nó giúp kiểm soát mọi requests do clients gửi lên các servers mà được bảo vệ.
<img src="https://www.maketecheasier.com/assets/uploads/2019/03/reverse-proxy-featured-800x400.png.webp">
## II.Cách cấu hình reverse proxy.<a name="2"></a>
Trong bài viết này mình sẽ hướng dẫn bạn tạo 1 reverse proxy bằng NGINX và sử dụng APACHE để làm web server.
Đầu tiên bạn cần tắt APACHE đi bằng câu lệnh:
```
sudo service apache2 stop
```
Vì Web server của bạn cần phải được bảo ật cẩn thận nên bạn cần phải thực hiện đổi Port cho nó để tránh việc tin tặc có thể mò ra nó.
Đầu tiên bạn cần chỉnh sửa file /etc/apache2/ports.conf (apache hay apache2 đều được nhé tùy vào web server mà bạn sử dụng) tiếp theo tìm đến dòng listen và sửa lại port mà bạn muốn nhé. ví dụ ở đây mình sẽ để là 1234
```
vim /etc/apache2/ports.com
```
```
# If you just change the port or add more ports here, you will likely also
# have to change the VirtualHost statement in
# /etc/apache2/sites-enabled/000-default.conf

Listen 1234

<IfModule ssl_module>
        Listen 443
</IfModule>

<IfModule mod_gnutls.c>
        Listen 443
</IfModule>

# vim: syntax=apache ts=4 sw=4 sts=4 sr noet
```
Tiếp theo bạn thực hiện bật lại APACHE :
```
sudo service apache2 start
```
Như đã nói ở trên reverse proxy giống như 1 người trung gian đứng giữa máy khách và 1 web server và làm nhiệm vụ vận chuyển các tập tin vậy thì các máy khách sẽ chỉ biết được thông tin của reverse proxy hứ ko thể biết được thông tin của web server vậy nên nó bảo mật tuyệt đối.vậy thì tại sao lại chúng ta lại phải thực hiện đổi port đơn giản khi bạn cài APACHE thì nó sẽ đc set ở 1 port mặc định nếu giữ nguyên tin tặc sẽ dễ dàng mò ra được nhưng khi thực hiện đổi port thì lại khác .

Bây giờ chúng ta thực hiện thiết lập reverse proxy bằng NGINX nhé!
Đầu tiên bạn cần nhập lệnh sau để vô hiệu virtual host:
```
sudo unlink /etc/nginx/sites-enabled/default
```
Sau khi vô hiệu virtual host, bạn cần tạo file gọi là reverse-proxy.conf trong thư mục etc/nginx/sites-available để lưu thông tin reverse proxy.

Để làm vậy, truy cập vào thư mục đó trước bằng lệnh cd:
```
cd etc/nginx/sites-available/
```
Tạo file bằng vim editor:
```
vim reverse-proxy.conf
```
Trong file này, chúng ta sẽ dán chuỗi sau:
```
server {
    listen 80;
    location / {
        proxy_pass http://192.x.x.2;
    }
}
```
Lệnh trên sẽ cho phép proxy pass chuyển dữ liệu qua Nginx reverse proxy sang 192.x.x.2:80, là Apache remote socket. Vì vậy, cả 2 web server – Nginx và Apache chia sẽ chung nội dung.

Sau khi hoàn tất, chỉ cần lưu lại file và thoát khỏi vi editor. Bạn có thể thoát bằng cách nhấn :wq.
Để chuyển dữ liệu tới server khác, bạn có thể dùng ngx_http_proxy_module trong terminal.

Giờ, kích hoạt directives bằng cách link tới /sites-enabled/ bằng lệnh sau:
```
sudo ln -s /etc/nginx/sites-available/reverse-proxy.conf /etc/nginx/sites-enabled/reverse-proxy.conf
```
Cuối cùng, chúng ta sẽ kiểm tra cấu hình Nginx và khởi động lại Nginx để kiểm tra hiệu năng. Gõ lệnh sau để xác nhận Nginx đang hoạt động trong Linux terminal:
```
service nginx configtest
```
```
service nginx restart
```
Có nhiều ưu điểm để setup Nginx reverse proxy trong hệ điều hành Linux. Nó sẽ tăng đáng kể tốc độ và tính bảo mật chống lại malware. Nginx reverse proxy là tiến trình cơ bản trong Linux terminal. Mặc dù cũng có nhiều cách khác để cài và cấu hình nó, tùy vào môi trường bạn đang dùng. Nhưng cách thiết lập trên dễ nhất và giúp bạn dựng được ngay Nginx reverse proxy server.
