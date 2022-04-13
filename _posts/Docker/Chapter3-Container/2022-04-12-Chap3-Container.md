---
title: 1. Container 소개
author: Banjung
date: 2022-04-10 08:00:00 -0500
categories: [Docker, Chapter 3(컨테이터)]
tags: [Docker Container]
math: true
mermaid: true
---

## 1. Summary

```
## 컨테이너 생성
> docker container create -it --name c1 centos

## 컨테이너 시작
> docker container start c1

## 컨테이너 정지
> docker container stop c1
> docker container pause c2

## 컨테이너 생성 및 시작
> docker container run -it centos
> docker container run -it --name c2 centos

## 컨테이너 재시작
> docker contaienr restart c1

## 컨테이너 삭제
> docker container rm c1 c2
> docker container unpause c2

## 컨테이너 확인
> docker ps
> docker container ls
> docker container inspect c8f
```

## 2. Docker Container의 Life cycle
- Docker image가 만들어 졌으면 Container를 생성할 수 있다.
- `docker container COMMAND`

```
> docker container --help
Usage:  docker container COMMAND
Manage containers
Commands:
  attach      Attach local standard input, output, and error streams to a running container
  commit      Create a new image from a container's changes
  cp          Copy files/folders between a container and the local filesystem
  create      Create a new container
  diff        Inspect changes to files or directories on a container's filesystem
  exec        Run a command in a running container
  export      Export a container's filesystem as a tar archive
  inspect     Display detailed information on one or more containers
  kill        Kill one or more running containers
  logs        Fetch the logs of a container
  ls          List containers
  pause       Pause all processes within one or more containers
  port        List port mappings or a specific mapping for the container
  prune       Remove all stopped containers
  rename      Rename a container
  restart     Restart one or more containers
  rm          Remove one or more containers
  run         Run a command in a new container
  start       Start one or more stopped containers
  stats       Display a live stream of container(s) resource usage statistics
  stop        Stop one or more running containers
  top         Display the running processes of a container
  unpause     Unpause all processes within one or more containers
  update      Update configuration of one or more containers
  wait        Block until one or more containers stop, then print their exit codes
```

- 컨테이너를 조작하기 위한 기본 명령은 5가지 이다.

|조작|Command|설명|
|------|------|------|
|생성|docker container create|컨테이너를 시작할 준비가 된 상태|
|생성 및 시작|docker container run|이미지로부터 컨테이너를 생성 및 실행|
|시작|docker continer start|정지 중인 컨테이너를 시작|
|정지|docker container stop|실행 중인 컨테이너를 정지|
|삭제|docker container rm|컨테이너를 삭제|

## 3. Container 생성 및 실행

#### **Container 생성**
`docker contaienr create [OPTION] IMAGE [COMMAND] [ARG...]`

- 이미지로부터 컨테이너를 생성한다.

```
> docker container create --help
Usage:  docker create [OPTIONS] IMAGE [COMMAND] [ARG...]
Create a new container
Options:
      --add-host list                  Add a custom host-to-IP mapping
...<생략>...
  -w, --workdir string                 Working directory inside the container
```

- 주요 옵션

|Option|설명|
|------|------|
|-t or --interactive|표준 입력을 사용할 수 있도록 한다.|
|-t or tty|표준 출력 및 표준 에러를 터미널로 출력한다.|
|--name|Container 이름을 지정한다.|

```
도커 명령에서 자주 사용되는 옵션

-i 옵션은 컨테이너를 실행할 때 컨테이너 쪽 표준 입력과의 연결을 그대로 유지한다. 그러므로 컨테이너 쪽 쉘에 들어가서 명령을 실행 할 수 있다. 실제 사용에서는 -t 옵션과 함께 사용하는 경우가 많다. -t 옵션은 유사 터미널 기능을 활성화하는 옵션인데, -i 옵션을 사용하지 않으면 유사 터미널을 실행해도 여기에 입력할 수가 없으므로 -i와 -t 옵션을 같이 사용한다.
```

- `Container를 생성할 때, -i 옵션과 -t 옵션을 같이 사용하여 표준 입출력이 가능한 Container를 생성` 한다.
- Container를 생성하면 STATUS가 `Created` 이다.
  - ex> centos 이미지를 c1이름으로 컨테이너화한다.

