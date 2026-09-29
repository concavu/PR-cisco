# 🌐 Enterprise Network Infrastructure Design & Deployment

##  1. Tổng quan Dự án (Project Overview)
Dự án thiết kế và triển khai mô hình hạ tầng mạng Doanh nghiệp đa phân vùng (Multi-zone Enterprise Network) theo chuẩn thiết kế phân tầng của Cisco (Hierarchical Network Architecture). Hệ thống đảm bảo đầy đủ các tiêu chuẩn về **Dự phòng đường truyền (High Availability)**, **Định tuyến động (Dynamic Routing)**, **Bảo mật phân đoạn (DMZ & Extended ACL Firewall)** và **Kết nối chi nhánh từ xa qua WAN**.

* **Công cụ mô phỏng:** Cisco Packet Tracer
* **Thiết bị triển khai:** Cisco Catalyst 3560 (Core Switch L3), 2960 (Access Switches), Cisco 1941 Routers, Wireless Access Point, Servers (Web, DNS, DHCP).
* **Vai trò:** Network Architect & Deployment Engineer.

---

##  2. Kiến trúc & Phân vùng Mạng (Network Topology & Zoning)

Mô hình được chia làm 4 phân vùng độc lập nhằm tối ưu hóa hiệu năng và bảo mật:
1. **CORE & EDGE LAYER (Vùng Trung tâm & Biên):** Xử lý định tuyến Inter-VLAN tốc độ cao trên Layer 3 Switch (`SW-CORE`) và giao tiếp OSPF với Router biên (`R-EDGE`).
2. **SERVER FARM / DMZ (Vùng Máy chủ Tập trung):** Thuộc VLAN 40, chứa Web Server, DNS Server và DHCP Server.
3. **LAN ACCESS LAYER (Mạng Nội bộ HQ):** Phân chia VLANs độc lập cho các phòng ban (IT - VLAN 10, Sales - VLAN 20, HR - VLAN 30), trang bị kết nối Có dây & Không dây (WLAN).
4. **WAN & BRANCH OFFICE (Mạng Chi nhánh từ xa):** Kết nối mạng Chi nhánh (`192.168.1.0/24`) về HQ thông qua môi trường WAN Internet (`200.1.1.0/24`).

---

##  3. Các Tính năng Kỹ thuật Trọng tâm (Technical Highlights)

* **Phân đoạn Mạng & Trunking (VLANs & 802.1Q):** Phân chia dải mạng cách ly giữa các phòng ban, đóng gói đường truyền Trunking 802.1Q trên tất cả các kết nối Inter-switch.
* **Độ tin cậy & Dự phòng (EtherChannel LACP):** Cấu hình EtherChannel (chế độ `LACP Active`) kẹp đôi các cổng vật lý (`Fa0/23-24` và `Fa0/21-22`) giữa Core và Access, tăng gấp đôi băng thông và loại bỏ điểm nghẽn đơn lẻ (No Single Point of Failure).
* **Định tuyến & Dịch vụ Mạng (Core Services):**
  * **Inter-VLAN Routing:** Khai báo SVI (Switch Virtual Interfaces) trên Core Switch L3 làm Default Gateway.
  * **Dynamic Routing:** Triển khai OSPF Area 0 kết nối thông suốt toàn mạng HQ và Chi nhánh từ xa.
  * **DHCP Relay:** Cấu hình `ip helper-address` giúp các VLAN tự động nhận IP từ máy chủ DHCP tập trung thuộc vùng DMZ.
  * **NAT Overload (PAT):** Dịch địa chỉ IP nội bộ (`172.16.0.0/16`) thành IP Public (`200.1.1.1`) khi đi ra WAN Internet.
* **Tường lửa & Bảo mật (Extended ACL Firewall):** 
  * Triển khai Extended Access Control List dán tại SVI VLAN 20 (`SW-CORE`).
  * **Chính sách:** Chặn hoàn toàn lưu lượng ICMP (Ping) từ phòng Sales đến Web Server DMZ (`172.16.40.10`), nhưng vẫn duy trì kết nối dịch vụ Web (HTTP - Port 80).

---

## 📸 4. Bằng chứng Vận hành System Verification (Evidence Set)

### 📷  1: Sơ đồ Tổng thể Hạ tầng Mạng Enterprise
 <img width="692" height="498" alt="image" src="https://github.com/user-attachments/assets/6631c8cf-15db-4bd3-b726-8fc8f6bf8fb0" />

*Sơ đồ kiến trúc 4 phân vùng chuẩn màu, trạng thái kết nối chuyển hoàn toàn sang Forwarding (đèn xanh).*

### 📷  2: Kiểm tra Dự phòng EtherChannel (LACP)
<img width="430" height="144" alt="image" src="https://github.com/user-attachments/assets/c36eaa77-3dd7-4c12-a6e2-4f1ca5cb4e72" />

*Trạng thái `Po1(SU)` và `Po2(SU)` đàm phán thành công ở chế độ `(P)` trên Core Switch.*

### 📷  3: Bảng Chuyển đổi Địa chỉ NAT Overload & Định tuyến OSPF
<img width="626" height="121" alt="image" src="https://github.com/user-attachments/assets/39482d40-e2a4-4faa-8673-95037b7cfbff" />

*Bảng NAT Translation ghi nhận luồng chuyển đổi IP nội bộ ra WAN Internet khi PC truy cập mạng ngoài.*

### 📷  4: Bằng chứng Firewall ACL (Chặn Ping - Mở Web)
<img width="523" height="205" alt="image" src="https://github.com/user-attachments/assets/2fbd1f13-9f8c-41f4-9f41-105b57800192" />

<img width="524" height="216" alt="image" src="https://github.com/user-attachments/assets/b2c695ce-2652-4c5d-9c9e-e5a58c7d56d8" />

*Minh chứng kiểm thử trên `PC-Sales`: Lệnh `ping` bị cấm hoàn toàn (100% loss) trong khi Trình duyệt Web vẫn truy cập trang chủ 172.16.40.10 thành công.*

---

## 📁 5. Tài nguyên Dự án (Project Files)
* File mô phỏng Cisco Packet Tracer: [`Enterprise_Network_Design.pkt`](./Enterprise_Network_Design.pkt)
