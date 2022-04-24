---
title: 2. Container 조작
author: Banjung
date: 2022-04-13 08:00:00 -0500
categories: [Docker, Chapter 3(컨테이터)]
tags: [Docker Container Controll]
math: true
mermaid: true
---

## 0. Summary

```
## 컨테이너 접근
> docker container attach webserver

## 컨테이너에서 어플리케이션 실행
> docker container exec -it webserver /bin/echo "Hello world"

## 컨테이너에서 실행 중인 프로세스 확인
> docker container top webserver

## 컨테이너 이름 변경
> docker container rename webserver s1

## 컨테이너 로그 확인
> docker container logs s1

## 컨테이너 차분 확인
> docker container diff s1
```

## 1. Container attach (컨테이너 접근)
`docker container attach [OPTIONS] CONTAINER`

- 백그라운드에서 실행 중인 Container에 접근하려면 `docker attach` 명령을 사용한다.

```
> docker container attach --help
Usage:  docker container attach [OPTIONS] CONTAINER
Attach local standard input, output, and error streams to a running container
Options:
      --detach-keys string   Override the key sequence for detaching a
                             container
      --no-stdin             Do not attach STDIN
      --sig-proxy            Proxy all received signals to the process
                             (default true)
```

- ex> centos 이미지를 c1이름으로 실행 시킨 후 attach 명령으로 다시 접근한다.
  - 연결한 Container를 종료하려면 ctrl + c, Container를 시작한 채로 Container 안에서 움직이는 프로세스만 종료하려면 ctrl + p + q를 입력한다.

```
> docker container run -itd --name c1 centos
5e0ebf558f47092da3e1c2cc3cb59d91b41256eceb892412e8deb7cbcb2f11f6
> docker container ls
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
5e0ebf558f47        centos              "/bin/bash"         3 seconds ago       Up 3 seconds                            c1
> docker container attach c1
# read escape sequence

```

## 2. Container exec(컨테이너에서 어플리케이션 실행)
`docker container exec [OPTIONS] CONTAINER COMMAND [ARG...]`

- 가동 중인 Container에서 새로운 프로세스를 작성할 때는 `docker container exec` 명령을 사용한다.

```
> docker container exec --help
Usage:  docker contianer exec [OPTIONS] CONTAINER COMMAND [ARG...]
Run a command in a running container
Options:
  -d, --detach               Detached mode: run command in the background
      --detach-keys string   Override the key sequence for detaching a
                             container
  -e, --env list             Set environment variables
  -i, --interactive          Keep STDIN open even if not attached
      --privileged           Give extended privileges to the command
  -t, --tty                  Allocate a pseudo-TTY
  -u, --user string          Username or UID (format:
                             [:])
```

- `COMMAND: 실행할 어플리케이션의 경로를 지정하며, 경로에 따라 아규먼트를 지정한다.`
- 실행 중인 Container에서만 실행할 수 있다.
- ex> 실행 중인 web1 Container에 추가로 bash 쉘을 실행한다.
  - bash만 종료 => **exit 명령으로 Container를 빠져나와도 web1 Container는 종료되지 않는다.**

```
> docker container ls
CONTAINER ID        IMAGE               COMMAND              CREATED STATUS              PORTS               NAMES
1e220a23ba2e        httpd               "httpd-foreground"   2 minutes ago       Up 2 minutes        80/tcp              web1
> docker container exec -it web1 bash
# exit
exit
> docker container ls
CONTAINER ID        IMAGE               COMMAND              CREATED             STATUS              PORTS               NAMES
1e220a23ba2e        httpd               "httpd-foreground"   2 minutes ago       Up 2 minutes        80/tcp              web1
```  

- 명령을 직접 실행할 수 있다.
  - ex> webserver 라는 이름의 Container에서 echo명령을 실행한다.

```
> docker container exec -it webserver /bin/echo "Hello world"
Hello world
```  

## 3. Container top(컨테이너 내의 실행 중인 프로세스 확인)
`docker container top CONTAINER [ps OPTION]`

- Container에서 실행 중인 프로세스를 확인하려면 `docker container top`명령을 사용한다.

