## 주기적 리부팅 후 ssh가 프록시 ip 를 바인드 하지 못하는 오류
- 원인 (추정)
  - tailscale도 프로세스로 실행 되고 있음
  - tailscale이 실행 되어야 ip를 바인드 할 수 있는 것 같음
  - 재부팅 -> ssh시작 -> tailscale 시작
  - 실제로 다시 ssh를 재시작 하면 ip가 정상적으로 바인딩 됨

## 해결 xxxx

- `sudo systemctl edit sshd.service` 수정

```` shell
[Unit]
After=network-online.target
Wants=network-online.target

[Service]
# tailscale 명령이 있거나 sshd_config에 특정 ListenAddress가 있으면 대기
ExecStartPre=/bin/sh -c '\
  # sshd_config에 ListenAddress 지정한 IP 가 있는지 확인
  if grep -qE "^\s*ListenAddress\s+***\.***\.***\.***" /etc/ssh/sshd_config && command -v tailscale >/dev/null 2>&1; then \
    echo "[sshd] waiting for {*****ip*****} to appear..."; \
    for i in $(seq 1 30); do \
      ip addr show | grep -q "{ip}" && exit 0 || sleep 2; \
    done; \
    echo "[sshd] timeout waiting for {ip}, continuing"; \
  fi; \
  exit 0'
# 실패시 재시작 설정은
Restart=on-failure
RestartSec=5s

````

- sudo systemctl daemon-reload
- sudo systemctl restart ssh
- 재시작
- 재부팅 후 지정 ip로 ssh 진입 성공 했지만 내부 와이파이랑 연결 되어있어서 성공 했을 가능성 있음

# 위 방법으로 해결 할 수 없었음

```
#!/bin/bash
TARGET_IP="100.75.33.73"
SSH_CMD="/usr/sbin/sshd"
MAX_RETRIES=0      # 0이면 무한 반복
SLEEP_INTERVAL=60  # 1분 간격

echo "[sshd-wait] Starting bind check for IP $TARGET_IP"

retry=0
while [ $MAX_RETRIES -eq 0 ] || [ $retry -lt $MAX_RETRIES ]; do
    if ip addr show | grep -q "$TARGET_IP"; then
        echo "[sshd-wait] IP $TARGET_IP found, starting sshd..."
        $SSH_CMD
        if [ $? -eq 0 ]; then
            echo "[sshd-wait] sshd started successfully, exiting script."
            exit 0
        else
            echo "[sshd-wait] sshd failed to start, retrying in $SLEEP_INTERVAL seconds..."
        fi
    else
        echo "[sshd-wait] IP $TARGET_IP not found, waiting $SLEEP_INTERVAL seconds..."
    fi
    sleep $SLEEP_INTERVAL
    ((retry++))
done

echo "[sshd-wait] Maximum retries reached, exiting."
exit 1
```
- /usr/local/bin/ 에 쉘 파일 생성
  - 실행권한 부여
- 부팅시 실행 파일로 설정
```
[Unit]
Description=Wait for Tailscale IP and start SSH
After=network.target tailscaled.service
Wants=network.target tailscaled.service

[Service]
Type=simple
ExecStart=/usr/local/bin/sshd-bind-wait.sh
Restart=always
RestartSec=10s

[Install]
WantedBy=multi-user.target
```
- /etc/systemd/system/ 경로에 위 내용 name.service로 저장
```
sudo systemctl daemon-reload
sudo systemctl enable name.service
sudo systemctl start name.service
```

- 부팅 시 실행되고 연결 될 때까지 주기적으로 무한 반복
- 연결시 종료 되는 shell로 변경

#### 해결