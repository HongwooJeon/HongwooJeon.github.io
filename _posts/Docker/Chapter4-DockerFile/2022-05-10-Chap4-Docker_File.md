---
title: 1. Docker File
author: Banjung
date: 2022-04-10 08:00:00 -0500
categories: [Docker, Chapter 4(DockerFile)]
tags: [Docker File]
math: true
mermaid: true
---

## 1. Dockerfile 이란?
- Dockerfile은 Docker 상에서 작동시킬 `Container의 구성정보를 기술하기 위한 파일`이다.
- 필수는 아니지만 Dockerfile을 제작할 때에는 디렉토리를 생성한 뒤 해당 디렉토리에 접근해서 사용할 것을 권장한다.
- Dockerfile에는 다음의 기능들이 정의될 수 있다.
  - Base가 될 Docker 이미지
  - Docker Container 안에서 수행한 조작
  - 환경변수 등의 설정
  - Docker Container안에서 작동시켜 둘 데몬 실행

## 2. Dockerfile 기본 구성
- Dockerfile은 텍스트 형식의 파일로 에디터 등을 사용하여 작성한다.
- 명령은 대문자, 소문자 상관없지만 관례적으로 대문자를 사용한다.
- Dockerfile에서 사용하는 주요명령을 소개한다.

|명령어|설명|명령어|설명|
|------|-------|---------|---------|
|FROM|베이스 이미지 지정|ADD|파일 추가|
|MAINTAINER|작성자 지정|COPY|파일 복사|
|RUN|명령어 실행|VOLUME|볼륨 마운트|
|CMD|데몬 실행|ENTRYPOINT|데몬 실행|
|LABEL|라벨 설정|USER|사용자 설정|
|EXPOSE|포트 내보내기|WORKDIR|작업 디렉토리 지정|
|ENV|환경 변수 설정|ONBUILD|build 후 실행 명령|

## 3. 베이스 이미지 지정(FROM)
- Dockerfile에는 `Docker Container를 어떤 Docker 이미지로부터 생성`할지 정보를 반드시 기술해야 한다.
- 이와 같은 이미지를 `베이스 이미지`라고 한다.

```
FROM IMAGE

FROM IMAGE:TAG

FROM IMAGE@DIGEST
```

- FROM 명령은 `필수적` 이다.
- 만약 해당 이미지가 없으면 서버 저장소(Repository)에서 다운로드 받는다.
- ex> CentOS를 베이스 이미지로 한 Dockerfile

```
FROM centos:centos7
```

- 태그명을 생략하면 베이스 이미지의 최신버전이 적용된다.
- 이미지명이나 태그명은 작성자가 임의의 값을 붙일수 있기 때문에 Dockerfile을 수정해도 똑같은 이름으로 몇 번이든 이미지를 만들수 있다.
- 이미지를 고유하게 특정할 때는 `DIGEST`를 이용한다.
  - digest는 DockerHub에 업로드하면 자동으로 부여되는 식별자다.
  - digest는 고유한 식별자이기 때문에 이미지를 고유하게 지정할 수 있다.
  - digest 확인: docker image ls 명령에 --digest 옵션을 지정한다.
  - Dockerfile에서 이미지를 고유하게 지정할 때는 이미지명 다음에 @마크를 붙이고 digest 값을 지정한다.


## 4. MAINTAINER
- MAINTAINER는 이미지를 생성한 사람의 정보를 설정한다.
- 형식은 자유이며 보통 다음과 같이 이름 & E-MAIL을 입력한다.
- MAINTAINER 지시어는 필수 요소는 아니지만 이미지를 공개할 때 작성자가 누구인지 명시하는 역할을 한다.

```
MAINTAINER Hong, Gildong <gildong@licos.com>
```

## 5. Docker 이미지 생성(Build)
- `docker build [OPTIONS] PATH | URL | -`

- Dockerfile로 부터 이미지를 생성하려면 `docker build` 명령을 사용한다.

```
Usage:  docker image build [OPTIONS] PATH | URL | -

Build an image from a Dockerfile

Options:
      --add-host list           Add a custom host-to-IP mapping (host:ip)
      --build-arg list          Set build-time variables
      --cache-from strings      Images to consider as cache sources
      --disable-content-trust   Skip image verification (default true)
  -f, --file string             Name of the Dockerfile (Default is
                                'PATH/Dockerfile')
      --iidfile string          Write the image ID to the file
      --isolation string        Container isolation technology
      --label list              Set metadata for an image
      --network string          Set the networking mode for the RUN
                                instructions during build (default "default")
      --no-cache                Do not use cache when building the image
  -o, --output stringArray      Output destination (format:
                                type=local,dest=path)
      --platform string         Set platform if server is multi-platform
                                capable
      --progress string         Set type of progress output (auto, plain,
                                tty). Use plain to show container output
                                (default "auto")
      --pull                    Always attempt to pull a newer version of
                                the image
  -q, --quiet                   Suppress the build output and print image
                                ID on success
      --secret stringArray      Secret file to expose to the build (only
                                if BuildKit enabled):
                                id=mysecret,src=/local/secret
      --ssh stringArray         SSH agent socket or keys to expose to the
                                build (only if BuildKit enabled) (format:
                                default|<id>[=<socket>|<key>[,<key>]])
  -t, --tag list                Name and optionally a tag in the
                                'name:tag' format
      --target string           Set the target build stage to build.
```