```
> docker container top --help
Usage:  docker container top CONTAINER [ps OPTIONS]
Display the running processes of a container
```

- ex> webserver Container에서 실행되고 있는 프로세스를 확인한다.

```
> docker container top webserver
PID                 USER                TIME                COMMAND
13055               root                0:00                httpd -DFOREGROUND
13103               bin                 0:00                httpd -DFOREGROUND
13104               bin                 0:00                httpd -DFOREGROUND
13105               bin                 0:00                httpd -DFOREGROUND
13215               root                0:00                /bin/bash 
```

## 4. Container rename(컨테이너 이름 변경)
`docker container rename CONTAINER NEW_NAME`

- `docker container rename` 명령을 사용하면 Contaienr의 이름을 변경할 수 있다.

```
> docker container rename --help
Usage:  docker rename CONTAINER NEW_NAME
Rename a container
```

- Container를 생성하거나 실행 할 때 --name 옵션을 사용하지 않으면 임의의 이름이 부여된다.
  - ex> centos 이미지를 Container화 할 때 이름 옵션을 사용하지 않고 rename을 이용해 이름을 변경한다.

```
> docker container run -itd centos
83d429b77a0dcee42e7c6c9e8883d0c31ad3aa9d51f4d40bdb7e463056eb0648
> docker container ls
CONTAINER ID        IMAGE               COMMAND              CREATED             STATUS              PORTS               NAMES
83d429b77a0d        centos              "/bin/bash"          4 seconds ago       Up 3 seconds                            sharp_mahavira
> docker container rename sharp_mahavira newc1
> docker container ls
CONTAINER ID        IMAGE               COMMAND              CREATED             STATUS              PORTS               NAMES
83d429b77a0d        centos              "/bin/bash"          44 seconds ago      Up 43 seconds                           newc1
```  

- Container가 실행 중이 아니어도 사용할 수 있다.

## 5. Container logs(컨테이너 로그 확인)
`docker container logs [OPTIONS] CONTAINER`

- Container가 실행되다가 갑자기 종료되거나 어플리케이션이 정상적으로 동작하지 않으면 로그를 확인해야 한다.
- Container의 표준 출력 및 에러를 확인할 수 있다.
- Container의 로그를 확인하려면 `docker container log` 명령을 사용한다.

```
> docker container logs --help
Usage:  docker container logs [OPTIONS] CONTAINER
Fetch the logs of a container
Options:
      --details        Show extra details provided to logs
  -f, --follow         Follow log output
      --since string   Show logs since timestamp (e.g. 2013-01-02T13:23:37) or relative
                       (e.g. 42m for 42 minutes)
      --tail string    Number of lines to show from the end of the logs (default "all")
  -t, --timestamps     Show timestamps
      --until string   Show logs before a timestamp (e.g. 2013-01-02T13:23:37) or
                       relative (e.g. 42m for 42 minutes)
```

- ex> db1 Container 의 로그를 확인한다.

```
> docker container logs db1
Initializing database
2018-10-22T20:49:39.880951Z 0 [Warning] [MY-011070] [Server] 'Disabling symbolic links using --skip-symbolic-links (or equivalent) is the default. Consider not using this option as it' is deprecated and will be removed in a future release.
2018-10-22T20:49:39.881078Z 0 [System] [MY-013169] [Server] /usr/sbin/mysqld (mysqld 8.0.12) initializing of server in progress as process 28
mbind: Operation not permitted
mbind: Operation not permitted
...<생략>...
```

- Container가 꼭 실행 중이지 않아도 로그를 확인할 수 있다.
  - ex> 중지된 Container의 logs2의 로그를 확인한다.

```
> docker container run -d -e MYSQL_ROOT_PASSWORD=1234 --name logs2 httpd
cf5588ebaa22051b798a1e63f6810513fb1beae573838f05e9005853f1144022
> docker stop cf5
cf5
> docker container ls -a
CONTAINER ID        IMAGE               COMMAND              CREATED             STATUS                     PORTS               NAMES
cf5588ebaa22        httpd               "httpd-foreground"   26 seconds ago      Exited (0) 2 seconds ago                       logs2
> docker container logs log2
AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using 172.17.0.2. Set the 'ServerName' directive globally to suppress this ...<생략>...s
```  

