# Session 10 - Bài 2: Dockerfile Single-stage cho Backend Node.js/Express

## 1. Mục tiêu

Đóng gói một REST API đơn giản (Node.js/Express) thành Docker Image `hw-backend:v1` bằng Dockerfile single-stage, sau đó chạy container `app-single-stage` và kiểm tra bằng `curl`.

## 2. Cấu trúc thư mục

```
homework/session_10/ex2/
├── Dockerfile
├── .dockerignore
├── package.json
├── package-lock.json
├── server.js
└── README.md
```

## 3. Giải thích Dockerfile

| Chỉ thị | Ý nghĩa |
|---|---|
| `FROM node:18-alpine` | Dùng image Node.js 18 chạy trên Alpine Linux, dung lượng nhỏ |
| `WORKDIR /app` | Đặt thư mục làm việc trong container là `/app`, các lệnh phía sau chạy trong thư mục này |
| `ENV PORT=3000` | Khai báo biến môi trường `PORT`, `server.js` đọc giá trị này qua `process.env.PORT` |
| `COPY package*.json ./` | Copy riêng file khai báo thư viện trước để tận dụng cache của Docker |
| `RUN npm ci --omit=dev` | Cài đúng phiên bản thư viện theo `package-lock.json`, bỏ qua devDependencies |
| `COPY . .` | Copy phần mã nguồn còn lại vào `/app` (file trong `.dockerignore` sẽ không được copy) |
| `EXPOSE 3000` | Ghi chú rằng ứng dụng lắng nghe port 3000 (chỉ mang tính tài liệu, không tự mở port ra ngoài) |
| `CMD ["node", "server.js"]` | Lệnh chạy khi container khởi động, dùng exec form để `node` là tiến trình chính (PID 1) và nhận được tín hiệu dừng từ `docker stop` |

File `.dockerignore` giúp không đưa `node_modules` của máy thật, `.git`... vào image, làm build nhanh và image gọn hơn.

## 4. Các bước thực hiện

### Bước 1: Build image

```bash
docker build -t hw-backend:v1 .
```

Kết quả: `<dán đoạn cuối của log build, có dòng "naming to docker.io/library/hw-backend:v1">`

### Bước 2: Chạy container, map port 8080 (máy thật) sang 3000 (container)

```bash
docker run -d --name app-single-stage -p 8080:3000 hw-backend:v1
```

### Bước 3: Kiểm tra trạng thái container

```bash
docker ps
```

Kết quả: `<dán kết quả, cột STATUS phải là "Up ...", cột PORTS có 0.0.0.0:8080->3000/tcp>`

### Bước 4: Xem log

```bash
docker logs app-single-stage
```

Kết quả mong đợi:

```
Server listening on port 3000
```

### Bước 5: Gọi thử API

```bash
curl -i http://localhost:8080/
```

Kết quả mong đợi: mã `HTTP/1.1 200 OK` và nội dung JSON:

```json
{"status":"success","message":"Hello from Docker Single-stage!"}
```

Kết quả thực tế: `<dán kết quả curl của bạn>`

## 5. Ảnh chụp minh chứng

`<chèn ảnh chụp terminal các lệnh build, run, docker ps, docker logs, curl>`

## 6. Dọn dẹp (tùy chọn)

```bash
docker stop app-single-stage
docker rm app-single-stage
```

## 7. Nhận xét

`<viết 2-3 câu theo hiểu biết của bạn, ví dụ: Single-stage đơn giản, dễ viết, nhưng image chứa cả npm và các công cụ build nên chưa tối ưu dung lượng. Sau này có thể dùng multi-stage để giảm kích thước image.>`