```
> docker image ls
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
centos              latest              75835a67d134        12 days ago         200MB
pys6478/web         centos              75835a67d134        12 days ago         200MB
> docker container create -it --name c1 centos
e9e056f426285681de9cfb08bde228db8f55ba268de08fcaae44830b4ee625a7
> docker container ls
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
> docker container ls -a(모든 상태의 컨테이너 조회)
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
e9e056f42628        centos              "/bin/bash"         5 seconds ago       Created                                 c1
```

#### **Container 시작**
`docker container start [OPTIONS] CONTAINER [CONTAINER...]`

- 정지되어 있는 Container를 시작할 때는 `docker container start` 명령을 사용한다.

```
> docker container start --help
Usage:  docker container start [OPTIONS] CONTAINER [CONTAINER...]
Start one or more stopped containers
Options:
  -a, --attach               Attach STDOUT/STDERR and forward signals
      --detach-keys string   Override the key sequence for detaching a
                             container
  -i, --interactive          Attach container's STDIN
```

- Container를 시작하면 STATUS가 Created에서 `Up`으로 변한다.
  - ex> 정지된 C1 Container를 시작한다.

```
> docker container ls -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
e9e056f42628        centos              "/bin/bash"         3 minutes ago       Created                                 c1
> docker container start c1
c1
> docker container ls
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
e9e056f42628        centos              "/bin/bash"         3 minutes ago       Up 2 seconds                            c1
```

- 다수의 Container를 한꺼번에 시작하고 싶을 때는 인수에 Container 식별자를 여러개 지정한다.

```
Shell 이미지와 Service 이미지의 차이점

centos: shell image
httpd: service image
shell 이미지는 -it 옵션을 하지 않으면 바로 종료된다.
```

#### **Container 정지**
`docker container stop [OPTION] CONTAINER [CONTIANER...]`

- 실행 중인 Container를 정지할 때는 `docker container stop` 명령을 사용한다.

```
> docker container stop --help
Usage:  docker stop [OPTIONS] CONTAINER [CONTAINER...]
Stop one or more running containers
Options:
  -t, --time int   Seconds to wait for stop before killing it (default 10)
```

- STATUS가 `Exited`인 것을 확인할 수 있다.
  - ex> 실행 중인 C1 Container를 stop 한다.

```
> docker container ls
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
e9e056f42628        centos              "/bin/bash"         4 minutes ago       Up 59 seconds                           c1
> docker container stop c1
c1
> docker container ls
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
> docker container ls -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                       PORTS               NAMES
e9e056f42628        centos              "/bin/bash"         4 minutes ago       Exited (137) 4 seconds ago                       c1
```  

- 정지 중인 모든 Container를 삭제하려면 `docker container prune` 명령을 사용한다.

```
> docker container prune
WARNING! This will remove all stopped containers.
Are you sure you want to continue? [y/N] y
Deleted Containers:
38f3f39a879526fc9868f4ca9d2112a59270bd1b75ebb21f9a3693a1be1efb8c
1a62d5eddbf4c8891986f9fc0f18ea70a0bdd8a00e2972bf7fce1b8723a28b8d
f6f9f7ae93976f381723df812893efa428fb691f0dfb442155d575ef94a81a2d
345cd87a09fa9bf46e7c17d98b570dab51a863f9f6607e3be5c2d80f7a9bb7f5
afda6b0720c2becec6374754e18a0c6c41bdde328abd24ebde080a6f02bc511d
fc2d9ed61286d8920a1a8c24ff62ae71b6d4dce08ebfa65238e5492d9f51190a
589b6812962f235b934521ecefcbba599741d021b3599d8a92c1ed067c668e12
638ed23e4323c11d93cb1274bf14bbc879f34882007f847867617aeb77689e78
ebe7fa1c6caca51264380f2e2a4e65be6c4af0fa9756f2edc278c5f54671de13
Total reclaimed space: 1.754MB
```

#### **Container 생성 및 실행**
`docker container run [OPTIONS] IMAGE [COMMAND] [ARG...]`

- docker Container의 생성 및 시작은 `docker container run` 명령으로 실행한다.

```
> docker container run --help
Usage:  docker container run [OPTIONS] IMAGE [COMMAND] [ARG...]
Run a command in a new container
Options:
      --add-host list                  Add a custom host-to-IP mapping
                                       (host:ip)
  -a, --attach list                    Attach to STDIN, STDOUT or STDERR
...<생략>...
  -w, --workdir string                 Working directory inside the container
```