- `-t 옵션:` 중지된 Container의 로그를 확인할 때 타임 테이블을 표시한다.
- `--tail 옵션:` 최근 발생된 로그를 확인하는 옵션이다.
- `--since 옵션:` 특정 날짜로부터 로그를 확인하는 옵션이다.
  - `docker container logs -t -- since "2022-04-13T22:30" logs2`

## 6. Health Checking
`Container 내보의 프로세스가 정상적으로 동작하고 있는지를 확인하는 것`

#### **CMD 명령**
- CMD 상태란 Console에서 docker run을 할 때 처럼 Container를 바로 실행할 때 사용하는 명령이다.

`docker run --health-cmd command`

- CMD 상태에서 처리할 수 있는 옵션
  - --interval=DURATION(기본값: 30s)
  - --timeout=DURATION(기본값: 30s)
  - --start-period=DURATION(기본값: 0s)
  - --retries=N(기본값: 3)

#### **상태 체크를 하지 않았을 경우**
- ex> db Container를 실행한다. 5초 전에 Container를 조회하면 health check가 시작되고 있는 모습을 확인할 수 있다.

```
> docker run --rm -d --name health -p 8080:8080 effectivetrainings/docker-health
> docker container ls
CONTAINER ID        IMAGE                              COMMAND                CREATED              STATUS              PORTS                    NAMES
84ba30fd483d        effectivetrainings/docker-health   "java -jar /app.jar"   About a minute ago   Up About a minute   0.0.0.0:8080->8080/tcp   health
```

- ex> curl 명령어을 통해 확인해 본다. 아래의 명령은 url을 실행해 결과 값을 받아오고 오류가 나면 1을 출력하는 예제이다. 정상적으로 출력 값을 받아온다.

```
> curl -s -S -f http://127.0.0.1:8080/health || echo 1
{"status":"UP","static":{"status":"UP"},"diskSpace":{"status":"UP","total":10340831232,"free":6843023360,"threshold":10485760}}
```

#### **상태 체크를 한 경우**
- --health 옵션을 사용해서 healthck 컨테이너를 실행한다. 2초마다 health를 체크한다.

```
> docker run --rm -d --name healthck -p 8080:8080 --health-cmd='curl -f -s -S http://127.0.0.1:8080/health || exit 1' --health-interval=2s effectivetrainings/docker-health
0c4192a1b2a3e68019f8de66c1dc8e7049a1b1ae5fa8f6d9a197422d8dd4b965
> docker container ls
CONTAINER ID        IMAGE                              COMMAND                CREATED             STATUS                   PORTS                    NAMES
0c4192a1b2a3        effectivetrainings/docker-health   "java -jar /app.jar"   10 seconds ago      Up 9 seconds (healthy)   0.0.0.0:8080->8080/tcp   healthck
```

## 7. Container 자원 제한
- Container를 생성할 때, 다양한 옵션으로 Container Resource 사용량을 조절할 수 있다.
- 아무 옵션을 입력하지 않으면 기본적으로 Host의 모든 리소스를 제한없이 사용할 수 있다.
  - 이 경우 여러 Container 실행 시 리소스 사용의 불균형이 발생할 수도 있다.
- 현재 Container에서 설정된 리소스를 확인하는 방법은 `docker inspect` 명령을 사용한다.

```
> docker container inspect u1
...
        "HostConfig": {
            "Binds": null,
            "ContainerIDFile": "",
            "LogConfig": {
                "Type": "json-file",
                "Config": {}
            },
            "NetworkMode": "default",
            ...
            "BlkioDeviceWriteIOps": null,
            "CpuPeriod": 0,
            "CpuQuota": 0,
            "CpuRealtimePeriod": 0,
            "CpuRealtimeRuntime": 0,
            "CpusetCpus": "",
            "CpusetMems": "",
            "Devices": [],
            "DeviceCgroupRules": null,
            "DeviceRequests": null,
            "KernelMemory": 0,
            "KernelMemoryTCP": 0,
            ...
```

