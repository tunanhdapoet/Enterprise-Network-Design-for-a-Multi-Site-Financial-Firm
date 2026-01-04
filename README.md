# Thiết Kế Hệ Thống Mạng Doanh Nghiệp - FastPay Financial

## 1. Tổng Quan Dự Án (Project Overview)
[cite_start]Dự án thiết kế hạ tầng mạng cho **Công ty tài chính FastPay** có trụ sở chính tại Quận 1 (TP.HCM) và 2 chi nhánh tại Hà Nội và Đà Nẵng. [cite_start]Hệ thống được thiết kế để đáp ứng các yêu cầu khắt khe về bảo mật, độ trễ thấp cho giao dịch trực tuyến và khả năng mở rộng linh hoạt theo mô hình **Top-Down** 3 lớp (Core - Distribution - Access)[cite: 701].

### Yêu cầu chính:
* [cite_start]**Bảo mật:** Zero Trust, IDS/IPS, Firewall Layer 7, tách biệt mạng nội bộ[cite: 649].
* [cite_start]**Kết nối:** VPN Site-to-Site kết nối các chi nhánh với Data Center[cite: 654].
* [cite_start]**Sẵn sàng cao (HA):** Đảm bảo hệ thống luôn hoạt động ổn định với cơ chế dự phòng[cite: 687].
* [cite_start]**Giám sát:** Theo dõi hiệu suất toàn hệ thống[cite: 655].

## 2. Mô Hình Mạng (Network Topology)
Sơ đồ vật lý và logic của toàn bộ hệ thống mạng FastPay:

![Network Topology](D:\UIT\pic\tkm\topology.png)

> *Lưu ý: Sơ đồ bao gồm thiết kế 3 lớp, kết nối VPN giữa các Site và phân hoạch vùng DMZ/Server farm.*

## 3. Các Tính Năng Kỹ Thuật (Key Features)

### 3.1. [cite_start]Phân hoạch VLAN & IP [cite: 658-671]
Hệ thống được chia thành các VLAN chức năng để tối ưu hóa quản lý và bảo mật:

| VLAN ID | Tên | Chức năng |
|---------|-----|-----------|
| **10** | Kế toán | Nhóm quản lý tài chính |
| **20** | RiskMgmt | Quản lý rủi ro, kiểm thử |
| **30** | IT | Nhóm phát triển phần mềm (Dev) |
| **40** | Wi-Fi Guest | Mạng khách (Internet only) |
| **50** | Wi-Fi Company | Mạng nội bộ không dây |
| **60** | Management | Quản lý thiết bị hạ tầng |

### 3.2. Định tuyến & Kết nối (Routing & Connectivity)
* [cite_start]**Inter-VLAN Routing:** Cấu hình trên Switch Layer 3/Router để các phòng ban giao tiếp theo chính sách[cite: 674].
* [cite_start]**Giao thức định tuyến:** Sử dụng **OSPF** (Open Shortest Path First) để cập nhật bảng định tuyến tự động và tìm đường đi ngắn nhất[cite: 797].
* [cite_start]**VPN Site-to-Site:** Sử dụng **GRE Tunnel kết hợp IPsec** để kết nối an toàn giữa HQ (HCM) và các chi nhánh (HN, ĐN) qua môi trường Internet[cite: 682, 771].
* [cite_start]**Internet Access:** Cấu hình **NAT** trên Core Router để ẩn IP nội bộ và tiết kiệm địa chỉ Public IP[cite: 810].

### 3.3. Tính Sẵn Sàng Cao (High Availability)
* Triển khai giao thức **HSRP (Hot Standby Router Protocol)** tại các Gateway.
* Cơ chế: Một Router đóng vai trò *Active*, Router còn lại là *Standby*. [cite_start]Nếu Router Active gặp sự cố, Router Standby sẽ tự động thay thế, đảm bảo người dùng không bị mất kết nối[cite: 687, 764].

### 3.4. Bảo Mật & Quản Lý (Security & Management)
* [cite_start]**Access Control Lists (ACL):** Áp dụng trên Interface VLAN để chặn truy cập trái phép giữa các phòng ban (VD: Guest không được vào mạng Kế toán)[cite: 818].
* [cite_start]**Firewall & IDPS:** Quản lý tập trung thiết bị IDPS bằng **Cisco FMC (Firepower Management Center)** để cấu hình chính sách IPS, lọc ứng dụng và chống mã độc[cite: 828].
* [cite_start]**DHCP:** Tự động cấp phát IP cho người dùng cuối để tránh xung đột[cite: 790].

## 4. Giám Sát Hệ Thống (Monitoring)
[cite_start]Hệ thống sử dụng **Zabbix** kết hợp giao thức **SNMP** để giám sát trạng thái thiết bị mạng[cite: 838].
* [cite_start]**Cấu hình:** SNMP Agent được bật trên Router/Switch Cisco[cite: 840].
* [cite_start]**Tính năng:** Thu thập thông tin CPU, RAM, Bandwidth và gửi cảnh báo (SNMP Trap) khi có sự cố[cite: 864].

## [cite_start]5. Thiết Bị Phần Cứng (Hardware) [cite: 833]
* **Core Switch:** Cisco Catalyst 9500-48Y4C (Layer 3, 100G Support).
* **Distribution Switch:** Cisco Catalyst 9300X-48HX.
* **Router:** Cisco ASR 1002-HX (Hiệu suất cao cho VPN/Routing).
* **Firewall:** FortiGate 200F.
* **Server:** Dell PowerEdge R750 (Chạy ảo hóa/Dịch vụ).

## 6. Kết Luận
Hệ thống mạng FastPay đã được thiết kế hoàn chỉnh với khả năng bảo mật cao (Zero Trust, Firewall L7), kết nối thông suốt giữa 3 miền Bắc - Trung - Nam thông qua VPN và đảm bảo tính sẵn sàng cao với HSRP. [cite_start]Hệ thống giám sát Zabbix giúp quản trị viên chủ động phát hiện sự cố [cite: 902-907].