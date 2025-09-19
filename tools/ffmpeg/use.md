# FFmpeg 사용



## mkv To hls
```
sudo mkdir -p {location} && docker run --rm -v "$PWD":/work -w /work \
linuxserver/ffmpeg:version-8.0-cli -i {fileName} -codec copy \
-start_number 0 -hls_time 10 -hls_list_size 0 -f hls {location/result.m3u8}
```

## cut time to time
```
docker run --rm -v "$PWD":/work -w /work linuxserver/ffmpeg:version-8.0-cli \
  -ss {startHH:MM:DD} -to {endHH:MM:DD}
  -i filename.mkv 
  -c copy 1.mkv
  
```