#### **설정된 자원 변경하기**
- 보통 Container를 생성하는 run, create 명령어에서 자원 할당을 조정할 수 있는데 만들어진 Container의 자원도 아래의 명령어를 통해서 업데이트할 수 있다.

```
> docker update [변경할 자원 제한] [컨테이너 이름]
> docker update --cpuset-cpus=1 centos
```

#### **메모리 관리**
- 메모리는 m(Megabyte), g(Gigabyte) 단위로 설정할 수 있으며 최소 메로리는 4mb읻.
- Container 내부에서 동작하는 프로세스가 Container에 할당된 메모리를 초과하면 자동으로 종료되므로 적절한 메모리를 설정해야 한다.
- Docker는 Container의 메모리 사용량의 Hard Limit나 Soft Limit을 지정할 수 있다.
- Hard Limit를 지정하면 Container는 지정된 메모리량 이상을 사용할 수 없다.
- Sofr Limit를 지정하면 필요한만큼 메모리를 이용하다가 커널이 호스트 머신의 메모리가 부족하거나 컨첸션이 몰릴 경우 제한을 한다.
- memory와 -memory-swap을 이용해서, 물리메모리와 스왑메모리에 대한 제한을 걸 수 있다.
  - --memory: 물리적인 메모리의 크기
  - --memory-swap: 스왑메모리 + 물리메모리의 크기 즉, 전체 메모리의 크기

|옵션|설명|
|----------|-----------------|
|-m 혹은 --memory|컨테이너가 사용할 최대 메모리 양을 지정한다. 이 옵션을 지정할 때, 최소 값은 4m(4메가 바이트)이다.|
|--memory-swap|컨테이너가 사용할 스왑 메모리 영역에 대한 설정을 할 수 있다. 자세한 내용은 밑에 적겠다.|
|--memory-swappiness|기본적으로 호스트 운영체제의 커널은 컨테이너가 사용하고 있는 Anonymous Page의 일정 퍼센트를 스왑 아웃 할 수 있다. --memory-swappiness 는 0 ~ 100 사이의 값을 지정할 수 있으며, 호스트 운영체제에 의해 발생하는 anonymous page의 스왑 아웃을 지정할 수 있다. 0이 설정되면, anonymous page 스왑 기능을 끈다 100이 설정되면, 모든 anonymous page들이 스왑 아웃 될 수 있다. 설정하지 않으면 호스트 운영체제의 설정 값이 상속되어 사용되어진다.|
|--memory-reservation|--memory 설정으로 명시한 값보다 작은 값의 soft limit을 명시할 수 있게 해준다. 이 값은 Docker가 컨텐션을 감지하거나 호스트 머신에서 메모리 가용률이 낮아졌을 때 활성화된다. --memory-reservation 값은 --memory 설정 값보다 작아야한다. Soft limit이니만큼 컨테이너의 메모리 사용양은 이 제한을 넘을 수 있다.|
|--kernel-memory|컨테이너가 사용할 수 있는 커널 메모리 양을 제한한다. 최소 값은 4m(4메가 바이트)이다. 커널 메모리는 스왑 아웃 되지 않기 때문에 커널 메모리를 지나치게 많이 사용하는 컨테이너는 호스트 머신의 커널 메모리를 점유하게 될 수 있다. 이는 호스트 머신이나 다른 컨테이너에게 사이드 이펙트를 만들어 낼 가능성이 있다.|
|--oom-kill-disable|OOM (Out-Of-Memory) 이 발생했을 때, 커널이 컨테이너에 존재하는 프로세스를 죽일 것인지 지정할 수 있는 옵션이다. 기본 설정은 프로세스를 죽인다. --oom-kill-disable 옵션이 켜져있으면, 컨테이너의 메모리 사용량이 늘어나다가 호스트 운영체제에 의해서 호스트 시스템의 프로세스가 Kill을 맞을 수 있다. 특정 상황에 쓰면 유용하다.|

