

# 발생한 문재
- tailscale과 복합적으로 발생한 문제

```bash
tailscale funnel status

# Funnel on:
#     - https://kky.tail0a6d17.ts.net

https://TAILSCALEDOMAIN (Funnel on)
|-- /    proxy http://127.0.0.1:80
|-- /api proxy http://127.0.0.1:8080
|-- /hls proxy http://127.0.0.1:8787
```

- 위처럼 프록시 설정을 해뒀음
## 문제 발생
- domain/api -> 백엔드로 가게 해놨음
- 근데 프록시 패스 strip안되는 줄 알고 백엔드에도 context-path설정 해둠
- 요청이 받아지질 않음
- domain/api/~~ 이렇게 요청 -> local:8080/api/~~ 이렇게 안붙고 api가 빠진 상태로 /~~ 상태로 프록시됨

## 해결

```bash
tailscale funnel status

# Funnel on:
#     - https://kky.tail0a6d17.ts.net

https://TAILSCALEDOMAIN (Funnel on)
|-- /    proxy http://127.0.0.1:80
|-- / proxy http://127.0.0.1:8080
|-- /hls proxy http://127.0.0.1:8787
```
- 프록시 경로는 그대로 두고 백엔드 context-path를 수정했음


### 의문점

- 분명 같은 문제가 또 발생할거다. 왜냐면
```nginx configuration
server {
  listen 80;
  server_name _;
  root /shared;

  types {
    application/vnd.apple.mpegurl m3u8;
    video/mp2t ts;
  }

  # /hls/<anything>  →  /shared/<anything>
  location ^~ /hls/ { # 일치하는 패턴
    rewrite ^/hls/(.*)$ /$1 break;   # /hls 프리픽스 제거
    try_files $uri =404;

    add_header Access-Control-Allow-Origin *;
    add_header Access-Control-Allow-Methods "GET, HEAD, OPTIONS";
    add_header Access-Control-Allow-Headers "Range, Accept-Encoding, Origin, User-Agent";
    # autoindex off;
  }
}
```
- nginx에서는 프록시 경로 잡아서 를 제거하고있음
- 이것도 정상 작동함


### 다시 정리
- funnel은 prefix를 strip하고 프록시 하는게 맞음
- funnel 프록시 이후 root가 잡아서 처리해서 정상 응답
- 프록시 없이 바로 응답하면 /hls블록이 매칭되서 prefix제거 후 정상 응답