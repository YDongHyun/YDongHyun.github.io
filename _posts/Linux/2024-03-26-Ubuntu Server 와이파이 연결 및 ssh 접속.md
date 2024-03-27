---
title:  "[라즈베리파이]Ubuntu Server 와이파이 연결 및 ssh 접속" 
excerpt: ""

categories:
  - Linux
tags:
  - [라즈베리파이, ssh]

toc: true
toc_sticky: true
 
date: 2024-03-26
last_modified_at: 2023-03-26

---

라즈베리파이에 Ubuntu 20.04 Server를 설치한 후, 와이파이를 설정하는 방법입니다.

연결 방법에는 2가지가 있습니다.

1. PC를 통한 network-config파일 수정
2. 라즈베리파이를 통해 network-config 수정

먼저 유선랜이 연결되지 않은 경우부터 시작하겠습니다.

## PC를 통한 network-config파일 수정

유선랜이 없는 경우는 Ubuntu Server가 설치된 SD카드를 PC에 연결합니다.

그러면  `system-boot` 라는 저장소가 나타납니다.

이 저장소에 이동한 후, `network-config` 파일을 찾습니다.
![image](https://user-images.githubusercontent.com/80799025/211032203-c028fa4c-dd57-45f8-9c3f-8f7ad611b490.png)


그 후 파일을 열어 (Window 환경의 경우는 메모장으로 실행) 파일을 수정합니다.

다음 부분에 내용을 수정해줍니다.

**wifis부터 password까지의 주석을 제거해야합니다.**

**그리고 파일 수정시 tab키 대신 space를 통해 수정을 해야합니다.** 

(여기서 들여쓰기는 한단당 2칸 띄어쓰기를 사용합니다.)

```xml
version: 2
wifis:
  wlan0:
    dhcp4: true
    optional: true
    access-points:
      "Your WiFi SSID":
        password: "Your WiFi Password"
```

Your WiFi 부분에 와이파이의 이름, 패스워드를 따옴표 안에 입력해 줍니다.

참고로 라즈베리파이에 5G 와이파이를 찾지 못할수도 있으니 주의해주세요.

적용을 완료 한 후 SD카드를 삽입 한 후 라즈베리 파이를 실행합니다.

## 라즈베리파이를 통해 network-config 수정

라즈베이파이에서 nano를 통해 파일을 수정합니다.
먼저 무선랜 관련 프로그램을 설치합니다.

 만약 유선랜이 있다면 다음을 진행하면 됩니다. (필수 X)
```xml
$ sudo apt install wireless-tools
$ sudo apt update
```

그후 아래 명령어를 이용해 사용 가능한 와이파이를 찾습니다.

```xml
$ sudo iwlist wlan0 scan | more
```


사용 가능한 WiFi를 알아냈다면 다음을 수행합니다.
사용 가능한 WiFi의 ESSID를 기억한 후 아래 명령어를 입력합니다.

```xml
$ sudo nano /etc/netplan/50-cloud-init.yaml
```

파일이 열렸으면 다음과 같이 파일을 수정해줍니다.

```
network:
    ethernets:
        eth0:
              dhcp4: true
              optional: true
    version:2
    wifis:
      wlan0:
        dhcp4: true
        optional: true
        access-points:
          "Your WiFi SSID":
            password: "Your WiFi password"
```

여기서 한 단은 4번 띄어쓰기 입니다. space를 이용하여 띄어쓰를 해주시면 됩니다.

모두 작성하였다면 `Ctrl+X` 를 입력 후 저장합니다.

이제 아래 명령어를 입력하여 적용합니다.

```xml
$ sudo netplan apply
```

이제 와이파이가 잘 연결되었는지 확인합니다.

ping을 이용하거나 아래 명령어로 확인 가능합니다.

```xml
$ iw wlan0 link
```

정상적으로 적용되었다면 와이파이의 정보가 출력될 것입니다.

## ssh 설정

ssh설정을 위해 먼저 아래 명령어를 입력해줍니다.

```xml 
$ sudo apt install openssh-server
```

그 후 아래 명령어로 ssh서버를 실행합니다.

```xml
$ sudo systemctl status ssh
```

명령어를 실행하면 정보들이 나옵니다.

중간에 초록색 글씨로  **active (running)**가 출력되었다면 성공입니다.

만약 실행이 안된다면 enable 후 다시 실행시킵니다.

이제 아이피를 확인합니다.

```xml
$ ip a
```

위 명령어를 실행시킨 후 `wlan0` 부분을 찾습니다.

여기서 `inet` 뒤에 있는 아이피를 기억합니다.

### 클라이언트 접속

클라이언트 접속은 아래 명령어를 이용합니다.

```xml
$ ssh username@ip
```

명령어를 입력한 후 yes를 입력하고 서버의 비밀번호를 입력하면 접속이 됩니다.
![image](https://user-images.githubusercontent.com/80799025/211032324-2533117a-c9f1-46b8-afb4-aef09599a8f2.png)

최종적으로 ssh 접속이 된것을 확인하였습니다.

다음에는 라즈베리파이 Ubuntu Server에 ROS2를 설치보도록 하겠습니다.
