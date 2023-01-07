---
title:  "Ubuntu Server에 ROS2 - foxy 설치하기" 
excerpt: ""

categories:
  - Blog
tags:
  - [우분투, ROS2,Ros foxy, 라즈베리파이]

toc: true
toc_sticky: true
 
date: 2023-01-06
last_modified_at: 2023-01-06

---

라즈베리파이에 설치한 Ubuntu Server(우분투 서버)에 ROS2-foxy를 설치하는 과정입니다.

먼저 Ubuntu Server는 20.04버전으로 설치한 후 ROS2를 설치합니다.

설치과정에 대한 더 자세한 정보는 [ROS 2 Documentation](https://docs.ros.org/en/foxy/Installation/Ubuntu-Install-Debians.html)을 참고해 주세요


## ROS2 설치하기	
먼저 ROS2를 편하게 설치하기 위해 데스크탑에서 ssh 접속을 통해 라즈베리파이 서버에 접속하였습니다.
접속 후 다음 명령어들을 입력합니다.

### locale 설정

```
$ locale  # check for UTF-8

$ sudo apt update && sudo apt install locales
$ sudo locale-gen en_US en_US.UTF-8
$ sudo update-locale LC_ALL=en_US.UTF-8 LANG=en_US.UTF-8
$ export LANG=en_US.UTF-8
```

### Source 설치
```
$ sudo apt update && sudo apt install curl gnupg2 lsb-release -y
$ sudo curl -sSL https://raw.githubusercontent.com/ros/rosdistro/master/ros.key  -o /usr/share/keyrings/ros-archive-keyring.gpg

$ sudo sh -c 'echo "deb [arch=$(dpkg --print-architecture)] http://packages.ros.org/ros2/ubuntu $(lsb_release -cs) main" > /etc/apt/sources.list.d/ros2-latest.list'
$ sudo apt update

```
이 과정에서 다음과 같은 오류가 발생할 수 있습니다.
```
W: GPG error: http://packages.ros.org/ros2/ubuntu focal InRelease: The following signatures couldn't be verified because the public key is not available: NO_PUBKEY F42ED6FBAB17C654
E: The repository 'http://packages.ros.org/ros2/ubuntu focal InRelease' is not signed.
N: Updating from such a repository can't be done securely, and is therefore disabled by default.
N: See apt-secure(8) manpage for repository creation and user configuration details.
```
이 경우에는 아래 명령어를 실행한 후 재시도 하면 됩니다.
```
$ sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys F42ED6FBAB17C654
```

### ROS2 설치
ROS를 우분투 서버에 설치하기 때문에 Desktop 버전을 설치할 필요가 없습니다.
따라서 다음 명령어로 ROS2를 설치합니다.
```
$ sudo apt install ros-foxy-ros-base
```

### Colcon Build 설치
ROS2에서 패키지를 빌드하기 위해 Colcon Build를 설치합니다.
아래 명령어를 통해 설치할 수 있습니다.
```
$ sudo apt update
$ sudo apt install python3-colcon-common-extensions
```
<hr></hr>

### Alias 설정
Alias를 설정함으로 긴 명령어를 하나의 단축어로 설정할 수 있습니다.
아래 명령어로 bashrc파일을 열어줍니다.
```
$ sudo nano ~/.bashrc
```
파일이 열렸으면 파일의 최하단에 다음을 추가합니다.
```
alias rosfoxy='source /opt/ros/foxy/setup.bash'

```
파일을 저장한 후, 다음 명령어로 bashrc를 적용할 수 있습니다.

```
$ source .bashrc

```
이제 터미널에 rosfoxy를 입력함으로 ros를 실행할 수 있습니다.
