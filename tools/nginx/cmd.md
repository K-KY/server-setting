```
docker run -d \
  --name hls-player \
  -p 80:80 \
  -v /loc:/shared:ro \
  -v $PWD/nginx.conf:/etc/nginx/conf.d/default.conf:ro \
  nginx:latest

docker run -d \
  --name hls-player4 \
  -p 8787:80 \
  -v /loc:/shared:ro \
  -v .tools/nginx/nginx.conf:/etc/nginx/conf.d/default.conf:ro \
  nginx:latest

```

