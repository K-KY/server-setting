# ssh 원격 접속

## ufw 활성화

```text
sudo ufw enable
```


## 정책 추가

```text
sudo ufw default deny incoming ## 들어오는 요청 전부 거부
sudo ufw default allow outgoing ## 나가는 요청 전부 허락
sudo ufw allow 22/tcp ## ssh
sudo ufw allow 80/tcp ## http
sudo ufw allow 443/tcp ## https
sudo ufw enable
sudo ufw reload

```


## ssh 키 접속

```text
cat ~/.ssh/id_rsa.pub | ssh 사용자@서버_IP "mkdir -p ~/.ssh && chmod 700 ~/.ssh && cat >> ~/.ssh/authorized_keys && chmod 600 ~/.ssh/authorized_keys"
```


# 만약 호스트가 바뀐경우
```text
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
```
- 많이 생략하긴 했는데 대부분 이런 내용이 나올거

```text
ssh-keygen -R 서버_ip
```
- 저장된 키를 삭제하고 다시 접속하면 됨