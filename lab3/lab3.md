Câu 3 SQL injection attack, querying the database type and version on
Oracle

Mô tả bài lab: khai thác lỗ hổng SQL injection ở bộ lọc category để truy
vấn và hiển thị chuỗi phiên bản của hệ quản trị cơ sở dữ liệu (database
version string) trong trường hợp này là Oracle.

Bước 1: Xác định web có lỗ hỏng SQL injection hay không  
Chọn category bất kỳ và thêm ký tự \' vào cuối URL, nếu như có thông báo
lỗi internal server error thì có tồn tại lỗ hỏng SQL injection

![A screenshot of a computer AI-generated content may be
incorrect.](image/image1.png){width="6.260416666666667in"
height="1.78125in"}

Bước 2: Xác định có thể khai thác web bằng SQL injection UNION được
không

Nếu như ta chỉnh sửa URL sao cho category=any_string , nếu nhập URL đã
chỉnh sửa mà trang web hiển thị any_string thì ta xác định có thể khai
thác bằng SQL injection UNION

![A screen shot of a computer AI-generated content may be
incorrect.](image/image2.png){width="6.260416666666667in"
height="2.5729166666666665in"}

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
height="1.65625in"}

Tiếp theo ta sẽ cần xác định kiểu dữ liệu của hai cột có cột nào là
string hay text không, ta có các payload cho MySQL như sau

\' union select \'a\',NULL\-- (kiểm tra lần lượt từng cột)

\' union select NULL,\'a\'\--

\' union select \'a\',\'a\'\--      (kiểm tra cùng lúc toàn bộ cột)

nếu như payload nào trả về lỗi internal server error thì ta xác định
được cột đó không phải kiểu string, nếu không có lỗi trả về thì cột đó
có kiểu dữ liệu là string

Thử payload \' union select \'a\',\'a\'\-- (ta cũng cần phải encode
payload sang \'union+select+\'a\',\'a\'\--) ta thấy rằng kết quả không
được như mong đợi vì ta thấy giao diện liệt kê các sản phảm là string
nhưng dùng truy vấn thì lại bị lỗi, web không sử dụng MySQL làm hệ quản
trị cơ sở dữ liệu. Suy đoán web có thể sử dụng hệ quản trị cơ sở dữ liệu
Oracle

![A screen shot of a computer AI-generated content may be
incorrect.](image/image4.png){width="6.260416666666667in"
height="1.4895833333333333in"}

Ta cần chỉnh sửa lại payload thành của Oracle. Trong Oracle, câu lệnh
SELECT bắt buộc phải có mệnh đề FROM. Tuy nhiên một số truy vấn không
cần bất kỳ bảng nào như truy vấn được dùng làm payload trong lab này.
Oracle đã tạo sẵn cho ta một bảng dummy tên là DUAL dùng cho mệnh đề
FROM. Bằng cách sử dụng bảng DUAL, ta có thể thực hiện các truy vấn chứa
các hàm không liên quan đến bất kỳ bảng nào

\' union select \'a\',\'a\' from DUAL\--  encode
\'+union+select+\'a\',\'a\'+from+DUAL\--

nhập payload vào URL ta thấy được hai dòng \'a\', \'a\' đã được thêm vào
trang

![A screenshot of a computer AI-generated content may be
incorrect.](image/image5.png){width="6.260416666666667in"
height="3.1770833333333335in"}

Bước 4: Xác định payload để xuất ra phiên bản của Oracle

\' union select banner,NULL from v\$version\-- encode
\'+union+select+banner,NULL+from+v\$version\--

Nhập payload vào URL để có được phiên bản của Oracle

![A screenshot of a computer AI-generated content may be
incorrect.](image/image6.png){width="6.260416666666667in"
height="2.46875in"}

![A screenshot of a computer AI-generated content may be
incorrect.](image/image7.png){width="6.260416666666667in"
height="2.84375in"}

Vì sao cái payload này lại trả về được cái phiên bản của Oracle?  
Bởi vì UNION trong SQL cho phép ghép kết quả của hai truy vấn khác nhau
miễn là:

**Hai truy vấn có cùng số cột**, và **Các cột tương thích kiểu dữ liệu**
(ví dụ: string ghép với string).

Và đặc điểm của bảng v\$version là có cột banner kiểu string chứa thông
tin về phiên bản của Oracle đang dùng

Nếu mà viết code truy vấn đầy đủ thì sẽ như sau (giả sử cột chứa tên sản
phẩm là ten_sp và cột chứa mô tả của sản phảm tương ứng là description)

Select ten_sp, description From category

Union

Select banner, NULL From v\$version;

**Cả hai bảng được Select đều có số cột bằng nhau là 2,** ten_sp kiểu
string ghép được với banner kiểu string, description kiểu string ghép
được với kiểu NULL. Kết quả ghép sẽ tạo ra bảng mới mà trong cột ten_sp
sẽ chứa thông tin về phiên bản của Oracle
