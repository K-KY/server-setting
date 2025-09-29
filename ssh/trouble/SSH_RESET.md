## 주기적 리부팅 후 ssh가 프록시 ip 를 바인드 하지 못하는 오류
- 원인 (추정)
  - tailscale도 프로세스로 실행 되고 있음
  - tailscale이 실행 되어야 ip를 바인드 할 수 있는 것 같음
  - 재부팅 -> ssh시작 -> tailscale 시작
  - 실제로 다시 ssh를 재시작 하면 ip가 정상적으로 바인딩 됨

## 해결

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
