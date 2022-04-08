---
title: 2. Image 관리
author: Banjung
date: 2022-04-07 08:00:00 -0500
categories: [Docker, Chapter 2(이미지 관리))]
tags: [Image Manage]
math: true
mermaid: true
---

## Docker Image
```
## 이미지 검색
> docker search hello-world

## 즐겨찾기 수 1000이상의 이미지 검색
> docker search centos -s 1000
> docker search -f stars=1000 centos

## 이미지 다운로드
> docker image pull centos:7

## 이미지 목록 표시
> docker image ls
> docker images

## 이미지 상세 정보 확인
> docker image inspect centos:7

## 이미지 태그 설정
> docker image tag nginx mirero/webserver:1.0

## 이미지 삭제
> docker rmi hello-world
> docker image rm hello-world
> docker image rm -f 9f
> docker image prune

## 이미지 업로드
> docker login
> docker tag centos:latest jh0105123/centos:v2
> docker image push jh0105123/centos:v2
> docker logout
```

## Docker Hub
- https://hub.docker.com/
- Docker Hub는 GitHub나 Bitbucket과 같은 소스코드 관리 툴과 연계하여 코드를 빌드하는 기능이나 실행 가능한 애플리케이션의 이미지를 관리하는 기능을 갖춘 docker의 공식 레포지토리 서비스이다.
- Docker Hub를 사용하여 물리 서버든, 가상 머신이든, 클라우드든 Docker이미지를 배포할 수 있다.
- Docker Hub 사이트의 검색 키워드에 임의의 문자를 입력하여 검색하면 등록되어 있는 Docker의 이미지의 목록이 표시된다.

## 이미지 검색
`docker search [Options] 이미지 키워드`
- Docker Hub에 공개된 이미지를 검색할 때는 docker search 명령을 사용한다.
- Docker Certified로고는 Image에 대한 품질, 출처 및 지원에 대한 보증을 제공한다.
```
> docker search --help
Usage:  docker search [OPTIONS] TERM
Search the Docker Hub for images
Options:
  -f, --filter filter   Filter output based on conditions provided
      --format string   Pretty-print search using a Go template
      --limit int       Max number of search results (default 25)
      --no-trunc        Don't truncate output    
```
- ex> Hello-world 키워드를 포함한 이미지를 검색한다.

```
> docker search hello-world
NAME                                       DESCRIPTION                                     STARS               OFFICIAL            AUTOMATED
hello-world                                Hello World! (an example of minimal Dockeriz…   694                 [OK]
kitematic/hello-world-nginx                A light-weight nginx container that demonstr…   111
tutum/hello-world                          Image to test docker deployments. Has Apache…   56                                      [OK]
... <생략>
uniplaces/hello-world                                                                      0
lkungs/docker-hello-world                  Simple Hello World Example                      0                                       [OK]
```

- STARS 필드는 즐켜찾기 수이다.
  - -s 옵션을 사용하면 일정 개수 이상의 STAR를 부여받은 이미지를 검색할 수 있다.
  - ex> 1000개 이상의  STAR를 부여받은 centos 키워드의 이미지를 검색

```
  > docker search centos -s 1000
Flag --stars has been deprecated, use --filter=stars=3 instead
NAME                DESCRIPTION                     STARS               OFFICIAL            AUTOMATED
centos              The official build of CentOS.   4834                [OK]
```

```
> docker search -f stars=1000 centos
Flag --stars has been deprecated, use --filter=stars=3 instead
NAME                DESCRIPTION                     STARS               OFFICIAL            AUTOMATED
centos              The official build of CentOS.   6541                [OK]
```

|항목|설명| 
|---------|---------|
|NAME|이미지 이름|
|DESCRIPTION|이미지 설명|
|STARS|즐겨찾기 수|
|OFFICIAL|공식 이미지 사실 여부(공식이면 [OK])|
|AUTOMATED|Dockerfile을 바탕으로 자동 생성된 이미지 여부|

- `--limit` 옵션으로 최대 검색 건수를 제한할 수도 있다.
  - 검색 결과는 STARS순으로 출력된다.
  - ex> mysql 이미지 중 상위 5개를 출력한다.
    - 첫번째 출력되는 mysql 리포지토리 이름에는 namespace가 생략되어 있는데 이는 공식 리포지토리이기 때문이다.
    - 공식 리포지토리의 네임스페이스는 일률적으로 library이며 생략할 수 있다.

