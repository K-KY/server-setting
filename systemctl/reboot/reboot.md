# 주기적 리부트

```
sudo tee /etc/systemd/system/periodic-reboot.service >/dev/null <<'EOF'
[Unit]
Description=Periodic reboot

[Service]
Type=oneshot
ExecStart=/usr/sbin/reboot
EOF
```
 - periodic-reboot.service
   - 주기적으로 수행할 동작을 설정



```
sudo tee /etc/systemd/system/periodic-reboot.timer >/dev/null <<'EOF'
[Unit]
Description=Reboot every 6 hours at 00,06,12,18

[Timer]
OnCalendar=*-*-* 00,06,12,18:00:00
Persistent=true

[Install]
WantedBy=timers.target
EOF
```
- periodic-reboot.timer
  - 실행할 주기 설정 
    - 00시, 06시, 12시, 18시에 실행

```
sudo systemctl daemon-reload
sudo systemctl enable --now periodic-reboot.timer
```
- 적용

### 타임존 설정

```text
timedatectl
sudo timedatectl set-timezone Asia/Seoul
```
- 한국 사니까 서울로 설정
- 

```
systemctl list-timers
```
- 다음 실행 예정시간 확인