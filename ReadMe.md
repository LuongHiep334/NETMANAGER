# NET-MANAGER

Ứng dụng desktop quản lý phòng máy (quán net), viết bằng **Java Swing** và **SQL Server**. Ứng dụng hỗ trợ theo dõi sơ đồ máy trạm, mở/tắt máy, tính tiền theo giờ chơi, gọi món F&B, thanh toán và xem báo cáo doanh thu.

> Đây là dự án nhóm thực hiện trong quá trình học Cao đẳng Công nghệ thông tin (Kỹ thuật phần mềm) tại FPT Polytechnic Thái Nguyên.

## Chức năng

**Đăng nhập và tài khoản**
- Đăng nhập bằng tên tài khoản và mật khẩu, có tùy chọn ghi nhớ tài khoản.
- Tài khoản bị tạm dừng sẽ không đăng nhập được.
- Đổi mật khẩu, quên mật khẩu (đặt lại mật khẩu mới theo tên tài khoản).
- Hai vai trò: **Quản lý** và **Nhân viên**.

**Vận hành phòng máy**
- Sơ đồ máy trạm chia theo khu: dàn máy thường, VIP, máy thi đấu.
- Mở máy, tắt máy, theo dõi danh sách phiên sử dụng máy.
- Máy đang bảo trì hoặc đang có phiên sử dụng sẽ không cho mở tiếp.
- Gọi món ăn, đồ uống theo từng phiên máy.

**Thanh toán**
- Tính tiền máy theo giá mỗi giờ và thời gian chơi, cộng thêm tiền dịch vụ F&B.
- Hóa đơn thanh toán hiển thị chi tiết giờ vào, giờ ra, tiền máy, tiền món, tiền khách đưa và tiền thừa.

**Quản lý (dành cho Quản lý)**
- Quản lý máy tính (thêm, sửa, xóa, trạng thái máy).
- Quản lý nhân viên.
- Quản lý thực đơn món ăn.
- Thống kê doanh thu theo khoảng ngày, có biểu đồ (JFreeChart).

## Công nghệ sử dụng

| Thành phần | Công nghệ |
|---|---|
| Ngôn ngữ | Java (JDK 21 trở lên) |
| Giao diện | Java Swing, FlatLaf 3.4.1, NetBeans AbsoluteLayout |
| Chọn ngày | JDatePicker 1.3.4, JCalendar 1.4 |
| Biểu đồ | JFreeChart 1.5.4 |
| Cơ sở dữ liệu | Microsoft SQL Server, driver `mssql-jdbc` 12.8.1 |
| Quản lý build | Apache Maven |
| Hỗ trợ | Lombok 1.18.38 (chỉ dùng khi biên dịch) |

## Cấu trúc mã nguồn

Dự án chia theo các gói (package), áp dụng mô hình DAO:

| Gói | Vai trò |
|---|---|
| `entity` | Các lớp dữ liệu: `Admin`, `MayTinh`, `MonAn`, `Menu`, `SuDungMay`, `ThanhToan`, `ThongKeDoanhThu` |
| `dao`, `daoImpl` | Giao diện và cài đặt truy vấn cơ sở dữ liệu |
| `controller` | Xử lý logic cho từng màn hình |
| `ui`, `ui.manager` | Các cửa sổ giao diện (đăng nhập, màn hình chính, mở máy, thanh toán, quản lý, thống kê) |
| `util` | Tiện ích dùng chung: kết nối CSDL (`XJdbc`), xác thực (`XAuth`), giao diện (`Style_Net`), ngày giờ, hộp thoại... |

## Cơ sở dữ liệu

Tên cơ sở dữ liệu: `NET_MANAGER_001`. Script tạo sẵn nằm ở file [`NET_MANAGER_001.sql`](NET_MANAGER_001.sql).

Các bảng: `Admin`, `MayTinh`, `SDMAY`, `MonAn`, `Menu`, `ThanhToan`, `ThongKe`.

Script cũng tạo các trigger tự sinh mã (`AD001`, `MT001`, `TD001`...) và chèn dữ liệu mẫu gồm 2 tài khoản, 13 máy (12 máy trống, 1 máy bảo trì) và 9 món ăn, đồ uống.

> **Cảnh báo:** script sẽ **xóa** cơ sở dữ liệu `NET_MANAGER_001` nếu đã tồn tại rồi tạo mới. Hãy sao lưu trước nếu bạn đang có dữ liệu thật.

## Hướng dẫn chạy

### Yêu cầu
- JDK 21 trở lên
- Apache Maven (hoặc NetBeans có hỗ trợ Maven)
- Microsoft SQL Server (bật TCP/IP, cổng 1433, cho phép đăng nhập SQL Server Authentication)

### Các bước

1. **Tải mã nguồn**
   ```bash
   git clone https://github.com/LuongHiep334/NETMANAGER.git
   cd NETMANAGER
   ```

2. **Tạo cơ sở dữ liệu:** mở `NET_MANAGER_001.sql` bằng SQL Server Management Studio và chạy toàn bộ script.

3. **Cấu hình kết nối:** ứng dụng đang kết nối tới
   ```
   jdbc:sqlserver://localhost:1433;database=NET_MANAGER_001;encrypt=true;trustServerCertificate=true;
   ```
   Mở lớp `util.XJdbc` và chỉnh `username`, `password` cho đúng với tài khoản SQL Server trên máy bạn.

4. **Chạy ứng dụng**
   ```bash
   mvn compile exec:java
   ```
   Lớp khởi chạy được cấu hình trong `pom.xml` là `ui.NetManagerJFrame`. Nếu dùng NetBeans, mở dự án và chạy lớp này.

> File `.jar` sinh ra từ `mvn package` **không chứa** các thư viện phụ thuộc và không khai báo lớp chính, nên không chạy trực tiếp bằng `java -jar`. Hãy chạy bằng Maven như trên.

### Tài khoản mẫu

Dữ liệu mẫu trong script tạo hai tài khoản để thử nghiệm:

| Vai trò | Tên đăng nhập | Mật khẩu |
|---|---|---|
| Quản lý | `admin` | `Admin123` |
| Nhân viên | `nhanvien1` | `123456` |

Đây chỉ là tài khoản thử nghiệm. Mật khẩu trong dự án hiện được lưu dạng văn bản thường, chưa mã hóa, nên không dùng cho môi trường thực tế.

## Hạn chế hiện tại

- Mật khẩu chưa được mã hóa trong cơ sở dữ liệu.
- Chức năng quên mật khẩu chưa xác minh qua email hay số điện thoại.
- Thông tin kết nối cơ sở dữ liệu đang cấu hình cứng trong mã nguồn.

## Tác giả

- **Lương Văn Hiệp**: [@LuongHiep334](https://github.com/LuongHiep334)
- Các thành viên khác trong nhóm: _(bổ sung tên và phần việc của từng người)_

<!--
Ảnh chụp màn hình: thêm vào thư mục docs/ rồi bỏ comment các dòng dưới.
## Ảnh chụp màn hình
![Đăng nhập](docs/login.png)
![Sơ đồ máy](docs/map.png)
![Thanh toán](docs/payment.png)
![Thống kê](docs/stats.png)
-->