```
> docker search --limit=5 mysql
NAME                  DESCRIPTION                                     STARS               OFFICIAL            AUTOMATED
mysql                 MySQL is a widely used, open-source relation…   8250                [OK]
mysql/mysql-server    Optimized MySQL Server Docker images. Create…   616                                     [OK]
mysql/mysql-cluster   Experimental MySQL Cluster Docker images. Cr…   45
bitnami/mysql         Bitnami MySQL Docker Image                      27                                      [OK]
circleci/mysql        MySQL is a widely used, open-source relation…   11
```

## 이미지 다운로드(image pull)
`docker image pull [OPTIONS] NAME[:TAG|@DIGEST]`
- Docker Hub에서 이미지 다운로드는 docker image pull 명령을 사용한다.

```
> docker image pull --help  
Usage:  docker pull [OPTIONS] NAME[:TAG|@DIGEST]
Pull an image or a repository from a registry
Options:
  -a, --all-tags                Download all tagged images in the repository
      --disable-content-trust   Skip image verification (default true)  
```

- Tag는 각 이미지의 기능이나 버전이다.
  - ex> CentOS의 버전 7을 다운로드한다.

```
> docker image pull centos:7
7: Pulling from library/centos
Digest: sha256:b5e66c4651870a1ad435cd75922fe2cb943c9e973a9673822d1414824a1d0475
Status: Downloaded newer image for centos:7
```

- 태그를 지정하지 않으면 자동으로 lastest 태그가 지정된다.
  - ex> Docker에서 공식으로 제공하는 centos:lastest 이미지를 다운로드한다.

```
> docker image pull centos
Using default tag: latest
latest: Pulling from library/centos
aeb7866da422: Pull complete
Digest: sha256:67dad89757a55bfdfabec8abd0e22f8c7c12a1856514726470228063ed86593b
Status: Downloaded newer image for centos:latest
```

- -a 옵션을 지정하면 모든 태그를 취든할 수 있다.
  - ex> centos의 모든 태그 이미지를  취득한다.

```
> docker image pull -a centos
```

- DIGEST: 각 이미지가 가지고 있는 해시 값이다.

## 이미지 목록표시(image ls)
`docker image ls`

`docker images`

- Docker Hub에서 다운로드하여 로컬에 저장되어 있는 이미지의 목록을 확인하려면 위의 명령을 사용한다.
- docker images는 이전 명령어이다.

```
> docker image ls --help
Usage:  docker image ls [OPTIONS] [REPOSITORY[:TAG]]
List images
Aliases:
  ls, images, list
Options:
  -a, --all             Show all images (default hides intermediate images)
      --digests         Show digests
  -f, --filter filter   Filter output based on conditions provided
      --format string   Pretty-print images using a Go template
      --no-trunc        Don't truncate output
  -q, --quiet           Only show numeric IDs
```

- ex> Docker 이미지 목록을 표시한다.

```
> docker image ls
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
centos              latest              75835a67d134        12 days ago         200MB 
```

- Docker 이미지를 작성하면 고유한 이미지 ID가 부여된다. 이미지 ID는 랜덤하다.

|항목|설명|
|------|------|
|REPOSITORY|이미지 이름|
|TAG|이미지 태그명|
|IMAGE ID|이미지 ID|
|CREATED|작성 일|
|SIZE|이미지 크기|

## 이미지 상세 정보 확인(image inspect)
`docker image inspect [OPTIONS] IMAGE [IMAGE...]`

- 이미지 상세 정보를 확인하려면 `docker image inspect` 명령을 사용한다.

```
> docker image inspect --help
Usage:  docker image inspect [OPTIONS] IMAGE [IMAGE...]
Display detailed information on one or more images
Options:
  -f, --format string   Format the output using the given Go template
```

- ex> centos:7 이라는 이미지 상세정보를 확인한다.

```
> docker image inspect centos:7
[
    {
        "Id": "sha256:9f38484d220fa527b1fb19747638497179500a1bed8bf0498eb788229229e6e1",
...<생략>...
        "Created": "2019-03-14T21:19:53.361167852Z",
...<생략>...      
        "DockerVersion": "18.06.1-ce",
...<생략>...  
        "Architecture": "amd64",
]    
```

- 결과는 JSON형식으로 표시된다.
- --format 옵션을 사용하여 원하는 데이터 값만 출력할 수 있다.
  - ex> centos:7의 os 정보만 출력한다.

