# YMEDU – Nền tảng học tập trực tuyến (Quiz App)

Ứng dụng desktop **Windows Forms (.NET 8)** mô phỏng một nền tảng học tập trực tuyến: người dùng có thể tạo và bán khóa học, học qua video / lý thuyết / flashcard, làm bài kiểm tra, mua khóa học hoặc gói thành viên và nhận chứng chỉ. Quản trị viên có bảng điều khiển riêng để quản lý, kiểm duyệt và xem báo cáo.

Đồ án môn **Lập trình trực quan (LTTQ)**, năm học 2025–2026.

---

## Tính năng chính

### Người dùng (User)
- **Tài khoản**: đăng ký, đăng nhập, quên mật khẩu, hồ sơ cá nhân, cài đặt tài khoản, lịch sử mua hàng.
- **Khám phá khóa học**: trang chủ với carousel, duyệt theo danh mục, tìm kiếm, **gợi ý khóa học** theo lịch sử học, hành vi, độ phổ biến và giá.
- **Học tập**: khóa học chia thành *Chương → Bài học → Nội dung*. Mỗi nội dung có thể là:
  - `Video` (phát bằng LibVLC)
  - `Theory` (lý thuyết, tài liệu PDF qua PdfiumViewer)
  - `FlashcardSet` (bộ thẻ ghi nhớ)
  - `Test` (bài kiểm tra)
- **Theo dõi tiến độ** học của từng khóa và **chứng chỉ** khi hoàn thành (xuất PDF, gửi qua email).
- **Bài kiểm tra**: 6 loại câu hỏi là `MCQ_Single`, `MCQ_Multi`, `TrueFalse`, `Cloze` (điền khuyết), `Range` (đáp án trong khoảng) và `ShortText`. Có lưu lịch sử làm bài và xem lại đáp án.
- **Flashcard**: tạo, sửa, học và luyện tập bộ thẻ, có ghi lại lịch sử luyện tập.
- **Tạo khóa học (Course Builder)**: trình hướng dẫn 4 bước gồm Thông tin, Cấu trúc, Nội dung và Xuất bản. Khóa học được gửi lên để admin kiểm duyệt.
- **Giảng viên**: xem kết quả làm bài của học viên (phân trang, tìm kiếm, xuất Excel) và xem doanh thu khóa học của mình.
- **Mua hàng**: giỏ hàng, mã giảm giá (voucher), thanh toán qua **MoMo** (sandbox) hoặc **VietQR**, và **gói thành viên (subscription)** theo tháng.
- **Thư viện**: lưu khóa học, bài học, flashcard và bài kiểm tra.
- **Đa ngôn ngữ**: Tiếng Việt (mặc định) và English.

### Quản trị viên (Admin)
- **Dashboard**: tổng quan, người dùng, học tập và doanh thu (biểu đồ Guna Charts).
- **Quản lý**: người dùng, khóa học, danh mục, flashcard, mã giảm giá.
- **Kiểm duyệt khóa học**: hệ thống tự kiểm tra tiêu đề, mô tả, ảnh bìa, giá, số chương/bài và từ khóa nhạy cảm, sau đó admin duyệt thủ công.
- **Nhật ký hệ thống (Audit Log)**: ghi và lọc các thao tác trong hệ thống.
- **Báo cáo** (RDLC / ReportViewer): báo cáo tổng hợp, người dùng, khóa học, doanh thu, bài kiểm tra và chứng chỉ. Có xuất CSV/Excel.

---

## Công nghệ sử dụng

| Thành phần | Công nghệ |
|---|---|
| Nền tảng | .NET 8 (`net8.0-windows`), Windows Forms |
| Giao diện | Guna.UI2.WinForms, Guna.Charts.WinForms, System.Windows.Forms.DataVisualization |
| Cơ sở dữ liệu | SQL Server + Entity Framework Core 9 (Database-First) |
| Lưu trữ media | Azure Blob Storage (tự chuyển sang lưu cục bộ nếu không cấu hình) |
| Video / PDF | LibVLCSharp + VideoLAN.LibVLC.Windows, PdfiumViewer |
| Báo cáo | ReportViewerCore.WinForms (`.rdlc`) |
| Excel | ClosedXML |
| Thanh toán | MoMo (môi trường test), VietQR + QRCoder |
| Email | SMTP (`System.Net.Mail`) |

---

## Cấu trúc thư mục