- ex> 표준 입출력을 사용하도록 centos 이미지를 c2 이름으로 Container를 만들고 실행한다.
  - -it 옵션 없이 실행하면 바로 종료된다.
  - `Ctrl + P + Q` : Container 실행 중인 상태에서 빠져 나올 때 사용한다.
  - Container를 종료시키려면 `exit 명령`을 사용한다.
  - `이름을 생략하면 Container 이름은 랜덤으로 자동 설정된다.`

```
> docker container run -it --name c2 centos
# exit
exit
> docker container run -it centos
#
> docker container ls -a
CONTAINER ID        IMAGE                     COMMAND                  CREATED             STATUS                     PORTS                 NAMES
d5cb4b73816d        centos                    "/bin/bash"              8 seconds ago       Up 7 seconds                                     ecstatic_montalcini
5367c9819caa        centos                    "/bin/bash"              2 minutes ago       Exited (0) 2 minutes ago                          c2
```  

- Container 안에의 Shell을 실행할 수 있다.
  - ex> /bin/bash를 Container에서 실행한다.

```
> docker container run -it --name c4 centos /bin/bash
#
> docker container ls -a
CONTAINER ID        IMAGE                     COMMAND                  CREATED             STATUS                     PORTS                 NAMES
1a3b95c47f92        centos                    "/bin/bash"              13 seconds ago      Up 11 seconds                                    c4
```

- `-d 옵션` : Container를 백그라운드로 실행한다.
  - ex> centos 이미지를 c3 이름으로 표준 입출력 백그라운드로 실행한다.

```
> docker container run -d -it --name c3 centos
2ff8e89c352c83a58241a056dc6a7df86fde822e963ada8ef929ace4a333dae2
> docker container ls
CONTAINER ID        IMAGE               COMMAND              CREATED             STATUS              PORTS               NAMES
2ff8e89c352c        centos              "/bin/bash"          3 seconds ago       Up 2 seconds                            c3
```  

```
실행하자마자 종료되는 경우

백그라운드에서 동작(예시: 서버 기능하는 이미지) => 표준입출력(-it)

예제) 백그라운드에서 동작하는 http를 포그라운드에서 컨테이너를 실행시켜 보자.

> docker container run -d --name web1 httpd
Unable to find image 'httpd:latest' locally
latest: Pulling from library/httpd
f17d81b4b692: Pull complete
06fe09255c64: Pull complete
0baf8127507d: Pull complete
1e5b6ba3cfcc: Pull complete
f09ae565a639: Pull complete
Digest: sha256:378951edb85bfd57ddaf2edf482ec62e552667bfc4deeaf326342d481ac526f0
Status: Downloaded newer image for httpd:latest
ca3d7fffa89ea18333a3d66e07e1a37b4700f556ba488ceeec2d1b04a49b0b84
> docker image ls
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
httpd               latest              0240c8f5816c        2 days ago          132MB
> docker container run -it --name web2 httpd
AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using 172.17.0.3. Set the 'ServerName' directive globally to suppress this message
AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using 172.17.0.3. Set the 'ServerName' directive globally to suppress this message
[Mon Oct 22 06:49:17.255292 2018] [mpm_event:notice] [pid 1:tid 140109860508864] AH00489: Apache/2.4.35 (Unix) configured -- resuming normal operations
[Mon Oct 22 06:49:17.255468 2018] [core:notice] [pid 1:tid 140109860508864] AH00094: Command line: 'httpd -D FOREGROUND'
C[Mon Oct 22 06:50:03.334138 2018] [mpm_event:notice] [pid 1:tid 140109860508864] AH00491: caught SIGTERM, shutting down
> docker container ls -a
CONTAINER ID        IMAGE               COMMAND              CREATED              STATUS                        PORTS               NAMES
97d54155d306        httpd               "httpd-foreground"   About a minute ago   Exited (0) 27 seconds ago                         web2
```

- 표준 입출력 옵션(-it)을 추가 안한 경우 컨테이너를 실행하고 바로 종료된다.
- ex> shell 이미지인 centos를 -it 옵션을 추가하지 않고 실행시켜 본다.

