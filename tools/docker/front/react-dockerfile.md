#Build
FROM node:lts-alpine AS build
WORKDIR /app

# 캐시 최적화를 위해 먼저 패키지 파일만 복사
COPY package*.json ./
RUN npm ci

# 소스 복사 후 빌드
COPY . .
RUN npm run build
# Vite면 dist가 생성, CRA면 build가 생성됨

# Serve with Nginx
FROM nginx:alpine
# 기본 conf 제거
RUN rm -f /etc/nginx/conf.d/default.conf

# 리액트 전용 Nginx 설정 복사
COPY nginx.conf /etc/nginx/conf.d/react.conf

# 빌드 산출물 복사 (Vite: dist / CRA: build 로 변경)
COPY --from=build /app/dist /usr/share/nginx/html
# CRA
# COPY --from=build /app/build /usr/share/nginx/html

EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
