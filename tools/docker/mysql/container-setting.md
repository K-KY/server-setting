```
docker run -d \
  --name mysql-container \
  -e MYSQL_ROOT_PASSWORD=비밀번호 \
  -e MYSQL_DATABASE=데이터베이스명 \
  -p 3306:3306 \
  -v /home/ubuntu/mysql_data:/var/lib/mysql \
  mysql:8.0.43
  

```
- 8.0.43 이 현재 최신버전
- -d : 백그라운드(detached) 모드 실행

- --name : 컨테이너 이름 지정
 
- -e : 환경 변수 설정 (root 비밀번호, DB명, 사용자명 등)
 
- -p 3306:3306 : 호스트와 컨테이너의 포트 연결
 
- -v : 볼륨 마운트 (데이터를 호스트에 영구 저장)

- mysql:8.0 : 사용할 MySQL 이미지 버전 지정