# 🌐 Thiết Kế Hệ Thống Mạng Doanh Nghiệp - FastPay Financial

![Cisco](https://img.shields.io/badge/Network-Cisco-1BA0D7?logo=cisco)
![Firewall](https://img.shields.io/badge/Security-Fortinet%20%26%20Cisco%20FMC-red)
![Monitoring](https://img.shields.io/badge/Monitoring-Zabbix-d02128?logo=zabbix)
![VPN](https://img.shields.io/badge/Connectivity-IPSec%20VPN-green)

Dự án thiết kế hạ tầng mạng an toàn, hiệu năng cao cho **Công ty tài chính FastPay**. Hệ thống kết nối trụ sở chính tại **TP.HCM** với hai chi nhánh **Hà Nội** và **Đà Nẵng**, đảm bảo tính sẵn sàng cao (High Availability), bảo mật đa lớp và khả năng giám sát tập trung.


## 🏗️ 1. Tổng Quan Kiến Trúc (Architecture)

Hệ thống được thiết kế theo mô hình phân cấp **3 lớp (Core - Distribution - Access)** kết hợp với kiến trúc **Top-Down**, tập trung vào bảo mật và hiệu suất ứng dụng.

### 🎯 Mục tiêu kỹ thuật
* **Tính sẵn sàng (High Availability):** Đảm bảo Uptime 99.9% với HSRP và đường truyền dự phòng.
* **Bảo mật (Security):** Phân đoạn mạng (Network Segmentation), kiểm soát truy cập (ACLs), và hệ thống phát hiện xâm nhập (IPS).
* **Kết nối (Connectivity):** Kết nối liên chi nhánh (Site-to-Site) an toàn qua Internet.
* **Giám sát (Monitoring):** Theo dõi trạng thái thiết bị thực gian thực.

### 🗺️ Sơ đồ mạng (Topology)
![Network Topology](https://github.com/user-attachments/assets/685c4333-532b-4d39-b8f3-f2861158666b)

---

## 🛠️ 2. Chi Tiết Triển Khai (Implementation)

### 2.1. Phân hoạch VLAN (VLAN Segmentation)
Hệ thống chia tách các phòng ban thành các mạng LAN ảo riêng biệt để tối ưu broadcast domain và tăng cường bảo mật.

| VLAN ID | Tên (Name) | Đối tượng sử dụng | Subnet |
|:---:|:---|:---|:---|
| **10** | KeToan | Phòng Kế toán - Tài chính | 192.168.10.0/24 |
| **20** | RiskMgmt | Phòng Quản lý rủi ro | 192.168.20.0/24 |
| **30** | IT | Phòng Kỹ thuật & Dev | 192.168.30.0/24 |
| **40** | WiFi_Guest | Khách vãng lai (Internet only) | 192.168.40.0/24 |
| **50** | WiFi_Staff | Nhân viên nội bộ (Wireless) | 192.168.50.0/24 |
| **99** | Management | Quản lý thiết bị hạ tầng | 192.168.99.0/24 |

### 2.2. Định tuyến & Kết nối (Routing & Connectivity)
* **OSPF (Open Shortest Path First):** Sử dụng OSPF Area 0 cho vùng Backbone để quảng bá các mạng con giữa các Router, đảm bảo hội tụ nhanh khi có thay đổi topo.
* **GRE over IPsec VPN:** Thiết lập đường hầm GRE để hỗ trợ định tuyến Multicast/OSPF, được mã hóa bởi IPsec để bảo vệ dữ liệu khi truyền qua Internet giữa HQ (HCM) và Chi nhánh (HN, ĐN).
* **NAT/PAT:** Cấu hình NAT Overload để cho phép toàn bộ nhân viên truy cập Internet thông qua một địa chỉ IP Public duy nhất.

### 2.3. Tính Sẵn Sàng Cao (High Availability)
* **Giao thức HSRP:** Cấu hình Active/Standby Router tại Gateway.
    * IP ảo (Virtual IP) được sử dụng làm Gateway cho các VLAN.
    * Đảm bảo khi Router chính gặp sự cố, Router phụ sẽ tiếp quản ngay lập tức mà không làm gián đoạn kết nối người dùng.

### 2.4. Bảo Mật Chuyên Sâu (Advanced Security)
* **Access Control Lists (ACL):**
    * Chặn VLAN Guest truy cập vào các VLAN nội bộ (Kế toán, IT).
    * Chỉ cho phép VLAN Management truy cập SSH vào thiết bị mạng.
* **Cisco Firepower (FMC & FTD):**
    * Triển khai IPS/IDS để phát hiện các mẫu tấn công mạng.
    * Cấu hình Policy để kiểm soát ứng dụng và lọc URL độc hại.
* **Port Security:** Giới hạn số lượng MAC address trên mỗi port switch để chống tấn công MAC Flooding.

---

## 📊 3. Giám Sát Hệ Thống (Monitoring)

Hệ thống sử dụng **Zabbix** kết hợp với giao thức **SNMP** để giám sát toàn diện:
* **Dashboard:** Trực quan hóa sơ đồ mạng và trạng thái thiết bị (Up/Down).
* **Metrics:** Thu thập thông số CPU, RAM, Băng thông (Traffic In/Out) của Router và Switch.
* **Alerting:** Gửi cảnh báo qua Email/Telegram khi phát hiện thiết bị quá tải hoặc mất kết nối VPN.

---

## 💻 4. Môi Trường Giả Lập & Phần Cứng (Environment)

### Công cụ mô phỏng
* **EVE-NG / GNS3:** Giả lập Router, Switch, Firewall.
* **VMware Workstation:** Chạy các máy ảo Windows/Linux đóng vai trò Client và Server.

### Thiết bị đề xuất (Proposed Hardware)
| Thiết bị | Model Đề Xuất | Vai trò |
|:---|:---|:---|
| **Core Switch** | Cisco Catalyst 9500 | Chuyển mạch lớp 3 hiệu năng cao |
| **Distribution** | Cisco Catalyst 9300 | Kết nối các tầng truy cập |
| **Router** | Cisco ASR 1000 Series | Định tuyến biên & VPN Gateway |
| **Firewall** | FortiGate 200F / Cisco FTD | Tường lửa thế hệ mới (NGFW) |
| **Server** | Dell PowerEdge R750 | Chạy Ảo hóa & Dịch vụ (Web, DB) |

---

## 🚀 5. Hướng Phát Triển (Future Work)
* **Nâng cấp FMC:** Tích hợp đầy đủ tính năng SIEM để phân tích log tập trung.
* **Mở rộng Monitoring:** Triển khai thêm Zabbix Proxy tại các chi nhánh Hà Nội, Đà Nẵng để giảm tải cho Server trung tâm.
* **Tối ưu Wi-Fi:** Áp dụng QoS cho VLAN Guest để kiểm soát băng thông.
* **AI/ML Integration:** Ứng dụng AI vào hệ thống IPS để tự động phản ứng với các mối đe dọa Zero-day.
