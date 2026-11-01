# Bản đồ nhiệt & phân tích kinh doanh Việt Tuấn

Mở `index.html` qua GitHub Pages và đăng nhập bằng mật khẩu hiện có. Menu bên trái có:

- **Bản đồ hiện tại**: giữ nguồn CSV, dữ liệu kho, vùng khoanh, nhãn, lộ trình, nhập/xuất cài đặt và xuất Excel. Bổ sung tên tỉnh/huyện cũ, nền hành chính ngoại tuyến, tự thu phóng theo bộ lọc và danh sách khách trùng tọa độ.
- **Bản đồ doanh số theo kỳ**: mở trong ứng dụng, đăng nhập Google bằng tài khoản có quyền đọc trang tính. Chọn tháng/quý/6 tháng/năm; đối soát tiền đầu đơn gồm VAT, xuất khách chưa định vị/chưa khớp mã. Khi thiếu lịch sử, ứng dụng không tính tổng đủ kỳ. Luồng này dùng bản Warehouse đã đồng bộ vào các tab WH_ và định vị từ cùng trang tính của bản nguồn; không dùng cột doanh số nhập tay thay thế.
- **Phân tích S-SUB VH1/VT1**: nạp CSV UTF-8 xuất từ WH_KhachHang, xác nhận cột mã/tên/địa bàn/nhóm. Mặc định cột J khi đủ 10 cột theo yêu cầu trong cuộc trò chuyện; tiêu đề hiện trong bộ chọn để người dùng kiểm tra. Không suy đoán VH1/VT1 từ tên hoặc trường khác. Có thống kê nhóm, phân bố địa bàn, tìm mã/tên và cảnh báo mã lặp. Tổng là số dòng nhập, chưa phải thị phần. Chưa tự lấy nhóm S-SUB hoặc doanh số từ Google.

Các trang được giữ trong bộ nhớ khi chuyển menu. Nút **← Bản đồ hiện tại** quay về bản đồ cũ. Dữ liệu và cấu hình cũ dùng nguyên khóa `mkt_v1`; dữ liệu thiết bị của trang doanh số theo kỳ dùng cơ chế lưu mã hóa theo tài khoản từ bản nguồn, có xuất/nhập sao lưu riêng. Có thể dùng Xuất/Nhập cài đặt bản đồ để chuyển cấu hình giữa hai bản đồ; không tự ghi đè cấu hình cũ.

## Xác minh nguồn và so sánh

Đã kiểm tra cuộc trò chuyện được tham chiếu, ảnh chụp ứng dụng, danh sách repository tài khoản, lịch sử và mã nguồn. Đích là `AdamAvaAdam/bandonhiet1`, nền `b694b82fcd2452fca224e40b6f65650583b8d8ba`; trước đó chỉ thêm trang S-SUB riêng, chưa có menu trong index.

Nguồn xác minh là [AdamAvaAdam/BHVer1](https://github.com/AdamAvaAdam/BHVer1), commit sáng 01/11/2026 lúc 10:32:35 UTC+7 [42593d6](https://github.com/AdamAvaAdam/BHVer1/commit/42593d612d14496c89adb1718c91011508a034ce). Đã giải nén `vt-map-document`, so sánh các hàm với index của đích và xác nhận cùng nền mã kho/vùng/lộ trình/nhãn/ranh giới. Đã xác nhận payload bản đồ không đổi giữa commit sáng này và HEAD [5a4d7e4](https://github.com/AdamAvaAdam/BHVer1/commit/5a4d7e4f631ed6ed31097fd6cbe1aab52b5a6616).

| Khác biệt nguồn so với đích | Cách tích hợp |
|---|---|
| Doanh số theo tháng/quý/6 tháng/năm | `revenue-map.html`, mở từ menu index, dùng nguyên bộ đọc Google chỉ đọc, kiểm tra lượt đồng bộ/phạm vi/tổng đầu đơn |
| Đối soát, khách chưa định vị, mã chưa khớp, mã trùng | Giữ nguyên bộ ghép mã chính xác và xuất danh sách của nguồn |
| Nền hành chính ngoại tuyến, tên tỉnh/huyện cũ, lọc và thu phóng, popup cùng tọa độ | Đưa vào cả bản đồ hiện tại và bản đồ theo kỳ; vẫn giữ lựa chọn nền tối |
| Đăng nhập Google và lưu thiết bị mã hóa | Giữ luồng đã xác minh của nguồn trong trang theo kỳ, giữ đăng nhập/cấu hình cũ của index |
| Các thay đổi giá/tồn/thông số sau buổi sáng | Không đưa sang vì không thuộc bản đồ; bỏ payload ứng dụng bán hàng không liên quan |

Hai sửa lỗi sau tích hợp: giới hạn mức zoom khi chỉ có một tọa độ để tránh NaN; xóa các thống kê kho/chú giải cũ khi tải kỳ mới thất bại. CSP được cập nhật theo nội dung script mới. Hash nguồn và đầu ra nằm trong `integration-source.json`.

## Kiểm thử

`test/integration.cjs` dùng dữ liệu mẫu và giả lập dịch vụ trong trình duyệt; không gọi dữ liệu kinh doanh thật. Cài Playwright 1.62.1, rồi chạy `node test/integration.cjs` (hoặc đặt PLAYWRIGHT_PATH trỏ tới gói đã cài). Mặc định Chrome ở đường dẫn Windows thông thường; đặt CHROME_PATH nếu khác.

Kiểm tra cú pháp, CSP của bản đồ nhúng, giữ nguyên hàm tải CSV/tính khoảng cách/xuất Excel và dữ liệu ranh giới, mật khẩu đúng/sai, tổng bản đồ cũ, S-SUB nhập/tìm kiếm/xóa kết quả cũ khi lỗi, đăng nhập Google giả lập, tổng đầu đơn và đối soát, mã trùng/chưa khớp/chưa định vị, bốn loại kỳ, từ chối kỳ thiếu dữ liệu, chuyển menu và bố cục máy tính/điện thoại. Chưa kiểm chứng đăng nhập Google thật hoặc số liệu thật sau triển khai; phụ thuộc quyền Google và trạng thái đồng bộ hiện có.
