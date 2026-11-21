# Chạy bằng Docker

Gồm 3 container: `mysql` (MySQL 8), `server` (Spring Boot), `client` (nginx phục vụ
React build + proxy `/api`, `/media` sang backend). Trình duyệt chỉ gọi 1 domain.

## Chạy local

```bash
cp .env.example .env          # sửa key nếu cần
docker compose up -d --build
```

- Web: <http://localhost:8088> (đổi bằng `WEB_PORT`)
- Lần đầu backend mất ~40s để chạy migration. Xem log: `docker compose logs -f server`
- MySQL mở ở `127.0.0.1:3307` (user `root`, pass = `DB_PASSWORD`) để debug.

Dữ liệu nằm trong volume `mysql_data` và `uploads`.
Xoá sạch DB: `docker compose down -v` (mất toàn bộ dữ liệu!).

## Lên VPS

1. Cài Docker, copy repo lên VPS.
2. Tạo `.env` và **đổi**:
   - `DB_PASSWORD` mật khẩu mạnh (đặt trước lần chạy đầu tiên)
   - `JWT_SECRET` khoá mới: `openssl rand -base64 48`
   - `FRONTEND_URL=https://ten-mien-cua-ban`
   - `WEB_PORT=80` (hoặc giữ 8088 nếu đặt reverse proxy HTTPS phía trước)
3. `docker compose up -d --build`

**Bắt buộc HTTPS** khi dùng domain: trình duyệt chỉ cho mở camera (theo dõi tư thế
MediaPipe) trên HTTPS hoặc localhost. Cách đơn giản: Caddy/nginx + Let's Encrypt trên
VPS proxy về `localhost:8088`, nhớ chuyển header `X-Forwarded-Proto`.

Cập nhật code: `git pull && docker compose up -d --build`.
