# 20110494: Nguyễn Đình Quang Huy
# Lab 08: Firewall
# 1: Setup rules on router to block all access into it except ping

## 1.1 Set up container 
Lệnh chạy: docker-compose up -d

![image](https://github.com/user-attachments/assets/e11ed0df-eb7d-4e42-a33b-4ff90ec46112)

Sau đó kiểm tra container của docker

![image](https://github.com/user-attachments/assets/da883250-54a1-41a7-854d-35ddb6461fd1)

Tiến hành chặn tất cả các truy cập vào router ngoại trừ ping:

Vào container router và run nó: docker exec -it router /bin/bash

iptables -A INPUT -p icmp --icmp-type 8 -j ACCEPT
iptables -A INPUT -p icmp --icmp-type 0 -j ACCEPT
iptables -A INPUT -p tcp --dport 23 -j DROP
iptables -A INPUT -p tcp --dport 80 -j DROP
iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
iptables -A INPUT -j DROP

![image](https://github.com/user-attachments/assets/ef5bb450-ca6c-4a32-87f1-0f6886f6b752)

# 2: Setup rules on router to prevent computers on subnet 10.9.0.0/24 from accessing the internal web server
(iweb).

iptables -A FORWARD -s 10.9.0.0/24 -d 172.16.10.110 -j REJECT


# 3: The badsite was found to contain malwares and source of delivering bots. Setup rules on router to stop
computers on subnet 172.16.10.0/24 from accessing the badsite.

iptables -A FORWARD -s 172.16.10.0/24 -d 10.9.0.10 -j REJECT


# 4 Kiểm tra:

## 4.1 Kiểm tra truy cập vào web server iweb từ container outsider

Kết quả: Không thể kết nối

![image](https://github.com/user-attachments/assets/4f9717e3-e851-453c-960b-86d8f0ec47ba)

## 4.2 Kiểm tra telnet từ container outsider tới router

Kết quả: Không thể kết nối

![image](https://github.com/user-attachments/assets/fa09e49d-3bed-4a57-9911-a8b8942eeafe)


## 4.3 Kiểm tra ping từ container inner tới container trong subnet khác

Kết quả : Có phản hồi từ máy tính trong subnet khác.

![image](https://github.com/user-attachments/assets/811fbbd6-9aeb-47d8-8cab-3dca9b2787d3)

## 4.4 Kiểm tra curl từ container inner tới web server badsite

![image](https://github.com/user-attachments/assets/e9102c53-1691-4fd0-8a50-05840673bcb2)


