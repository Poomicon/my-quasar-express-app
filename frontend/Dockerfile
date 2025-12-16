# Stage 1: Build Quasar SPA
FROM node:20-alpine AS builder
WORKDIR /app

# 1. Copy package*.json และ .npmrc ก่อนเพื่อใช้ Cache layer
COPY package*.json ./

# 2. Install dependencies (ใช้ --ignore-scripts เพื่อข้าม quasar prepare ก่อน เพราะยังไม่มีไฟล์โปรเจกต์)
RUN npm ci --ignore-scripts

# 3. Copy ไฟล์โปรเจกต์ทั้งหมด
COPY . .

# 4. Run quasar prepare (รันตอนนี้เพราะมีไฟล์โปรเจกต์แล้ว)
RUN npx quasar prepare

# 5. Build Quasar SPA (ผลลัพธ์อยู่ที่ /app/dist/spa)
RUN npx quasar build -m spa

# Stage 2: Production (Serve with Nginx)
FROM nginx:1.27-alpine

# 1. Copy build artifacts จาก stage 1
COPY --from=builder /app/dist/spa /usr/share/nginx/html

# 2. สร้าง Config สำหรับ Nginx เพื่อรองรับ Quasar History Mode (แก้ปัญหา 404 เมื่อ Refresh หน้าเว็บ)
RUN echo 'server { \
    listen 80; \
    root /usr/share/nginx/html; \
    index index.html; \
    location / { \
        try_files $uri $uri/ /index.html; \
    } \
}' > /etc/nginx/conf.d/default.conf

# Expose port และรัน Nginx
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]