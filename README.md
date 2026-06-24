# AI-Powered SOC Lab: Integrated SIEM/IDS with Machine Learning for Real-Time Threat Detection

[![Security Standard](https://img.shields.io/badge/Framework-MITRE%20ATT%26CK-red.svg)](https://attack.mitre.org/)
[![SIEM](https://img.shields.io/badge/SIEM-Wazuh%20%2F%20Elastic-blue.svg)](https://wazuh.com/)
[![IDS](https://img.shields.io/badge/IDS-Suricata-green.svg)](https://suricata.io/)
[![Python](https://img.shields.io/badge/Language-Python%203.x-yellow.svg)](https://www.python.org/)

## 📝 Tổng Quan Dự Án (Project Overview)
Dự án này xây dựng một môi trường phòng thí nghiệm giám sát an ninh mạng (**SOC Home Lab**) toàn diện. Hệ thống không chỉ thu thập và quản lý log tập trung thông qua nền tảng **SIEM (Wazuh/Elastic Stack)** mà còn tích hợp bộ não trí tuệ nhân tạo (**Machine Learning Pipeline via Python**) để phân tích luồng mạng thời gian thực, phát hiện và tự động ngăn chặn các hành vi xâm nhập nguy hiểm.

Dự án mô phỏng chính xác quy trình xử lý sự cố của một **SOC Analyst Level 1**: Giám sát $\rightarrow$ Phát hiện $\rightarrow$ Điều tra $\rightarrow$ Phản hồi tự động.

---

## 🏗️ Kiến Trúc Hệ Thống (System Architecture)

Hệ thống được triển khai ảo hóa trên nền tảng VMware Workstation/VirtualBox với cấu hình các phân vùng mạng rõ ràng:

1. **Attacker Node (Kali Linux):** Đóng vai trò thực hiện các kỹ thuật tấn công, dò quét (Reconnaissance, DoS/DDoS, Web Attacks, Reverse Shell).
2. **Victim Node (Ubuntu Server / Windows Server):** Đóng vai trò máy chủ dịch vụ của doanh nghiệp (chạy Apache Web Server), được cài đặt **Wazuh Agent** và cấu hình đẩy Event Logs/Syslog tập trung.
3. **SOC & Monitoring Node (Ubuntu Server):** Trung tâm xử lý dữ liệu, bao gồm:
   * **Suricata IDS:** Lắng nghe lưu lượng mạng thô (Promiscuous Mode) và xuất log dạng `eve.json`.
   * **Real-time AI Pipeline (Python):** Đọc log mạng liên tục, trích xuất đặc trưng và đưa vào mô hình Machine Learning để phân loại độc hại.
   * **Wazuh SIEM Manager:** Tiếp nhận log từ Agent, Log từ Suricata và Alert từ mô hình AI để hiển thị lên Dashboard tập trung.