#### **Container 메모리 제한**
- `--memory`를 지정하면 Container의 메모리를 제한할 수 있다.
- 설정한 메모리 이상으로 사용하면 Container는 자동 종료된다.

```
> docker run -d ^       # -d 데몬 형태로 생성 
> --memory="1g" ^       # --memory: 컨테이너의 메모리를 제한, 여기서는 1GB로 제한
> --memory-swap="3g" ^  # --memory-swap: 스왑메모리를 설정할 수 있다, 여기서는 3GB로 설정했다.
> --name memory_1g ^    # --name: 컨테이너의 이름을 설정, 여기서는 "memory_1g"가 이름
> nginx                 # 이미지는 nginx를 사용
```

- `--memory-swap`옵션을 --memory옵션이 설정되어 있을 때에 의미를 갖는다.
- 스왑 동작은 Container가 할당된 메모리를 모두 사용한 경우에도 디스크 공간을 끌어와서 쓸 수 있게 도와준다.

#### **CPU 자원 관리**
- Docker Container는 호스트 머신의 CPU자원을 제한 없이 사용할 수 있다.
- 즉, 하나의 Container가 수행될 때 CPU-Intensive한 작업을 실행하면, 호스트 머신의 CPU가 자원을 점유해 버릴 수 있다는 것이다.
- 이는 Container간 독립적인 환경을 구축할 수 없음을 의미하기 때문에 CPU사이클에 대한 쿼터를 적용할 필요가 있다는 것이다.
- 가상 머신의 경우 특정 갯수의 CPU를 할당 받지만 Container의 경우 전체 사용량에서 얼마나 사용할지를 결정한다.

|옵션|설명|
|----------|--------------|
|--cpus=|Docker 컨테이너가 몇 개의 CPU를 사용할 것인지 명시한다. 예를 들어, 호스트 머신에 2개의 CPU가 있고, --cpus="1.5"라고 옵션을 명시하면, 컨테이너가 최대한 1.5개의 CPU 파워를 사용할 수 있다는 의미이다. --cpus="1.5"는 --cpu-period="100000" 옵션과 --cpu-quota="150000" 옵션을 동시에 준 것과 동일한 의미를 갖는다. Docker 버전 1.13 이상에서 지원된다.|
|--cpu-period=|CPU CFS 스케줄러 Period를 의미하며, --cpu-quota 옵션과 같이 사용한다. 기본 값은 1초이며, 마이크로초로 표현된다. 대부분의 유저는 이 값을 변경하지 않고 사용한다. Docker 1.13 버전 이후에는 --cpus 옵션을 사용하는 것을 권장한다.|
|--cpu-quota=|컨테이너의 CPU CFS 쿼터(Quota)를 의미한다. --cpu-quota 로 입력한 값은 --cpu-period 로 입력한 값 중 얼마를 컨테이너에게 할당할 것인가를 의미한다. 즉, --cpu--quota/--cpu-period 가 사용할 리소스의 퍼센트이다. 마찬가지로 Docker 1.13 버전 이후에는 --cpus 옵션을 사용하는 것을 권장한다.|
|--cpuset-cpus|컨테이너가 사용할 수 있는 CPU 혹은 코어를 제한한다. 코어를 지정하는 인덱스는 0부터 시작한다. 즉, 코어가 4개 있는 호스트 머신에서 컨테이너를 띄울 때, --cpuset-cpus="0-3" 이라고 명시하면, 해당 컨테이너는 첫번 째, 두 번째, 세 번째, 네 번째 코어를 사용하게 된다. --cpuset-cpus="0, 2" 이라고 명시하면, 첫 번째와 세 번째 코어를 사용하도록 명시한 것이다.|
|--cpu-shares|컨테이너를 실행할 때 고려할 가중치라고 보면된다. 설정의 기본 값은 1024이며, 설정한 값은 상대적으로 사용된다. --cpu-shares=2048 로 설정하면, 기본 값보다 두 배 많은 CPU 자원을 할당한다. 이 설정 값은 리눅스 커널의 cgroups에서 사용된다. 이 값을 이용하여 CPU 자원을 50:50으로 할당할 수도 있고, 80:20으로 할당할 수도 있다.|