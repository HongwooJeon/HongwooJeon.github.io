---
title: 4. Container File 설정
author: Banjung
date: 2022-04-19 08:00:00 -0500
categories: [Docker, Chapter 3(컨테이터)]
tags: [Docker Container 파일 설정]
math: true
mermaid: true
---

## 0. Summary

```
## 컨테이너로부터 이미지 생성
> docker container run -it --name c1 centos
> docker container commit c1 centos:hello

## 컨테이너를 tar 파일로 출력
> docker attach c1
# echo "This is export test" > export.txt
# cat export.txt
> docker container export -o ./testexport.tar c1
> tar tf testexport.tar | findstr export.txt

## 아카이브 파일로 이미지 만들기
> docker image import testexport.tar export:test

## 이미지 저장
> docker image save -o imgarc.tar centos
> attrib imgarc.tar

## 이미지 불러오기
> docker image load -i imgarc.tar

## 컨테이너 파일 복사
> docker container cp dockercp.txt c1:/
> docker container exec -it c1 cat /dockercp.txt

## 컨테이너 파일 변경 확인
> docker container diff c1
```

## 1. Container로 부터 이미지 작성
`docker container commit [OPTIONS] CONTAINER [REPOSITORY[:TAG]]`

- `docker contaienr commit` 명령을 사용하여 Container를 새오루 이미지로 생성할 수 있다.

```
> docker container commit --help
Usage:  docker commit [OPTIONS] CONTAINER [REPOSITORY[:TAG]]
Create a new image from a container's changes
Options:
  -a, --author string    Author (e.g., "John Hannibal Smith
                         ")
  -c, --change list      Apply Dockerfile instruction to the created image
  -m, --message string   Commit message
  -p, --pause            Pause container during commit (default true)
```

- repository와 tag를 지정하지 않으면 Dangle Image가 생성된다. (권장 x)
  - ex> c1이미지를 생성한다.

```
> docker container ls -a
CONTAINER ID        IMAGE               COMMAND              CREATED             STATUS                      PORTS               NAMES
7ab5c4764a79        centos              "/bin/bash"          5 hours ago         Up 5 hours                                      c1
> docker container commit c1
sha256:cde75dbdf4f46bfc6cc31efcf4034d0577ef3218c9f9b48d2bc28250813d7c12
> docker image ls
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
                            cde75dbdf4f4        4 seconds ago       200MB
```

- ex> c1 이미지를 centos:hello라는 이름의 이미지로 생성한다.

```
> docker container commit c1 centos:hello
sha256:8e371c761123fdb3646356e5336ff174bbba2ba86971d038b75f155e989d0350
> docker image ls
REPOSITORY          TAG                 IMAGE ID            CREATED              SIZE
centos              hello               8e371c761123        6 seconds ago        200MB
                            cde75dbdf4f4        About a minute ago   200MB
```

## 2. Container를 tar 파일로 출력
`docker container export [OPTION] CONTAINER`

- ex> c1 Container를 생성하고 텍스트 파일을 생성한 뒤 docker export 명령을 실행한다.
  - 아카이브 파일 중에 Container에 생성한 export.txt 파일이 존재한다.

```
> cd C:\Users\UserName\temp
> docker container run -it --name c1 centos
# echo "This is export test" > export.txt
# cat export.txt
This is export test
[Ctrl + P + Q]
> docker container export -o testexport.tar c1
> tar tf testexport.tar | findstr export.txt
export.txt
```  

## 3. 아카이브 파일로 이미지 만들기
`docker image import [OPTIONS] file|URL|- [REPOSITORY[:TAG]]`

- ex> 위에서 생성한 testexport.tar 파일로 이미지를 생성한다.

```
> cd C:\Users\UserName\temp
> docker image ls
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
httpd               latest              0240c8f5816c        2 days ago          132MB
centos              latest              75835a67d134        12 days ago         200MB
hello-world         latest              4ab4c602aa5e        6 weeks ago         1.84kB
> docker image import testexport.tar export:test
sha256:fc31567e00eb5422899e8405d82d2e909de6f24bbe71effae9a4dda12aebbf55
> docker image ls
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
export              test                fc31567e00eb        5 seconds ago       200MB
httpd               latest              0240c8f5816c        2 days ago          132MB
centos              latest              75835a67d134        12 days ago         200MB
hello-world         latest              4ab4c602aa5e        6 weeks ago         1.84kB
```

## 4. 이미지 저장
`docker image save [OPTIONS] IMAGE [IMAGE...]`

