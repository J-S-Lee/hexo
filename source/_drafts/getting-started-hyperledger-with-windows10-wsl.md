---
title: Hyperledger 개발 환경 구축하기
date: 2018-08-11 15:48:28
categories: 
- Note
tags: 
- Hyperledger
- WSL
- Ubuntu
---

# Hyperledger 개발 환경 구축하기

WSL(Windows Subsystem for Linux) 환경에서 `Hyperledger Composer` 실행 환경 구축하는 과정을 정리하는 글



[TOC]





### Hyperledger Composer & Fabric 설치에 필요한 개발 툴

- OS : Ubuntu Linux 14.04 / 16.04 LTS (both 64-bit), or Mac OS 10.12
- Docker Engine : Version 17.03 or higher
- Docker-Compose : Version 1.8 or higher
- Node : 8.9 or higher (version 9 is not supported)
- npm : v5.x
- git : 2.9.x or higher
- Python : 2.7.x
- Editor : A code editor of user choice, recommend VSCode



### 1. 윈도우10에 Ubuntu 설치하기




#### Microsoft 스토어에서 우분투 다운로드

윈도우  Microsoft 스토어에서 `Ubuntu`를 설치해야 한다.

![MS Store](C:\SMYSoft\SMemo\capture\20180811013102.png)



![다운로드](C:\Users\LeeJaeSeok\blog\source\images\wsl\download_ubuntu.png)



다운로드 완료 후 설치된 `Ubuntu`를 실행한 모습이다.

![install 성공](C:\Users\LeeJaeSeok\blog\source\images\wsl\install_success.png)



#### 오류 발생시

![install_failed](C:\Users\LeeJaeSeok\blog\source\images\wsl\install_failed.png)

`0x08007007e`, `0x8007019e` 등의 오류가 발생할 경우 다음 항목을 체크해줘야 한다.

`제어판` > `프로그램 추가제거` > `windows 기능 켜기/끄기` >  `Linux용 Windows 하위 시스템` 

 

![Windows 기능 켜기](C:\Users\LeeJaeSeok\blog\source\images\wsl\windows_function_onoff.png)



해당 기능을 켠 후 다시 `Ubuntu`를 설치하면 오류없이 진행되며 `username`, `password`를 설정하면 설치가 완료된다.

![우분투 설치 성공](C:\Users\LeeJaeSeok\blog\source\images\wsl\install_success.png)



#### 우분투 계정 만들기

```bash
adduser hyper
```



계정을 만든 후 sudo 권한 부여

```bash
sudo visudo # vi /etc/sudoers 등으로 직접 접근하여 수정하지 말고 이 방법을 쓰자
```

```bash
....

# User privilege specification
사용자		호스트		명령어
root	ALL=(ALL:ALL) ALL
# 이부분에 아래와 같이 추가하여 새로 만든 계정에도 권한을 준다.
# hyper 사용자에게 모든 호스트에 대해서 모든 명령어 실행 권한 부여
hyper	ALL=(ALL:ALL) ALL 
# Members of the admin group may gain root privileges
....
```



수정 내용을 저장하기 위해서는 아래 보이는 `^X` Exit 를 선택하면 된다.

![sudo visudo](C:\Users\LeeJaeSeok\blog\source\images\wsl\1251251235.png)

^ 는 키보드의 `ctrl`키를 의미하므로 `ctrl + X`를 누르면 변경사항을 저장할건지 묻는다.

![20180810171351](C:\Users\LeeJaeSeok\blog\source\images\wsl\20180810171351.png)

`tmp`가 아닌 본래 파일을 수정해야 하므로 File Name을 `/etc/sudoers`로 수정하고 엔터.

![20180810171629](C:\Users\LeeJaeSeok\blog\source\images\wsl\20180810171629.png)





### 2. 개발 환경 설치



> **Ubuntu에 Hyperledger Composer를 설치할 경우 아래 사항들을 기억하자.**
>
> - Login as a *normal user*, rather than root
> - *Do not* `su` to root.
> - When installing prerequisites, use `curl`, then unzip using `sudo`.
> - Run `prereqs-ubuntu.sh` as a *normal user*. It may prompt for root password as some of it's actions are required to be run as root.
> - Do not use `npm` with `sudo` or `su` to root to use it.
> - Avoid installing `node` *globally* as root.



