# AI-Powered SOC Lab: Integrated SIEM/IDS with Machine Learning for Real-Time Threat Detection

[![Security Standard](https://img.shields.io/badge/Framework-MITRE%20ATT%26CK-red.svg)](https://attack.mitre.org/)
[![SIEM](https://img.shields.io/badge/SIEM-Wazuh%20%2F%20Elastic-blue.svg)](https://wazuh.com/)
[![IDS](https://img.shields.io/badge/IDS-Suricata-green.svg)](https://suricata.io/)
[![Python](https://img.shields.io/badge/Language-Python%203.x-yellow.svg)](https://www.python.org/)


## 📝 Tổng Quan Dự Án (Project Overview)
Dự án này xây dựng một môi trường phòng thí nghiệm giám sát an ninh mạng (**SOC Home Lab**) toàn diện. Hệ thống không chỉ thu thập và quản lý log tập trung thông qua nền tảng **SIEM (Wazuh/Elastic Stack)** mà còn tích hợp bộ não trí tuệ nhân tạo (**Machine Learning Pipeline via Python**) để phân tích luồng mạng thời gian thực, phát hiện và tự động ngăn chặn các hành vi xâm nhập nguy hiểm.

Dự án mô phỏng chính xác quy trình xử lý sự cố của một **SOC Analyst Level 1**: Giám sát -> Phát hiện -> Điều tra -> Phản hồi tự động.

---

## 🏗️ Kiến Trúc Hệ Thống (System Architecture)

Hệ thống được triển khai ảo hóa trên nền tảng VMware Workstation/VirtualBox với cấu hình các phân vùng mạng rõ ràng:

1. **Attacker Node (Kali Linux):** Đóng vai trò thực hiện các kỹ thuật tấn công, dò quét (Reconnaissance, DoS/DDoS, Web Attacks, Reverse Shell).
2. **Victim Node (Ubuntu Server / Windows Server):** Đóng vai trò máy chủ dịch vụ của doanh nghiệp (chạy Apache Web Server), được cài đặt **Wazuh Agent** và cấu hình đẩy Event Logs/Syslog tập trung.
3. **SOC & Monitoring Node (Ubuntu Server):** Trung tâm xử lý dữ liệu, bao gồm:
   * **Suricata IDS:** Lắng nghe lưu lượng mạng thô (Promiscuous Mode) và xuất log dạng eve.json.
   * **Real-time AI Pipeline (Python):** Đọc log mạng liên tục, trích xuất đặc trưng và đưa vào mô hình Machine Learning để phân loại độc hại.
   * **Wazuh SIEM Manager:** Tiếp nhận log từ Agent, Log từ Suricata và Alert từ mô hình AI để hiển thị lên Dashboard tập trung.

### Sơ đồ luồng dữ liệu:
* [Kali Linux (Attacker)] -> Gửi Traffic tấn công mạng -> [Web Server (Victim)]
* [Web Server (Victim)] -> Cài Wazuh Agent gửi Syslog/Event Log -> [Wazuh SIEM Dashboard]
* [Suricata IDS] -> Quét gói tin thô -> Xuất file log [eve.json] -> [Python AI Script]
* [Python AI Script] -> Dự đoán và đẩy cảnh báo -> [Wazuh SIEM Dashboard]
* [Wazuh SIEM Dashboard] -> Kích hoạt Active Response -> Tự động Block IP Tấn Công.

---

## 🎯 Các Tính Năng Cốt Lõi (Key Features)

* **Giám Sát Tập Trung (Centralized Logging):** Thu thập toàn diện log hệ điều hành, log ứng dụng web và log mạng về một giao diện duy nhất.
* **Phát Hiện Bằng Học Máy (AI Detection):** Mô hình **Random Forest / XGBoost** được huấn luyện trên bộ dữ liệu chuẩn **CICIDS2017** giúp nhận diện các cuộc tấn công thế hệ mới hoặc các hành vi bất thường (Anomaly Detection) với độ trễ dưới 50ms.
* **Đối Chiếu Sự Kiện (Log Correlation):** Liên kết dữ liệu cảnh báo mạng từ IDS với hành vi sinh tiến trình (Process Creation) trên Endpoint để xây dựng bức tranh toàn cảnh về sự cố.
* **Phản Hồi Tự Động (Active Response):** Khi mô hình AI hoặc SIEM kích hoạt cảnh báo mức độ nghiêm trọng cao, hệ thống tự động gọi script cô lập máy chủ hoặc chặn IP của kẻ tấn công ngay tại Firewall (iptables).

---

## 🛠️ Công Nghệ & Công Cụ Sử Dụng (Tech Stack)

* **SIEM/SOAR:** Wazuh Manager & Wazuh Agent / Elastic Stack
* **IDS/IPS:** Suricata Network Intrusion Detection System
* **Data Science & ML:** Python 3, Scikit-learn, Pandas, Joblib, Jupyter Notebook
* **Mô hình sử dụng:** Random Forest, XGBoost
* **Bộ dữ liệu huấn luyện:** CICIDS2017 / NSL-KDD
* **Môi trường:** VMware Workstation, Kali Linux, Ubuntu Server

---

## 🛡️ Kịch Bản Thực Nghiệm & Điều Tra (SOC Use Cases)

Dự án giả lập thành công các kỹ thuật tấn công trong khung **MITRE ATT&CK** và quy trình điều tra của SOC Analyst:

### 1. Tấn công Từ chối dịch vụ (DoS/DDoS)
* **Tấn công:** Dùng công cụ hping3 từ Kali Linux nhắm vào máy Victim.
* **Phát hiện:** Tốc độ gói tin tăng vọt kích hoạt bộ lọc của Suricata. Đồng thời script AI phân tích dữ liệu luồng (Flow), nhận diện nhãn DoS và đẩy cảnh báo đỏ về Wazuh SIEM.
* **Phản hồi:** Wazuh kích hoạt Active Response, thực thi lệnh block IP Kali Linux trong 10 phút.

### 2. Dò quét hệ thống & Tấn công ứng dụng Web (SQL Injection)
* **Tấn công:** Sử dụng Nmap quét cổng dịch vụ và dùng Sqlmap để khai thoát lỗ hổng cơ sở dữ liệu trên Web Server.
* **Phân tích log:** SOC Analyst thực hiện điều tra trên Dashboard: Bóc tách thành công địa chỉ IP nguồn, công cụ tấn công (User-Agent: sqlmap), các câu lệnh truy vấn SQL độc hại độc và mã phản hồi HTTP 200 OK (xác định tấn công thành công).

---

## 🚀 Hướng Dẫn Triển Khai (Deployment Guide)

### Bước 1: Huấn luyện mô hình Machine Learning
Di chuyển vào thư mục /ml_module, chạy Jupyter Notebook để làm sạch dữ liệu và huấn luyện mô hình:
* Lệnh di chuyển: `cd ml_module`
* Lệnh chạy notebook: `jupyter notebook train_model.ipynb`
* Kết quả sẽ xuất ra file `network_model.pkl`.

### Bước 2: Cấu hình Suricata & Wazuh Agent
Cấu hình Suricata lắng nghe trên card mạng nội bộ và đảm bảo file log eve.json được sinh ra ổn định tại thư mục `/var/log/suricata/`.

Thêm cấu hình khai báo file log vào file cấu hình `ossec.conf` của Wazuh Agent để hệ thống tự động đẩy log Suricata về SIEM.

### Bước 3: Khởi chạy Real-time AI Pipeline
Chạy script Python ngầm trên Monitoring Node để quét log và đưa vào mô hình dự đoán theo thời gian thực bằng lệnh:
`python3 src/app_detection_pipeline.py --model models/network_model.pkl --log /var/log/suricata/eve.json`

---

## 📊 Kết Quả Đạt Được (Key Metrics)
* **Độ chính xác mô hình (Accuracy):** Đạt **98.5%** trên tập kiểm thử (Test set) của CICIDS2017 đối với các nhãn DoS và PortScan.
* **Thời gian phản hồi (Latency):** Thời gian từ lúc nhận gói tin độc hại cho đến khi mô hình AI đưa ra cảnh báo và đẩy về SIEM trung bình là **~35ms**, đáp ứng yêu cầu vận hành thời gian thực.
* **Tỷ lệ giảm nhiễu (False Positive Reduction):** Giảm thiểu **20%** cảnh báo sai so với việc chỉ sử dụng các rule ký tự (Signature-based) truyền thống của IDS.