- docker save 명령으로 이미지를 아카이브로 저장할 때 반드시 `-o` 옵션을 사용하여 경로를 지정한다.
- ex> centos hello-world httpd 이미지를 아카이브 파일로 저장한다.

```
> cd C:\Users\UserName\temp
> docker image ls
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
httpd               latest              0240c8f5816c        2 days ago          132MB
centos              latest              75835a67d134        12 days ago         200MB
hello-world         latest              4ab4c602aa5e        6 weeks ago         1.84kB
> docker image save -o imgarc.tar centos
> attrib imgarc.tar
A                    C:\Users\UserName\imgarc.tar
```

## 5. 이미지 불러오기
`docker image load [OPTIONS]`

- docker save 명령으로 저장한 아카이브 파일을 불러올 수 있다.
- docker load 명령으로 이미지를 불러올 때 반드시 `-i` 옵션을 사용하여 경로를 지정한다.
- ex> imgarc.tar 파일을 로드한다.

```
> cd C:\Users\UserName\temp
> docker image ls
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
centos              latest              75835a67d134        12 days ago         200MB
> attrib imgarc.tar
imgarc.tar: POSIX tar archive
> docker image load -i imgarc.tar
Loaded image: centos:latest
428c97da766c: Loading layer  3.584kB/3.584kB
Loaded image: hello-world:latest
237472299760: Loading layer  58.44MB/58.44MB
504b6a6a6fd2: Loading layer   2.56kB/2.56kB
355bd981febe: Loading layer  34.57MB/34.57MB
0ace1cc25781: Loading layer  43.58MB/43.58MB
7857c7afb135: Loading layer  3.584kB/3.584kB
Loaded image: httpd:latest
> docker image ls
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
httpd               latest              0240c8f5816c        2 days ago          132MB
centos              latest              75835a67d134        12 days ago         200MB
hello-world         latest              4ab4c602aa5e        6 weeks ago         1.84kB
```

#### (export/import) 와 (save/load)의 차이

|Command|결과|사용처|소스|
|------|---------|---------|-------|
|export|tar File|Container File System|Container|
|import|Docker Image|Flat File System|tar File|
|save|tar File|Docker Image|Image|
|load|Docker Image|Docker Image|tar File|

- Flat File System: 하위 디렉토리 없이 파일들을 포함하는 하나의 디렉토리
- export와 import는 플랫 파일 시스템에서 동작한다.
- export는 Container 상태를 구성하는 tar파일을 생성한다. Docker와 마찬가지로 프로세스 실행 상태는 저장되지 않는다.
- 즉, 파일만 저장한다.
- import는 tar파일에서 히스토리 or 메타 데이터가 없는 docker image를 생성한다.

## 6. Container 파일 복사
Host -> Container: `docker container cp [OPTIONS] CONTAINER:SRC_PATH DEST_PATH|-`

Container -> Host: `docker container cp [OPTIONS] SRC_PATH|- CONTAINER:DEST_PATH`

- ex> host에 있는 dockercp.txt 파일을 c1 Container로 복사한다.

```
> docker container run -itd --name c1 centos
e9b448f07d99af238fd6dfb050d761ed423b9f0996abb4eae00658d20f749cf2
> docker container ls
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
e9b448f07d99        centos              "/bin/bash"         3 seconds ago       Up 2 seconds                            c1
> docker container cp dockercp.txt c1:/
> docker container exec -it c1 cat /dockercp.txt
This is "docker cp" Test
```

- ex> c1 Container에 있는 dockercp.txt파일을 host로 복사한다.

```
> docker cp c1:/dockercp.txt .
> cat dockercp.txt
This is "docker cp" Test
```

## 7. Container 파일 변경 확인
`docker container diff CONTAINER`

|구분|설명|
|------|------|
|A|파일 추가|
|D|파일 삭제|
|C|파일 수정|

- ex> webserver 이름의 Container에서 Container 조작 변경을 확인한다.

```
> docker container diff webserver
C /usr
C /usr/local
C /usr/local/apache2
C /usr/local/apache2/logs
A /usr/local/apache2/logs/httpd.pid
```

- ex> centos 이미지를 c1으로 Container화 한 뒤 dira/filea.txt파일 생성 뒤 anaconda-post.log를 삭제한다.

```
> docker container run -it --name c1 centos
# mkdir dira
# echo "This is filea" > dira/filea.txt
# ls
anaconda-post.log  dev   etc   lib    media  opt   root  sbin  sys  usr
bin                dira  home  lib64  mnt    proc  run   srv   tmp  var
# rm -f anaconda-post.log
[Ctrl + P + Q]
> docker container diff c1
A /dira
A /dira/filea.txt
D /anaconda-post.log

```