```
> docker image inspect --format="{{ .Os}}" centos:7
linux 
```

## 이미지 태그 설정(image tag)
`docker image tag SOURCE_IMAGE[:TAG] TARGET_IMAGE[:TAG]`

- Docker 이미지에 붙는 태그는 `이미지의 특정 버전을 구별` 하기 위한 것이다.
- 애플리케이션을 수정하고 이미지를 빌드하면 매번 다른 이미지가 된다.
- 원래 같은 이미지였지만, 수정 후에는 다른 IMAGE ID값이 할당된다.

```
> docker image tag --help
Usage:  docker image tag SOURCE_IMAGE[:TAG] TARGET_IMAGE[:TAG]
Create a tag TARGET_IMAGE that refers to SOURCE_IMAGE
```

## 이미지 삭제(image rm)
`docker image rm [OPTIONS] IMAGE [IMAGE...]`

- `작성한 이미지를 삭제하려면 docker image rm 명령`을 사용한다.

```
> docker image rm --help
Usage:  docker image rm [OPTIONS] IMAGE [IMAGE...]
Remove one or more images
Options:
  -f, --force      Force removal of the image
      --no-prune   Do not delete untagged parents
```

- ex> hello-world 이미지를 삭제한다.

```
> docker image ls
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
hello-world         latest              4ab4c602aa5e        6 weeks ago         1.84kB
> docker image rm hello-world
Untagged: hello-world:latest
Untagged: hello-world@sha256:0add3ace90ecb4adbf7777e9aacf18357296e799f81cabc9fde470971e499788
Deleted: sha256:4ab4c602aa5eed5528a6620ff18a1dc4faef0e1ab3a5eddeddb410714478c67f
Deleted: sha256:428c97da766c4c13b19088a471de6b622b038f3ae8efa10ec5a37d6d31a2df0b
>  docker image ls
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
```

- 여러 개의 이미지를 삭제하고 싶을 때는 여러 이미지명을 스페이스로 구분하여 지정한다.
- -f 옵션으로 Image ID가 같은 여러개의 이미지를 동시에 삭제할 수 있다.
  - ex> Image ID가 9f3으로 시작하는 2개의 이미지를 삭제한다.

```
> docker image ls
REPOSITORY          TAG      IMAGE ID            CREATED             SIZE
centos              v2       9f38484d220f        2 months ago        202MB
centos              latest   9f38484d220f        2 months ago        202MB
> docker image rm -f 9f
Untagged: jh0105123/centos:v2
Untagged: centos:latest
Untagged: centos@sha256:b5e66c4651870a1ad435cd75922fe2cb943c9e973a9673822d1414824a1d0475
Deleted: sha256:9f38484d220fa527b1fb19747638497179500a1bed8bf0498eb788229229e6e1
Deleted: sha256:d69483a6face4499acb974449d1303591fcbb5cdce5420f36f8a6607bda11854
> docker image ls
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
```

`docker image prune [OPTIONS]`

- `사용하지 않은 Docker 이미지를 삭제할 때는 docker image prune 명령`을 사용한다.

```
> docker image prune --help
Usage:  docker image prune [OPTIONS]
Remove unused images
Options:
  -a, --all             Remove all unused images, not just dangling ones
      --filter filter   Provide filter values (e.g. 'until=')
  -f, --force           Do not prompt for confirmation
```

- `사용하지 않은 Docker 이미지는 디스크 용량을 차지하기 때문에 정기적으로 삭제하는 것이 좋다.`

## 이미지 업로드(image push)
- Docker Hub에 가입한다. [Docker Hub](https://hub.docker.com/)
- Docker Hub 로그인한다.: `docker login`

```
> docker login
Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com to create one.
Username: banjung
Password:
Login Succeeded
```

- 사용자가 제작한 이미지를 Docker Hub에 업로드 하려면 docker tag를 사용하여 `사용자 ID/Repository:tag` 형식으로 이미지 이름을 변경해야한다.

```
> docker image tag centos:latest banjung/centos:v2
```

`docker push [OPTIONS] NAME[:TAG]`

- Docker Hub에 이미지를 업로드하려면 `docker image push 명령`을 사용한다.

```
> docker image push --help
Usage:  docker push [OPTIONS] NAME[:TAG]
Push an image or a repository to a registry
Options:
      --disable-content-trust   Skip image signing (default true)
```

- `docker logout명령` 으로 로그아웃하여 종료한다.

