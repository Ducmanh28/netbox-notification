# Hướng dẫn sử dụng Warning Tools
Cách cài đặt và sử dụng Warning Tools
## Giới thiệu
`Notify Tools` là một chương trình được viết bằng ngôn ngữ Python. Nó sẽ kết hợp với Telegram, NetBox để gửi cảnh báo khi có sự thay đổi ở trên NetBox

Chương trình hoạt động thông qua cấu hình Event Rules và WebHooks của NetBox, để gửi cảnh báo về Telegram. Đồng thời cũng sẽ ghi lại vào Journal của đối tượng bị thay đổi

**Chức năng**:
- Gửi cảnh báo khi Thêm thiết bị (Ghi journal) 
- Gửi cảnh báo khi Cập nhật thiết bị (Ghi journal)
- Gửi cảnh báo khi Xóa thiết bị
- Tương tự đối với VM

**Mô hình hoạt động**

![](/Notify/Picture/Screenshot_1034.png)

Giải thích:
- Khi có sự thay đổi dữ liệu trên NetBox, cảnh báo sẽ được gửi tới Server Run Bot như đã cấu hình ở WebHook và EventRule trong NetBox.
- Dữ liệu sẽ được xử lý ở Server Run Bot và sẽ được gửi tới Telegram thông qua Internet
- Telegram sẽ gửi tin nhắn tới người dùng thông qua Internet
## Yêu cầu cần có
- NetBox phiên bản `3.7` trở lên
  - Có cấu hình ***Event Rules***
  - Có cấu hình ***WebHooks***
- Server Linux (Ubuntu/CentOS)
  - Server có khả năng kết nối tới NetBox
  - Server này sẽ đồng thời đóng vai trò làm ***Webhooks***
  - Chương trình sẽ được chạy trên Server này

## Cài đặt
Tải xuống chương trình bằng câu lệnh sau:
- File `warning.py`
```
curl -O https://raw.githubusercontent.com/Ducmanh28/Thuc-Tap/refs/heads/main/Linux/06.NetBox/Tools/Warning/warning.py
```
- File `config.py`
```
curl -O https://raw.githubusercontent.com/Ducmanh28/Thuc-Tap/refs/heads/main/Linux/06.NetBox/Tools/Warning/config.py
```

## Cấu hình
### Cấu hình file `config`

Sẽ có 5 thông tin cần nhập vào:
- **Bot Token** - Mã Token bot Telegram của bạn
- **Chat ID** - Chat ID Telegram của bạn
- **Webhooks Port** - Cổng Port mà Webhooks của bạn đang lắng nghe
- **URL NetBox** - Địa chỉ NetBox
- **Token NetBox** - Token của NetBox

Ví dụ: Thay các giá trị bên dưới bằng thông tin của bạn

- **TELEGRAM_BOT_TOKEN** = "73e:2381asdhasufh12eh17dh17d"
- **TELEGRAM_CHAT_ID** = "12345"  
- **WEBHOOKS_PORTS** = "5000"
- **URL_NETBOX** = "https:netboxlab.local"
- **TOKEN_NETBOX** = "asc7asdf8adf7asdf8adfa98sdf9s8f"
### Cấu hình WebHooks
Để cấu hình **WebHooks Local** trên NetBox, bạn cần truy cập vào: **Operation --> Integration --> Webhooks**

Chỉnh sửa URL WebHooks trên NetBox của bạn thành như sau:
```
http://<ip>:<webhooks_ports>/webhooks
```
- Thay `<ip>`: Bằng địa chỉ IP của bạn
- Thay `<webhooks_ports>`: Bằng cổng webhooks của bạn
- Ví dụ: `http://172.16.66.82:5000/webhooks` là 1 URL đúng

Mẫu cấu hình như sau:

![](/Notify/Picture/Screenshot_1035.png)

### Cấu hình Event Rules
Chương trình hiện tại có thể làm việc với 3 loại Event
- **Create** - Tạo 1 object mới
- **Update** - Chỉnh sửa 1 object nào đó
- **Delete** - Xóa 1 object nào đó

Để cấu hình Event Rule, bạn cần truy cập vào: **Operation --> Integration --> Event Rules**

Mẫu cấu hình như sau:

![](/Notify/Picture/Screenshot_1036.png)
### Cấu hình Server
Đảm bảo Server của bạn đáp ứng các yêu cầu sau
- Thiết lập môi trường ảo với python3:
```
python3 -m venv venv
source venv/bin/activate
```
- Cài đặt các mục cần thiết sử dụng `pip install`
```
pip install asyncio
pip install logging
pip install flask
pip install telegram
pip install requests
pip install urlib3
```
- Mở cổng webhooks (ví dụ cổng 5000)
```
# Nếu bạn dùng ufw
sudo ufw allow 5000/tcp

# Hoặc iptables
sudo iptables -A INPUT -p tcp --dport 5000 -j ACCEPT

# Hay firewalld
sudo firewall-cmd --zone=public --add-port=5000/tcp --permanent
```
## Khởi chạy
Truy cập vào nơi lưu file code
```
cd App
python3 main.py
```
Mẫu khởi chạy thành công:

![](/Notify/Picture/Screenshot_1009.png)

Hình ảnh mẫu kết quả trên Telegram:

![](/Notify//Picture/Screenshot_1020.png)

Hình ảnh mẫu journal được tạo:

![](/Notify/Picture/Screenshot_1012.png)

