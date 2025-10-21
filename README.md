# Write-up-SQL-injection
# 🔒 SQL Injection Write-up

<div align="center">

![SQL Injection](https://img.shields.io/badge/SQL-Injection-red?style=for-the-badge&logo=mysql&logoColor=white)
![Security](https://img.shields.io/badge/Security-Penetration%20Testing-orange?style=for-the-badge&logo=shield&logoColor=white)
![Database](https://img.shields.io/badge/Database-MySQL-blue?style=for-the-badge&logo=mysql&logoColor=white)

</div>

---


## 📊 Tổng kết và Khuyến nghị

### 🚨 Các lỗ hổng được phát hiện

| Lỗ hổng | Mức độ | Mô tả | Câu |
|---------|--------|-------|-----|
| 🔴 **SQL Injection (UNION)** | **Nghiêm trọng** | Hệ thống không có cơ chế bảo vệ chống SQL injection | 4, 5, 9 |
| 🔴 **Blind SQL Injection** | **Nghiêm trọng** | Khai thác SQL injection qua cookie TrackingId | 13 |
| 🟡 **Thiếu Input Validation** | **Trung bình** | Không kiểm tra và làm sạch dữ liệu đầu vào | Tất cả |
| 🔴 **Thông tin nhạy cảm** | **Nghiêm trọng** | Mật khẩu được lưu trữ dưới dạng plain text | 5, 9, 13 |
| 🟡 **Cookie Security** | **Trung bình** | Cookie không được bảo vệ khỏi SQL injection | 13 |

### 📋 Tóm tắt các cuộc tấn công

| Câu | Phương pháp | Mục tiêu | Kết quả |
|-----|-------------|----------|---------|
| **4** | UNION SQL Injection | Lấy thông tin phiên bản DB | ✅ Thành công |
| **5** | UNION SQL Injection | Lấy thông tin từ bảng users_ifhciw | ✅ Lấy được admin account |
| **9** | UNION SQL Injection | Lấy thông tin từ bảng users | ✅ Lấy được admin account |
| **13** | Blind SQL Injection | Khai thác qua cookie TrackingId | ✅ Lấy được admin account |

### 🛡️ Khuyến nghị bảo mật

#### 🔧 Biện pháp kỹ thuật
- ✅ **Sử dụng Prepared Statements**: Thay thế string concatenation bằng prepared statements
- ✅ **Input Validation**: Kiểm tra và làm sạch tất cả dữ liệu đầu vào (form inputs, cookies, headers)
- ✅ **Mã hóa mật khẩu**: Sử dụng hash functions như bcrypt để mã hóa mật khẩu
- ✅ **Principle of Least Privilege**: Giới hạn quyền truy cập database
- ✅ **Error Handling**: Không hiển thị thông tin lỗi chi tiết cho người dùng
- ✅ **Cookie Security**: Validate và sanitize tất cả cookie values
- ✅ **Web Application Firewall**: Triển khai WAF để phát hiện và chặn các cuộc tấn công SQL injection
- ✅ **Database Monitoring**: Giám sát các truy vấn SQL bất thường

#### 📋 Checklist bảo mật
- [ ] Kiểm tra tất cả input parameters (form, URL, cookies)
- [ ] Implement parameterized queries cho tất cả database operations
- [ ] Mã hóa tất cả mật khẩu với salt và hash mạnh
- [ ] Giới hạn quyền database user (chỉ cần thiết)
- [ ] Cấu hình error handling không leak thông tin
- [ ] Validate và sanitize cookie values
- [ ] Triển khai WAF với rules chống SQL injection
- [ ] Thực hiện penetration testing định kỳ
- [ ] Code review tập trung vào SQL injection vulnerabilities
- [ ] Implement logging và monitoring cho database access

---

<div align="center">

### 🎓 **Kết luận**

> 💡 **Bài học:** SQL injection là một trong những lỗ hổng bảo mật phổ biến và nguy hiểm nhất trong web application. Việc hiểu rõ cách thức tấn công và phòng chống là vô cùng quan trọng đối với các developer và security professional.

**📅 Ngày thực hiện:** `[21/10/2025]`  
**👨‍💻 Tác giả:** `[vantrwng]`  
**🏷️ Tags:** `#SQLInjection` `#WebSecurity` `#PenetrationTesting` `#DatabaseSecurity`

</div>