- ex> Dockerfile 작성

```
FROM centos:latest
```

- ex> 현재 경로에 있는 Dockerfile로 부터 sample이라는 이름의 Docker 이미지를 생성한다.

```
> docker image build -t sample .
Sending build context to Docker daemon  2.048kB
Step 1/1 : FROM centos:latest
---> 9f38484d220f
Successfully built 9f38484d220f
Successfully tagged sample:latest
```

- ex> 베이스 이미지인 centos:latest가 이미 로컬 환경에 다운로드된 상태에서 sample이라는 이름에 태그명 1.0이라는 버전으로 한 새로운 이미지를 작성한다.

```
> docker image build -t sample:1.0 .
Sending build context to Docker daemon  2.048kB
Step 1/1 : FROM centos:latest
---> 9f38484d220f
Successfully built 9f38484d220f
Successfully tagged sample:1.0
> docker image ls
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
centos              latest              9f38484d220f        2 months ago        202MB
sample              1.0                 9f38484d220f        2 months ago        202MB
sample              latest              9f38484d220f        2 months ago        202MB
```

- Dockerfile에는 임의의 파일명을 붙일수 있다.
  - 파일명은 docker build 명령에서 **-f 옵션**으로 지정한다.
  - 단, 파일명이 Dockerfile 이외의 이름인 경우 Docker Hub에서 이미지의 자동 생성 기능을 사용할 수 없다.
  - ex> BaseDockerfile 이름의 dockerfile을 빌드한다.

```
  > docker image build -t sample:2.0 -f BaseDockerfile .
Sending build context to Docker daemon  2.048kB
Step 1/1 : FROM centos:latest
---> 9f38484d220f
Successfully built 9f38484d220f
Successfully tagged sample:2.0
> docker image ls
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
centos              latest              9f38484d220f        2 months ago        202MB
sample              1.0                 9f38484d220f        2 months ago        202MB
sample              2.0                 9f38484d220f        2 months ago        202MB
sample              latest              9f38484d220f        2 months ago        202MB
```

- `--pull` 옵션을 사용하면 매번 베이스 이미지를 강제로 새로 받아온다.
- 이미지를 빌드할 때 확실하게 최신 베이스 이미지를 사용하고 싶다면 다음과 같이 `--pull-true` 옵션을 붙여서 빌드한다.
  
```
> docker image build --pull-true -t sample:2.0 -f Basedockerfile .
```

## 6. Docker 이미지의 레이어 구조
- Dockerfile을 빌드하여 Docker 이미지를 작성하면 Dockerfile의 명령별로 이미지를 작성한다.
- 작성된 여러개의 이미지는 레이어 구조로 되어있다.
- 작성한 이미지는 다른 이미지와도 공유된다.
  - 공통의 베이스 이미지를 바탕으로 여러 개의 이미지를 작성한 경우, 베이스 이미지의 레이어가 공유된다.
  - 디스크 용량을 효율적으로 이용한다.
- ex> Docker image의 레이거 구조 확인방법
  - Dockerfile

```
FROM node:argon
# Create app directory
RUN mkdir -p /usr/src/app
WORKDIR /usr/src/app
# Install app dependencies
COPY package.json /usr/src/app/
RUN npm install
# Bundle app source
COPY . /usr/src/app
EXPOSE 8080
CMD [ "npm", "start" ]
```

  - image build: `명령 한 줄마다 이미지가 작성`된다.

```
> docker image build -t expressweb .
Step 1 : FROM node:argon
argon: Pulling from library/node...
...
Status: Downloaded newer image for node:argon
---> 530c750a346e
Step 2 : RUN mkdir -p /usr/src/app
---> Running in 5090fde23e44
---> 7184cc184ef8
Removing intermediate container 5090fde23e44
Step 3 : WORKDIR /usr/src/app
---> Running in 2987746b5fba
---> 86c81d89b023
Removing intermediate container 2987746b5fba
Step 4 : COPY package.json /usr/src/app/
---> 334d93a151ee
Removing intermediate container a678c817e467
Step 5 : RUN npm install
---> Running in 31ee9721cccb
---> ecf7275feff3
Removing intermediate container 31ee9721cccb
Step 6 : COPY . /usr/src/app
---> 995a21532fce
Removing intermediate container a3b7591bf46d
Step 7 : EXPOSE 8080
---> Running in fddb8afb98d7
---> e9539311a23e
Removing intermediate container fddb8afb98d7
Step 8 : CMD npm start
---> Running in a262fd016da6
---> fdd93d9c2c60
Removing intermediate container a262fd016da6
Successfully built fdd93d9c2c60
```
