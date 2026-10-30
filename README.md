# K26CNTT2_IT108_Session14_Ex06

# 🌾 Báo cáo Bài tập: Giải pháp Quản lý Dự án Smart Farm (Trang trại thông minh)
**Môn học:** Nhập Môn Công Nghệ Thông Tin (Buổi 15: Không gian cộng tác đám mây)  
**Học viên:** Lê Hồ Thanh Thảo (N26DTCN030)  
**Repository:** `K26CNTT2_IT108_Session14_Ex06`

---

## 1. Mục tiêu
- Vận dụng tổng hợp kiến thức từ Session 01 đến Session 14 để quản lý dự án công nghệ lớn.
- Tích hợp các mô hình tư duy cốt lõi: **IPO** (Input - Process - Output), tháp tri thức **DIKW** (Data - Information - Knowledge - Wisdom) và **Cloud Workspace**.

---

## 2. Bối cảnh & Bài toán
> **Tình huống:** Dự án *Trang trại thông minh* của gia đình cần mở rộng quy mô. Hệ thống cần quản lý đồng thời 3 thành phần trọng yếu:
> 1. **Source code** điều khiển hệ thống máy bơm tự động.
> 2. **Dữ liệu nhiệt độ** cảm biến thu thập liên tục (50MB/ngày).
> 3. **Tài liệu hướng dẫn vận hành** dành cho người nông dân.

---

## 3. Yêu cầu đầu ra & Triển khai chi tiết

### Phần 1: Phân tích Sơ đồ phối hợp giữa Thiết bị IoT (Local) và Không gian cộng tác (Cloud)

Luồng dữ liệu vận hành theo mô hình **IPO** kết hợp **Hybrid Workflow**:
* **Input (Đầu vào):** Cảm biến IoT tại trang trại đo đạc nhiệt độ liên tục (sinh ra ~50MB dữ liệu/ngày); Kỹ sư lập trình viết và chỉnh sửa mã điều khiển máy bơm trên máy cá nhân (Local).
* **Process (Xử lý):** 
  * Dữ liệu thô từ cảm biến được gom nhóm, lọc bỏ nhiễu và chuyển hóa thành thông tin biểu đồ nhiệt độ (áp dụng tầng **DIKW**).
  * Mã nguồn được quản lý qua Git kết hợp quy trình Hybrid Workflow (lưu nháp trên SSD, đồng bộ mã nguồn chính lên Cloud).
* **Output (Đầu ra):** Báo cáo trực quan trên Cloud Workspace; Task tự động hóa từ cảnh báo lỗi cảm biến; Tài liệu hướng dẫn thân thiện cho người dùng cuối.

---

### Phần 2: Bản đặc tả Kiến trúc Không gian Cộng tác (README Spec)

#### 1. Cấu trúc Thư mục Cloud & Quản lý Lưu trữ dữ liệu cảm biến (Chức năng 1)
Với lượng dữ liệu cảm biến lớn (**50MB/ngày $\approx$ 1.5GB/tháng**), nếu đồng bộ toàn bộ sẽ nhanh chóng làm tràn dung lượng Cloud. Giải pháp tối ưu là sử dụng tính năng **Files On-Demand**:
* Thư mục chứa dữ liệu log cảm biến được bật chế độ trực tuyến (**Cloud-only**), chỉ tải về khi có nhu cầu phân tích, giúp giải phóng hoàn toàn dung lượng ổ cứng thiết bị.

```text
📁 Smart_Farm_Workspace/
│
├── 📁 01_IoT_Sensor_Data/             <-- Lưu dữ liệu nhiệt độ (Bật tính năng Files On-Demand)
│   ├── 📊 raw_logs_2026/              <-- Dữ liệu thô (50MB/ngày)
│   └── 📈 aggregated_reports/         <-- Thông tin phân tích theo DIKW
│
├── 📁 02_Pump_Control_SourceCode/     <-- Mã nguồn điều khiển máy bơm (Hybrid Workflow)
│   ├── 📁 src/                        <-- Source code chính (Đồng bộ thời gian thực, loại trừ node_modules)
│   └── 📄 package.json
│
└── 📁 03_Farmer_User_Manuals/         <-- Tài liệu hướng dẫn cho người nông dân (PDF, Video)



2. Thiết lập Phân quyền Chặt chẽ (Chức năng 2)Áp dụng nguyên tắc đặc quyền tối thiểu (Least Privilege) để bảo mật thông tin trang trại:Viewer (Người xem): Dành cho người nông dân và nhân sự vận hành trực tiếp (chỉ được xem báo cáo nhiệt độ và tài liệu hướng dẫn, không được chỉnh sửa).Editor (Người chỉnh sửa): Dành riêng cho Kỹ sư lập trình phần cứng/phần mềm (có quyền truy cập thư mục mã nguồn để tối ưu code máy bơm).Commenter / Restricted: Dành cho các bên cố vấn kỹ thuật bên ngoài.3. Tự động hóa Giao tiếp & Task (Chức năng 3)Chuyển đổi thông báo lỗi thành Task: Khi hệ thống cảm biến phát hiện nhiệt độ bất thường hoặc máy bơm lỗi, hệ thống tích hợp (Enterprise Workspace Bot) tự động tạo một Task mới có gắn Deadline cụ thể, phân công trực tiếp cho kỹ thuật viên phụ trách xử lý sự cố ngay lập tức mà không sợ bị trôi tin nhắn.Phần 3: Quy trình Kiểm thử & Xử lý sự cố Rò rỉ dữ liệu (Data Leak)Giả định: Hệ thống xảy ra sự cố rò rỉ dữ liệu (Data Leak) do phân quyền nhầm lẫn. Quy trình 3 bước xử lý khẩn cấp:Bước 1: Khóa và Thu hồi ngay lập tức quyền truy cập (Revoke Access)Quản trị viên (Super Admin) truy cập ngay vào phần cài đặt chia sẻ của thư mục dự án, chuyển trạng thái từ chia sẻ công khai/liên kết sang chế độ Restricted (Hạn chế) và gỡ bỏ toàn bộ các tài khoản nghi vấn hoặc không có phận sự.Bước 2: Kiểm tra Nhật ký hoạt động (Activity Audit Log)Truy cập mục Admin Console $\rightarrow$ Security $\rightarrow$ Audit Log (hoặc Access Audit Log) để rà soát toàn bộ lịch sử truy cập trong tháng: Ai đã tải xuống file? Ai đã chỉnh sửa tài liệu? Vào lúc mấy giờ? Từ địa chỉ IP nào? nhằm khoanh vùng chính xác phạm vi thất thoát dữ liệu.Bước 3: Đánh giá thiệt hại, vá lỗ hổng và Báo cáoSau khi trích xuất nhật ký hoạt động làm bằng chứng, tiến hành thay đổi toàn bộ khóa bảo mật (API Keys), thiết lập lại quy tắc phân quyền nghiêm ngặt hơn và gửi báo cáo sự cố minh bạch đến các bên liên quan.