```
> docker container run --name c1 centos
> docker container ls -a
CONTAINER ID        IMAGE               COMMAND              CREATED             STATUS                         PORTS               NAMES
56154a39a0bc        centos              "/bin/bash"          6 seconds ago       Exited (0) 5 seconds ago                           c1
```

#### **Container 재시작**
`docker container restart [OPTIONS] CONTAINER [CONTAINER...]`

- Container를 재시작할 때는 `docker container restart` 명령을 사용한다.

```
> docker container restart --help
Usage:  docker container restart [OPTIONS] CONTAINER [CONTAINER...]
Restart one or more containers
Options:
  -t, --time int   Seconds to wait for stop before killing the container
                   (default 10)
```

- -t --time 옵션을 사용하면 n초 후 Container를 재시작할 수 있다.
  - ex> 2초 후 c1 Container를 재시작한다.

```
> docker container ls -a
CONTAINER ID        IMAGE               COMMAND             CREATED              STATUS                        PORTS               NAMES
2a3a5409c47e        centos              "/bin/bash"         About a minute ago   Exited (137) 56 seconds ago                       c1
> docker container restart -t 2 2a3
2a3
> docker container ls -a
CONTAINER ID        IMAGE               COMMAND             CREATED              STATUS              PORTS               NAMES
2a3a5409c47e        centos              "/bin/bash"         About a minute ago   Up 4 seconds                            c1
```

- Container 안에서 실행하는 명령의 종료 STATUS에 따라 컨테이너를 자동으로 재시작하고 싶은 경우는 `docker container run 명령에서 --restart 옵션`을 사용한다.(default는 restart no 이다.)
  - ex> Container가 종료될 경우 Docker가 다시 시작하도록 redis Container를 실행한다.

```
> docker container run --restart=always redis
```  

|Policy|Result|
|------|------|
|no|	컨테이너가 종료되면 자동으로 재시작하지 않는다.(default)|
|on-failure|컨테이너가 0이 아닌 종료상태로 종료될 때만 다시 시작한다. 선택적으로 docker 데몬이 시도하는 재시작 횟수를 제한할 수 있다.|
|always|종료 상태에 관계 없이 항상 컨테이너를 다시 시작한다.|
|unless-stopped|컨테이너가 중지 상태가 된 경우를 제외하고는 재시작한다.|

#### **Container 삭제**
`docker container rm [OPTIONS] CONTAINER [CONTAINER...]`

- 중지(Exited)된 Container를 삭제할 때는 `docker container rm`명령을 사용한다.

```
> docker container rm --help
> Usage:  docker container rm [OPTIONS] CONTAINER [CONTAINER...]
Remove one or more containers
Options:
  -f, --force     Force the removal of a running container (uses SIGKILL)
  -l, --link      Remove the specified link
  -v, --volumes   Remove the volumes associated with the container
```

- 여러 개의 Container를 한꺼번에 삭제할 때는 인수에 Container 식별자를 여러개 지정한다.
  - ex> c1, c2, c3 이름의 컨테이너 삭제

```
> docker container ls -a
CONTAINER ID        IMAGE               COMMAND              CREATED             STATUS                      PORTS               NAMES
4469905e024a        centos              "/bin/bash"          5 seconds ago       Exited (0) 3 seconds ago                        c3
856aefb5aba6        centos              "/bin/bash"          12 seconds ago      Exited (0) 8 seconds ago                        c2
edf6a916287b        centos              "/bin/bash"          19 seconds ago      Exited (0) 14 seconds ago                       c1
97d54155d306        httpd               "httpd-foreground"   3 minutes ago       Exited (0) 3 minutes ago                        web2
ca3d7fffa89e        httpd               "httpd-foreground"   12 minutes ago      Up 12 minutes               80/tcp              web1
> docker container rm c1 c2 c3
c1
c2
c3
> docker container ls -a
CONTAINER ID        IMAGE               COMMAND              CREATED             STATUS                     PORTS               NAMES
97d54155d306        httpd               "httpd-foreground"   3 minutes ago       Exited (0) 3 minutes ago                       web2
ca3d7fffa89e        httpd               "httpd-foreground"   12 minutes ago      Up 12 minutes              80/tcp              web1
```

- Container를 삭제하려면 먼저 중지(Exited) 상태여야한다.
  - ex> 실행 중인 Container c1을 삭제한다.