```
Quiz_App/WinFormsApp1/
├── WinFormsApp1.sln
├── Database/Migrations/          # Migration bổ sung trường kiểm duyệt khóa học
├── Res/                          # File test (pdf, video)
└── WinFormsApp1/                 # Project chính (WinFormsApp1.csproj)
    ├── Program.cs                # Điểm khởi động: đọc cấu hình, khởi tạo Azure và ngôn ngữ, mở form đăng nhập
    ├── appsettings.json
    ├── Controllers/              # Xử lý nghiệp vụ (Admin, Course, CourseBuilder, Flashcard, AuditLog)
    ├── Service/                  # Dịch vụ: Course, Discount, Flashcard, AuditLog, gợi ý khóa học
    │   └── PaymentService/       # MoMo, VietQR, giỏ hàng, gói thành viên
    ├── Services/                 # Azure Blob Storage, Email, kiểm duyệt khóa học
    ├── Models/
    │   ├── EF/                   # LearningPlatformContext (DbContext)
    │   └── Entities/             # Các entity ánh xạ bảng CSDL
    ├── ViewModels/               # ViewModel cho dashboard, báo cáo, course builder, discount...
    ├── View/
    │   ├── dangnhap.cs, dangky.cs, quenmatkhau.cs   # Đăng nhập / Đăng ký / Quên mật khẩu
    │   ├── Admin/                # Giao diện quản trị
    │   ├── User/                 # Giao diện người dùng (MainContainer, Controls, Forms, Components)
    │   ├── Dialogs/              # Thanh toán MoMo/QR, chứng chỉ
    │   └── Components/           # Toast thông báo
    ├── Helpers/                  # Tiện ích: Auth, Session, Password, Media, Validation, Export...
    ├── Localization/             # Đa ngôn ngữ (Strings.resx, Strings.en-US.resx)
    ├── Reports/                  # Mẫu báo cáo .rdlc
    ├── Database/                 # Script SQL tạo CSDL, migration, seed data, sơ đồ ERD
    ├── Library/                  # Ảnh, video, PDF lưu cục bộ
    └── Resources/                # Icon, logo
```

Sơ đồ thực thể chi tiết nằm ở [WinFormsApp1/WinFormsApp1/Database/ERD_Diagram.md](WinFormsApp1/WinFormsApp1/Database/ERD_Diagram.md).

---

## Cài đặt và chạy

### Yêu cầu
- Windows 10/11
- Visual Studio 2022 (workload **.NET desktop development**) hoặc .NET 8 SDK
- SQL Server (bản Express/Developer hoặc Docker)

### 1. Tạo cơ sở dữ liệu
Chạy các script trong `WinFormsApp1/WinFormsApp1/Database/` theo thứ tự:

1. `LearningPlatform.sql`: tạo database `LearningPlatform`, toàn bộ bảng và 2 role là `Admin` và `User`.
2. `SubscriptionPlans_Migration.sql`: bảng gói thành viên.
3. `UserSubscription_Migration.sql`: bảng đăng ký gói của người dùng.
4. `SeedData_RealVideos_Updated.sql`: dữ liệu mẫu (không bắt buộc).

> `Discount_Migration.sql` và `Database/Migrations/Add_Course_Moderation_Fields.sql` chỉ cần chạy khi nâng cấp một CSDL cũ, vì `LearningPlatform.sql` đã có sẵn các bảng và cột này.

### 2. Cấu hình
Tạo file `WinFormsApp1/WinFormsApp1/appsettings.Development.json`. File này đã nằm trong `.gitignore`, **không commit thông tin bí mật**:

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=localhost;Database=LearningPlatform;User ID=sa;Password=<mật_khẩu>;TrustServerCertificate=True",
    "AzureBlobConnectionString": "<chuỗi kết nối Azure Storage, để trống nếu lưu cục bộ>"
  },
  "EmailSettings": {
    "SmtpHost": "smtp.gmail.com",
    "SmtpPort": "587",
    "FromEmail": "<email>",
    "FromPassword": "<app password>",
    "FromName": "YMEDU"
  }
}
```

- Nếu không có `AzureBlobConnectionString`, media sẽ được lưu vào thư mục `Library/`.
- Thông tin tài khoản nhận tiền VietQR nằm trong `Service/PaymentService/VietQRConfig.cs`.
- MoMo đang dùng endpoint test `test-payment.momo.vn`.

### 3. Build và chạy
```bash
cd WinFormsApp1
dotnet restore
dotnet run --project WinFormsApp1/WinFormsApp1.csproj
```
Hoặc mở `WinFormsApp1.sln` bằng Visual Studio rồi nhấn **F5**.

Ứng dụng mở form **Đăng nhập**. Tài khoản có role `Admin` sẽ vào **Admin Dashboard**, các tài khoản khác vào giao diện người dùng.

---

## Kiến trúc tóm tắt

- **View** (Form / UserControl) gọi **Controller** hoặc **Service** để xử lý nghiệp vụ.
- **Service / Controller** truy cập dữ liệu qua `LearningPlatformContext` (EF Core).
- **Helpers** chứa các phần dùng chung: phiên đăng nhập (`AuthHelper`, `SessionHelper`), phân quyền (`PermissionHelper`), băm mật khẩu (`PasswordHelper`), upload media (`MediaHelper`), hiển thị thông báo (`ToastHelper`), xuất dữ liệu (`ExportImportHelper`), v.v.
- **Localization** dùng file `.resx`. Ngôn ngữ được đổi qua `LanguageHelper` / `LanguageSelectorDialog`.
