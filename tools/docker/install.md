```
# 1) 키 저장 디렉터리 보장
sudo install -m 0755 -d /etc/apt/keyrings

# 2) 키 다운로드 + dearmor
curl -fsSL https://download.docker.com/linux/ubuntu/gpg \
  | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

# 3) apt가 읽을 수 있게 권한
sudo chmod a+r /etc/apt/keyrings/docker.gpg

# 4) 저장소 등록 (UBUNTU_CODENAME: jammy/noble 등)
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
https://download.docker.com/linux/ubuntu \
$(. /etc/os-release && echo $UBUNTU_CODENAME) stable" \
| sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# 5) 갱신
sudo apt update

# 6) 도커 저장소 추가
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
  https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# 7) 도커 설치
sudo apt update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin


# 8) 그룹 추가
sudo groupadd docker
sudo usermod -aG docker $USER
newgrp docker
```