```
> docker container rm c1
Error response from daemon: You cannot remove a running container 2a3a5409c47ef31c226b5c34ca011a801ca5ca2b8163975b743ba78670ca9c67. Stop the container before attempting removal or force remove
```

- `-f 옵션`: 실행 중인 상태의 Container를 갈제적으로 삭제한다.
  - ex> web1 이름의 실행 중인 컨테이너를 삭제한다.

```
> docker container ls
CONTAINER ID        IMAGE               COMMAND              CREATED             STATUS              PORTS               NAMES
ca3d7fffa89e        httpd               "httpd-foreground"   13 minutes ago      Up 13 minutes       80/tcp              web1
> docker container rm -f web1
web1
> docker container ls -a
CONTAINER ID        IMAGE               COMMAND              CREATED             STATUS                     PORTS               NAMES
97d54155d306        httpd               "httpd-foreground"   4 minutes ago       Exited (0) 3 minutes ago                       web2
```

- 새로 이름 붙인 Container를 생성할 때 같은 이름을 가진 기존 Container가 존재하는 경우 새로운 Container를 생성할 수 없다.(같은 이름의 Container를 삭제해야한다.)

#### **불필요한 이미지/컨테이너를 일괄 삭제**
`docker system prune [OPTIONS]`

- `docker system prune` 명령을 사용하면 사용하지 않는 이미지, 컨테이너, 볼륨, 네트워크를 일괄적으로 삭제할 수 있다.

```
> docker system prune --help
Usage:  docker system prune [OPTIONS]
Remove unused data
Options:
  -a, --all             Remove all unused images not just dangling ones
      --filter filter   Provide filter values (e.g. 'label==')
  -f, --force           Do not prompt for confirmation
      --volumes         Prune volumes
```

- ex> 사용하지 않는 리소스를 모두 삭제한다. 디스크 낭비를 줄일 수 있다.

```
> docker system prune -a
WARNING! This will remove:
        - all stopped containers
        - all networks not used by at least one container
        - all images without at least one container associated to them
        - all build cache
Are you sure you want to continue? [y/N] y
Deleted Containers:
d3ed594d193b0d3402a447a85a3278ff335b329c4b92801e6c24ada0404ed2ad
Deleted Networks:
temp_default
...<생략>...
Total reclaimed space: 4.332GB
```

- 실행 중이 아닌 모든 컨테이너를 삭제하는 명령은 `docker container prune`이다. 정지시킨 대부분의 컨테이너는 그리 쓸모가 없기 때문에 정기적으로 이들을 삭제하는 것이 좋다.

```
> docker container prune
```

- `docker image prune` 명령은 태그가 붙지 않은(dangling) 모든 이미지를 삭제한다.

```
> docker image prune
```

#### **Container 중단**
`docker container pause CONTAINER [CONTAINER...]`

- 실행중인 컨테이너에서 작동 중인 프로세스를 모두 중단시킬 때는 `docker container pause`명령을 사용한다.

```
> docker container pause --help
Usage:  docker pause CONTAINER [CONTAINER...]
Pause all processes within one or more containers
```

- STATUS 필드에 (Paused)가 표시되어 있으면 컨테이너가 일시 중지된 상태이다.
  - ex> web1 컨테이너 일시 중지한다.

```
> docker container ls
CONTAINER ID        IMAGE               COMMAND              CREATED             STATUS              PORTS               NAMES
1e220a23ba2e        httpd               "httpd-foreground"   12 minutes ago      Up 12 minutes       80/tcp              web1
> docker container pause web1
web1
> docker container ls
CONTAINER ID        IMAGE               COMMAND              CREATED             STATUS                   PORTS               NAMES
1e220a23ba2e        httpd               "httpd-foreground"   12 minutes ago      Up 12 minutes (Paused)   80/tcp              web1
```  

```
Note
[STOP VS PAUSE]

STOP: 컨테이너가 사용 중인 자원 모두 해제한다.
PAUSE: 컨테이너가 사용 중인 자원 해제하지 않는다.
```

#### **Container 재개**
`docker container unpause CONTAINER [CONTAINER...]`

- 중지중인 컨테이너를 재개할 때는 `docker container unpause`명령을 사용한다.

