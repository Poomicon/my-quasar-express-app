# Stage 1: Build Quasar SPA
FROM node:20-alpine AS builder
WORKDIR /app

# 1. Copy package*.json และ .npmrc ก่อนเพื่อใช้ Cache layer
COPY package*.json ./

# 2. Copy ไฟล์โปรเจกต์ทั้งหมด (ยกเว้นที่อยู่ใน .dockerignore)
#    ต้องอยู่ตรงนี้เพื่อให้สคริปต์ของ Quasar รันได้สมบูรณ์ในขั้นตอน npm ci
COPY . .

# 3. ติดตั้ง dependencies และรันสคริปต์ prepare (ที่เคย error)
#    เราใช้ npm install แทน npm ci เพื่อความยืดหยุ่น ถ้ายัง error ค่อยเปลี่ยนกลับเป็น npm ci
RUN npm install --legacy-peer-deps

# 4. Build Quasar SPA (ผลลัพธ์อยู่ที่ /app/dist/spa)
RUN npx quasar build -m spa
# 5. ติดตั้ง Leaflet และ @types/leaflet
RUN npm install leaflet @types/leaflet

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