[위 사항들에 주의하며 설치과정을 잘 정리해주신 블로그](https://d2fault.github.io/2018/04/30/20180430-setting-hyperledger-developement-environment/)가 있어 설치 과정을 참고하였다.



#### prerequisites 다운로드

위에 명시된 `hyperledger composer` 설치를 위해 필요한 시스템 패키지를 다운로드한다.

```bash
curl -O https://hyperledger.github.io/composer/latest/prereqs-ubuntu.sh
chmod u+x prereqs-ubuntu.sh
./prereqs-ubuntu.sh
```



패키지별 버전 확인을 통해 잘 설치되었는지 체크한다.

```bash
cat /etc/issue #Ubuntu version check

nodejs -v # node version check

npm -v # npm version check

git --version # git version check

python --version # python version check
```



### 3. 컴포넌트 설치

`composer-cli`는 필수 CLI 툴이고, 나머지는 개발 환경 구성에 꼭 필요한 부분은 아니다.

하지만 [공식 사이트](https://hyperledger.github.io/composer/latest/installing/development-tools.html)에서는 튜토리얼을 진행하거나 어플리케이션 개발을 할 때 유용하므로 함께 설치할 것을 권장하고 있다.

#### CLI 툴 설치

1. 필수 CLI 툴

    ```bash
    npm install -g composer-cli@0.20
    ```

2. RESTful API Server를 띄워주는 기능

    ```bash
    npm install -g composer-rest-server@0.20
    ```

3. 어플리케이션 assets 생성에 유용

    ```bash
    npm install -g generator-hyperledger-composer@0.20
    ```

4. `generator-hyperledger-composer` 같은 기능을 이용한 어플리케이션 생성을 위한 툴 Yeoman

    ```bash
    npm install -g yo
    ```



#### Playground 설치

1. Playground - 간단한 수정, 테스팅을 브라우저 UI 환경에서 제공

   ```bash
   npm install -g omposer-playground@0.20
   ```

   

#### Hyperledger Fabric 설치

```bash
mkdir ~/fabric-dev-servers && cd ~/fabric-dev-servers

curl -O https://raw.githubusercontent.com/hyperledger/composer-tools/master/packages/fabric-dev-servers/fabric-dev-servers.tar.gz
tar -xvf fabric-dev-servers.tar.gz
```



```bash
cd ~/fabric-dev-servers
export FABRIC_VERSION=hlfv12
./downloadFabric.sh
```



#### Hyperledger Fabric 시작/종료

런타임을 처음 시작하는 경우 start 스크립트 실행 후 PeerAdmin card를 생성해야 한다.

```bash
cd ~/fabric-dev-servers
export FABRIC_VERSION=hlfv12
./startFabric.sh
./createPeerAdminCard.sh
```

이후 런타임을 시작하거나 정지시킬 때는 `~/fabric-dev-servers/stopFabric.sh`, `~/fabric-dev-servers/startFabric.sh`을 이용하면 된다.



개발 세션을 종료할 경우엔 `~/fabric-dev-servers/stopFabric.sh` 실행 후에 `~/fabric-dev-servers/teardownFabric.sh`를 사용하면 된다. 만약 teardown 스크립트를 실행한다면, 다음에 런타임을 시작할 때 처음 런타임 시작과 같이 새로운 PeerAdmin card를 생성해줘야한다.



#### Playground 실행

playground web app을 실행하는 방법

```bash
composer-playground
```

브라우저가 다음의 주소를 갖고 자동으로 열린다. http://localhost:8080/login

"My Business Networks" 화면에서  `createPeerAdminCard` 스크립트를 통해 생성한 `PeerAdmin@hlfv1` 카드가 보이지 않는다면 런타임이 정상적으로 시작되지 않은 것이다.





### 참고자료

- [[Hyperledger] 개발 환경 구축하기(설치) | 잡동사니 수집광](https://d2fault.github.io/2018/04/30/20180430-setting-hyperledger-developement-environment/)
- [Installing pre-requisites | Hyperledger ](https://hyperledger.github.io/composer/latest/installing/installing-prereqs.html)
- [Installing development-tools | Hyperledger](https://hyperledger.github.io/composer/latest/installing/development-tools.html)