```
> docker container unpause --help
Usage:  docker unpause CONTAINER [CONTAINER...]
Unpause all processes within one or more containers
```

## 4. Container 확인
`docker container ls [OPTION]`

- ps 명령어와 같이 가동 중인 Container 리시트를 보여준다.

```
> docker container ls --help
Usage:  docker container ls [OPTIONS]
List containers
Aliases:
  ls, ps, list
Options:
  -a, --all             Show all containers (default shows just running)
  -f, --filter filter   Filter output based on conditions provided
      --format string   Pretty-print containers using a Go template
  -n, --last int        Show n last created containers (includes all
                        states) (default -1)
  -l, --latest          Show the latest created container (includes all
                        states)
      --no-trunc        Don't truncate output
  -q, --quiet           Only display numeric IDs
  -s, --size            Display total file sizes
```

- -q 옵션을 사용하면 Container ID만을 추출할 수 있다.
- 표시할 Container를 필터링할 때는 -f 옵션을 지정한다.(Filtering 조건은 key=value로 지정한다.)
  - ex> 이름이 c1인 Container를 조회한다.

```
> docker container ls -a -f name=c1
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
2a3a5409c47e        centos              "/bin/bash"         About an hour ago   Up About an hour                        c1
```  

- 출력 형식을 변경하고 싶을 때는 `--format` 옵션을 지정한다.
  - ex> Container ID와 가동 중인지 아닌지의 상태를 콜론으로 구분하여 표시한다.

```
> docker container ls -a --format "{{.Names}}: {{.Status}}"
c1: Up About an hour 
```        

#### **Container 가동 확인**
`docker container stats [OPTIONS] [CONTAINER...]`

- Container 가동 상태가 실시간 목록으로 표시된다.

```
> docker container stats --help
Usage:  docker container stats [OPTIONS] [CONTAINER...]
Display a live stream of container(s) resource usage statistics
Options:
  -a, --all             Show all containers (default shows just running)
      --format string   Pretty-print images using a Go template
      --no-stream       Disable streaming stats and only pull the first result
      --no-trunc        Do not truncate output
```

- ex> c1 Container의 가동 상황을 확인한다.

```
> docker container stats c1
CONTAINER ID        NAME                CPU %               MEM USAGE / LIMIT   MEM %               NET I/O             BLOCK I/O           PIDS
2a3a5409c47e        c1                  0.00%               948KiB / 1.934GiB   0.05%               1.39kB / 0B         0B / 0B             1
CONTAINER ID        NAME                CPU %               MEM USAGE / LIMIT   MEM %               NET I/O             BLOCK I/O           PIDS
2a3a5409c47e        c1                  0.00%               948KiB / 1.934GiB   0.05%               1.39kB / 0B         0B / 0B             1
CONTAINER ID        NAME                CPU %               MEM USAGE / LIMIT   MEM %               NET I/O             BLOCK I/O           PIDS
2a3a5409c47e        c1                  0.00%               948KiB / 1.934GiB   0.05%               1.39kB / 0B         0B / 0B             1
CONTAINER ID        NAME                CPU %               MEM USAGE / LIMIT   MEM %               NET I/O             BLOCK I/O           PIDS
2a3a5409c47e        c1                  0.00%               948KiB / 1.934GiB   0.05%               1.39kB / 0B         0B / 0B             1 
```

#### **Container 세부사항 확인**
`docker sontainer inspect`

- Container 뿐만 아니라 docker에서 사용되는 Network, Volume 등 모든 리소스를 자세히 확인한다.

```
> docker container inspect --help
Usage:  docker inspect [OPTIONS] NAME|ID [NAME|ID...]
Return low-level information on Docker objects
Options:
  -f, --format string   Format the output using the given Go template
  -s, --size            Display total file sizes if the type is container
      --type string     Return JSON for specified type
```

- ex> 이름이 c1인 Container를 자세히 확인한다.

```
> docker container ls
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
c8fe27fdfc39        centos              "bash"              6 minutes ago       Up 16 seconds                           stoic_kalam
> docker container inspect c8f
[
    {
        "Id": "c8fe27fdfc390389b7612f7465c9299d93cf888c855757e39bb62b5fe918da69",
        "Created": "2018-10-22T06:11:58.00252796Z",
... <생략>
                    "DriverOpts": null
                }
            }
        }
    }
]
```