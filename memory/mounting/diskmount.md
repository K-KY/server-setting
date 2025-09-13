# 디스크 마운팅
- 특히 외장 하드, 외장 SSD

## 디스크 목록  확인
````
lsblk
````

```text

NAME                         MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
sda                            8:0    0   1.8T  0 disk 
├─sda1                         8:1    0     1G  0 part /boot/efi
├─sda2                         8:2    0     2G  0 part /boot
└─sda3                         8:3    0   1.8T  0 part 
  ├─ubuntu--vg-ubuntu--lv    252:1    0   100G  0 lvm  /
 *└─ubuntu--vg-data          252:2    0   1.7T  0 lvm***  
nvme0n1                      259:0    0 238.5G  0 disk 
├─nvme0n1p1                  259:1    0     1G  0 part 
├─nvme0n1p2                  259:2    0     2G  0 part /hddd
└─nvme0n1p3                  259:3    0 235.4G  0 part 
  └─ubuntu--vg--1-ubuntu--lv 252:0    0   100G  0 lvm  
```
- 마운트 되어있지 않은 디스크 이름 확인
- 별표 되어있는거는 내가 직접 해놓은거

## 디스크 볼륨 만들기
```text
sudo lvcreate -l 100%FREE -n data ubuntu-vg
```
- 전체 사용

```text
sudo lvcreate -L 500G -n data ubuntu-vg
```
- 일부만 사용

## 파일 시스템 생성
```text
sudo mkfs.ext4 /dev/ubuntu-vg/data
```



## 마운트 할 디렉토리 생성
```text
sudo mkdir -p /mnt/data
```

## 마운트 
```text
sudo mount /dev/ubuntu-vg/data /mnt/data
```

## 마운트 잘 됐나 확인
```text
df -h | grep data
```

```text
kky@kky:~$ df -h | grep data
/dev/mapper/ubuntu--vg-data        1.7T  213G  1.4T   13% /mnt/data
```

## 지금 상태는 재부팅하면 다시 마운트 초기화됨

```text
blkid /dev/ubuntu-vg/data
```
- 디스크 UUID 나올거임
  - 민감한 정보는 아니나 외부 공개가 권장되진 않으므로 비공개

```
sudo vim /etc/fstab

UUID={UUID}  /mnt/data   ext4   defaults   0   2
```
- fstab 파일에 추가

-------
# 끝