# Dockerfile single-stage cho ứng dụng Node.js/Express
# Base image nhẹ dựa trên Alpine Linux
FROM node:18-alpine

# Thư mục làm việc bên trong container
WORKDIR /app

# Biến môi trường: port mà ứng dụng sẽ lắng nghe
ENV PORT=3000

# Copy file khai báo thư viện trước để tận dụng cache của Docker layer
# (chỉ khi package*.json đổi thì bước npm ci bên dưới mới chạy lại)
COPY package*.json ./

# Cài đặt thư viện (bỏ qua devDependencies)
RUN npm ci --omit=dev

# Copy phần mã nguồn còn lại vào image
COPY . .

# Khai báo port ứng dụng sử dụng (chỉ mang tính tài liệu)
EXPOSE 3000

# Lệnh chạy ứng dụng, dùng exec form
CMD ["node", "server.js"]
