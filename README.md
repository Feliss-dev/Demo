# Tài liệu chức năng đăng nhập Google

Tài liệu này hướng dẫn cách thiết lập và triển khai chức năng đăng nhập bằng Google trong ứng dụng của bạn. Quá trình bao gồm việc tạo ứng dụng trên Google Cloud, lấy thông tin xác thực (credentials), và cấu hình biến môi trường trong `docker-compose`.

## Mục lục
1. [Thiết lập ứng dụng Google](./setup_google_app.md)
2. [Cấu hình Docker và biến môi trường](./docker_config.md)

## Tổng quan
Chức năng đăng nhập Google sử dụng OAuth 2.0 để xác thực người dùng. Người dùng sẽ được chuyển hướng đến trang đăng nhập của Google, sau khi xác thực thành công, ứng dụng sẽ nhận được token để xác minh danh tính.

## Yêu cầu
- Tài khoản Google Cloud
- Docker và Docker Compose đã được cài đặt
- Quyền truy cập vào mã nguồn backend

Bắt đầu với [Thiết lập ứng dụng Google](./setup_google_app.md).

# Thiết lập ứng dụng Google cho OAuth 2.0

Hướng dẫn này mô tả cách tạo một ứng dụng trên Google Cloud Console để hỗ trợ đăng nhập Google thông qua OAuth 2.0.

## Bước 1: Tạo dự án trên Google Cloud
1. Truy cập [Google Cloud Console](https://console.cloud.google.com/).
2. Nhấn **"Create Project"** (Tạo dự án):
   - Đặt tên dự án (ví dụ: `MyApp-Google-Login`).
   - Chọn tổ chức (nếu có) hoặc để mặc định.
3. Nhấn **"Create"** và chờ dự án được tạo.

## Bước 2: Kích hoạt API Google+
1. Trong menu bên trái, chọn **"APIs & Services" > "Library"**.
2. Tìm **"Google+ API"** (hoặc "People API" nếu cần thông tin người dùng).
3. Nhấn **"Enable"** để kích hoạt API.

## Bước 3: Tạo thông tin xác thực (Credentials)
1. Vào **"APIs & Services" > "Credentials"**.
2. Nhấn **"Create Credentials" > "OAuth Client ID"**.
3. Cấu hình màn hình đồng ý (Consent Screen):
   - Chọn **"External"** (cho ứng dụng công khai).
   - Điền thông tin:
     - Tên ứng dụng: `MyApp`
     - Email hỗ trợ: `<your-email>@example.com`
     - Các scope: chọn `userinfo.email` và `userinfo.profile`.
   - Lưu và quay lại.
4. Tạo OAuth Client ID:
   - Loại ứng dụng: **"Web application"**.
   - Đặt tên: `Web Client for MyApp`.
   - URI chuyển hướng được ủy quyền: `http://localhost:8000/auth/google/callback` (thay đổi theo domain thực tế của bạn).
5. Nhấn **"Create"**, sau đó tải xuống tệp JSON hoặc ghi lại:
   - `Client ID`
   - `Client Secret`

## Bước 4: Lưu thông tin xác thực
- Lưu `Client ID` và `Client Secret` vào một nơi an toàn (sẽ dùng trong bước cấu hình Docker).

Tiếp theo: [Cấu hình Docker và biến môi trường](./docker_config.md).

# Cấu hình Docker và biến môi trường cho đăng nhập Google

Hướng dẫn này mô tả cách tích hợp thông tin xác thực Google vào ứng dụng thông qua `docker-compose`.

## Bước 1: Tạo tệp `.env`
1. Trong thư mục gốc của dự án (`project_root/`), tạo tệp `.env`:

- Thay `<your-client-id>` và `<your-client-secret>` bằng giá trị lấy từ Google Cloud.
- `GOOGLE_REDIRECT_URI` phải khớp với URI đã khai báo ở bước trước.

2. Đảm bảo tệp `.env` được thêm vào `.gitignore` để không commit lên repository.

## Bước 2: Cập nhật `docker-compose.yml`
1. Mở tệp `docker-compose.yml` trong `backend/` (hoặc thư mục gốc).
2. Cấu hình service backend để sử dụng biến môi trường:
```yaml
version: '3.8'
services:
  backend:
    build: ./backend
    ports:
      - "8000:8000"
    environment:
      - GOOGLE_CLIENT_ID=${GOOGLE_CLIENT_ID}
      - GOOGLE_CLIENT_SECRET=${GOOGLE_CLIENT_SECRET}
      - GOOGLE_REDIRECT_URI=${GOOGLE_REDIRECT_URI}
    volumes:
      - ./backend:/app
    command: uvicorn app.main:app --host 0.0.0.0 --port 8000
## Chạy ứng dụng với Docker Compose

Để khởi động ứng dụng, chạy câu lệnh sau trong terminal:

```bash
docker-compose up --build


- **Lợi ích**: 
  - Câu lệnh được tô sáng (syntax highlighting) nếu xem trên các nền tảng như GitHub, GitLab.
  - Rõ ràng, dễ sao chép.

---

### 2. Sử dụng Inline Code cho câu lệnh ngắn
Nếu câu lệnh ngắn và nằm trong đoạn văn, bạn có thể dùng một dấu backtick (`) để bọc câu lệnh.

#### Ví dụ:
```markdown
Để dừng ứng dụng, dùng lệnh `docker-compose down`.
