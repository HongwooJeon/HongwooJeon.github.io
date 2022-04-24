---
title: 3. Container 환경 및 네트워크
author: Banjung
date: 2022-04-17 08:00:00 -0500
categories: [Docker, Chapter 3(컨테이터)]
tags: [Docker Container 환경 및 네트워크]
math: true
mermaid: true
---

## Summary

```
## 컨테이너 환경변수 설정
> docker container run -d -e MYSQL_ROOT_PASSWORD=1234 --name db1 mysql

## 네트워크 목록 표시
> docker network ls

## 네트워크 상세 정보 확인
> docker network inspect bridge

## 네트워크 생성
> docker network create --driver=bridge d-net
> docker network create --subnet 192.168.0.0/24 --gateway 192.168.0.254 customnet

## 네트워크 연결
> docker container run -it --name a1 alpine
> docker attach a1
/ # ifconfig
> docker network connect customnet a1
> docker attach a1
/ # ifconfig
> docker container run -it --net customnet --name a1 alpine
> docker network disconnect customnet a1

## 네트워크 삭제
> docker network rm d-net

## 컨테이너 링크
> docker container run -itd --name a1 alpine
> docker container run -itd --name a2 --link a1 alpine
> docker attach a2
/ # ping -c1 a1
> docker attach a1
/ # ping -c1 a2

## 외부에서 컨테이너 접속
> docker container run -d -p 80:80 --name web2 httpd
> docker container ps
> curl localhost
```
## 1. Container 환경 변수 설정
`docker container run -e`

- docker container run 명령의 `-e` 옵션을 사용하여 Container 내부에 환경 변수를 설정할 수 있다.
- 보통 설정 값이나 비밀번호를 전달할 때 사용한다.
- ex> centos Container를 a=100, b20, c=30으로 환경 변수를 설정한다.

```
> docker container run -it -e a=100 --name c1 centos
# echo $a
100
[Ctrl + P + Q]
> docker container run -it -e b=20 -e c=30 centos
# echo $b
20
# echo $c
30
```

- ex> mysql 이미지를 사용하여 Container를 생성한다.
  - 포그라운드로 실행해야한다.
  - 메시지는 표준 에러로 출력되며, 몇몇 환경 변수를 설정해야 한다는 문구가 나온다.

```
> docker container run -it --name db1 mysql
error: database is uninitialized and password option is not specified
  You need to specify one of MYSQL_ROOT_PASSWORD, MYSQL_ALLOW_EMPTY_PASSWORD and MYSQL_RANDOM_ROOT_PASSWORD
```  

- ex> mysql 이미지를 자세히 확인한다.
  - 보통 이미지가 실행되면 cmd에 지정된 어플리케이션이 실행된다.
  - `Entrypoint가 지정되어 있으면 Entrypoint가 cmd보다 우선적으로 실행된다.`
  - Entrypoint는 쉘 스크립트로 이루어져 있으며 Container가 실행되기 위한 조건을 확인하고 일치하지 않으면 Container를 실행하지 않거나 특정 작업을 수행한다.

```
> docker image inspect mysql
... <생략>...
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "GOSU_VERSION=1.7",
                "MYSQL_MAJOR=8.0",
                "MYSQL_VERSION=8.0.12-1debian9"
            ],
            "Cmd": [
                "/bin/sh",
                "-c",
                "#(nop) ",
                "CMD [\"mysqld\"]"
            ],
... <생략>...
            "Entrypoint": [
                "docker-entrypoint.sh"
            ],
... <생략>...
```  

- 환경변수를 MYSQL_ROOT_PASSWORD=1234로 지정한다.

```
 > docker container run -d -e MYSQL_ROOT_PASSWORD=1234 --name db1 mysql
 65dcb4a71896ae110ef0cc89f9e6d7ba00f9e78846ede9c169b228d1369aadb6
 > docker container ls
 CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                 NAMES
 65dcb4a71896        mysql               "docker-entrypoint.s…"   3 seconds ago       Up 2 seconds        3306/tcp, 33060/tcp   db1
```

## 2. Conainer Network 관리
#### **도커 네트워크란?**
- Container로서 실행중인 `mysql server`에 접근하기 위해서는 Container 실행 시, `--link` 옵션을 이용해 Container 간의 연결이 필요하다.
- 같은 Docker Host내에서 실행 중인 Container간 연결할 수 있도록 돕는 네트워크다.

#### **Container 네트워크 구조**
- 만약 외부와 연결을 해야할 경우에는 호스트에 veth(=virtual eth)라는 네트워크 인터페이스를 생성하고 컨테이너의 eth와 연결이 된다.
- veth 인터페이스는 사용자가 직접 생성할 필요 없이 도커엔진에 의해 자동으로 생성된다.
- veth 인터페이스 뿐만 아니라 docker()라는 브리지도 있는데 이는 veth 인터페이스와 바인딩되어 호스트의 eth 인터페이스와 연결을 해준다.

#### **네트워크 유형**
**bridge**
- docker() bridge와 비슷하지만 사용자가 정의한 브리지를 생성해 각 컨테이너에 연결하는 네트워크 구조로 컨테이너는 연결된 브리지를 통해 외부와 통신할 수 있다.
- 보통 컨테이너가 사용하는 네트워크를 추가로 생성할 때에는 bridge 네트워크를 생성한다.
- 네트워크 생성 시 별도의 드라이버(bridge, host, none 등)을 지정하지 않으면 기본 값으로 생성되는 네트워크이다.
- ex> customnet 네트워크로 컨테이너를 생성한 뒤 네트워크를 확인한다.

```
> docker container run -it --net customnet --name a1 alpine
/ # ifconfig
eth0      Link encap:Ethernet  HWaddr 02:42:C0:A8:00:01
          inet addr:192.168.0.1  Bcast:192.168.0.255  Mask:255.255.255.0
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:15 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0
          RX bytes:1580 (1.5 KiB)  TX bytes:0 (0.0 B)
```

**host**
- host 유형의 네트워크는 호스트의 네트워크를 공유한다.
- Container 내부의 어플리케이션을 별도의 포트 포워딩 없이 바로 서비스할 수 있다.
- ex> host 네트워크로 Container를 생성한 뒤 네트워크를 확인한다.

```
> docker container run -it --net host --name a2 alpine
/ # ifconfig
...<생략>
eth0      Link encap:Ethernet  HWaddr 08:00:27:D1:E8:E8
          inet addr:10.0.2.15  Bcast:10.0.2.255  Mask:255.255.255.0
          inet6 addr: fe80::82db:bc1f:34fb:7c50/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:200313 errors:0 dropped:0 overruns:0 frame:0
          TX packets:49657 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:226258165 (215.7 MiB)  TX bytes:3151008 (3.0 MiB)
...<생략>
```

**none**
- Container에 어떤 네트워크도 할당하지 않는다. 외부와 단절된다.
- ex> none 네터워크로 Container를 생성한 뒤 네트워크를 확인한다.

```
> docker container run -it --name a1 --net none alpine
/ # ifconfig
lo        Link encap:Local Loopback
          inet addr:127.0.0.1  Mask:255.0.0.0
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1
          RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)
```

#### 네트워크 관리
`docker network COMMAND`

- docker에서 Container 네터워크를 관리하려면 `docker network`명령을 사용한다.

```
> docker network --help
Usage:  docker network COMMAND
Manage networks
Commands:
  connect     Connect a container to a network
  create      Create a network
  disconnect  Disconnect a container from a network
  inspect     Display detailed information on one or more networks
  ls          List networks
  prune       Remove all unused networks
  rm          Remove one or more networks
Run 'docker network COMMAND --help' for more information on a command.
```

#### 네트워크 목록 표시
`docker network ls`
- docker의 네트워크는 bridge, host, none 세 종류가 있다.

```
> docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
948486320b16        bridge              bridge              local
9997deede5a1        host                host                local
48be711d1ba7        none                null                local
```

#### 네트워크 상세 정보 확인
`docker network inspect [OPTIONS] NAME | ID [NAME|ID...]`

- 네트워크의 상세 정보를 확인하려면 `docker network inspect` 명령을 사용한다.

```
> docker network inspect --help
Usage:  docker network inspect [OPTIONS] NAME|ID [NAME|ID...]
Return low-level information on Docker objects
Options:
  -f, --format string   Format the output using the given Go template
  -s, --size            Display total file sizes if the type is container
      --type string     Return JSON for specified type
```

- ex> 기본적으로 존재하는 bridge 네트워크를 확인한다.

```
> docker network inspect bridge
[
    {
        "Name": "bridge",
        "Id": "eb2a64dc0eb56235d3b09b16b91f3b1987b43dc91f49bdf7f0cf2340b033f30d",
        "Created": "2018-10-22T14:22:06.68394493+09:00",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": null,
            "Config": [
                {
                    "Subnet": "172.17.0.0/16"
                }
            ]
        },
... <생략>
            "com.docker.network.bridge.name": "docker0",
            "com.docker.network.driver.mtu": "1500"
        },
        "Labels": {}
    }
]
```

#### 네트워크 생성
`docker network create [OPTIONS] NETWORK`

- `docker network create` 명령을 사용하면 도커 네트워크를 생성할 수 있다.

```
> docker network create --help
Usage:  docker network create [OPTIONS] NETWORK
Create a network
Options:
      --attachable           Enable manual container attachment
      --aux-address map      Auxiliary IPv4 or IPv6 addresses used by
                             Network driver (default map[])
      --config-from string   The network from which copying the configuration
      --config-only          Create a configuration only network
  -d, --driver string        Driver to manage the Network (default "bridge")
      --gateway strings      IPv4 or IPv6 Gateway for the master subnet
      --ingress              Create swarm routing-mesh network
      --internal             Restrict external access to the network
      --ip-range strings     Allocate container ip from a sub-range
      --ipam-driver string   IP Address Management Driver (default "default")
      --ipam-opt map         Set IPAM driver specific options (default map[])
      --ipv6                 Enable IPv6 networking
      --label list           Set metadata on a network
  -o, --opt map              Set driver specific options (default map[])
      --scope string         Control the network's scope
      --subnet strings       Subnet in CIDR format that represents a
                             network segment
```

- 네트워크 생성 시 별도의 드라이버(bridge, host, none..)을 지정하지 않으면 기본 값으로 생성되는 네트워크는 bridge이다.
  - ex> dlfmadl d-net인 bridge 유형의 도커 네트워크를 생성한다.
  - 네터워크 범위는 자동으로 172.18.0.0/16으로 설정된다.
  - --drive 옵션에서 지정할 수 있는 네터워크 드라이버는 `bridge` or `overlay`이다.
    - overlay 네트워크는 여러 개의 호스트에 걸쳐 있는 네트워크이다.

```
> docker network create --driver=bridge d-net
d9c9fe157e6d173e3609e8bac426ec52aeda330ead3996641e17dc525c545f7d
> docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
eb2a64dc0eb5        bridge              bridge              local
d9c9fe157e6d        d-net               bridge              local
4395a69bc04c        host                host                local
8d28eb905273        none                null                local
> docker network inspect d-net
...<생략>...
                    "Subnet": "172.18.0.0/16",
                    "Gateway": "172.18.0.1"
...<생략>...
```    

- 네트워크 범위를 설정하려면 `--subnet`옵션을 사용하며 게이트웨이는 `--gateway` 옵션을 사용한다.
  - ex> subnet이 192.168.0.0/24이고 gateway가 192.168.0.254인 customnet 이름의 네트워크를 생성한다.

```
> docker network create --subnet 192.168.0.0/24 --gateway 192.168.0.254 customnet
63f54042c3b8e72f145847d16f8b94ab46cb2e23b1f19b9ac7f4806f4b3bd7a3
> docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
eb2a64dc0eb5        bridge              bridge              local
63f54042c3b8        customnet           bridge              local
d9c9fe157e6d        d-net               bridge              local
4395a69bc04c        host                host                local
8d28eb905273        none                null                local
> docker network inspect customnet
... <생략>
                    "Subnet": "192.168.0.0/24",
                    "Gateway": "192.168.0.254"
... <생략>
```

#### 네트워크 연결
`docker network connect [OPTIONS] NETWORK CONTAINER`

- ex> a1 Conainer에 customnet 네트워크를 연결 시킨다.

```
> docker container run -it --name a1 alpine
Unable to find image 'alpine:latest' locally
latest: Pulling from library/alpine
e7c96db7181b: Pull complete
Digest: sha256:769fddc7cc2f0a1c35abb2f91432e8beecf83916c421420e6a6da9f8975464b6
Status: Downloaded newer image for alpine:latest
/ #
> docker attach a1
/ # ifconfig
eth0      Link encap:Ethernet  HWaddr 02:42:AC:11:00:03
          inet addr:172.17.0.3  Bcast:172.17.255.255  Mask:255.255.0.0
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:9 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0
          RX bytes:718 (718.0 B)  TX bytes:0 (0.0 B)
lo        Link encap:Local Loopback
          inet addr:127.0.0.1  Mask:255.0.0.0
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1
          RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)
          / # read escape sequence
> docker network connect customnet a1
> docker attach a1
/ # ifconfig
eth0      Link encap:Ethernet  HWaddr 02:42:AC:11:00:03
          inet addr:172.17.0.3  Bcast:172.17.255.255  Mask:255.255.0.0
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:12 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0
          RX bytes:928 (928.0 B)  TX bytes:0 (0.0 B)
eth1      Link encap:Ethernet  HWaddr 02:42:C0:A8:00:01
          inet addr:192.168.0.1  Bcast:192.168.0.255  Mask:255.255.255.0
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:9 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0
          RX bytes:758 (758.0 B)  TX bytes:0 (0.0 B)
lo        Link encap:Local Loopback
          inet addr:127.0.0.1  Mask:255.0.0.0
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1
          RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)
/ #
```

- 사용자가 생성한 네트워크를 Container가 사용하려면 docker container run 명령의 `--net 옵션`을 사용한다.
  - ex> alpine 이미지를 a1으로 Container화를 하고, 사용자가 생성한 customnet 네트워크를 Container가 사용한다.

```
> docker container run -it --net customnet --name a1 alpine   
```  

- 네트워크에 대한 연결을 해제할 때는 `docker network disconnect` 명령을 사용한다.
  - ex> a1 Container에 customnet 네트워크 연결을 해제한다.

```
> docker network disconnect customnet a1
```  

#### 네트워크 삭제
`docker network rm NETWORK [NETWORK...]`

- ex> web-network 를 삭제한다.

```
> docker network rm web-network
web-network
```

## 3. Contaienr 통신
- docker는 Container를 연결시켜주는 링크 기능과 호스트 외부에서 접근할 수 있도록 `포트 포워딩`을 사용할 수 있다.

#### Container Link
- Container는 IP를 동적으로 받아오기 때문에 항상 일치하지 않는다.
- 이는 서로 다른 두 개의 Container를 연결해서 사용할 때 문제가 될 수 있다.
- Linux 시스템 또는 Container는 다른 시스템이나 Container와 통신할 때 IP기반으로 하기 때문이다.
- 이 때 `Container의 IP가 동적으로 변경되어도 통신이 유지되도록 하는 기능`이 Container Link이다.

**Container 연동시 link를 사용해야 하는 이유**
- 동일 host상에 배포된 Container사이에는 Private IP를 이용해 통신이 가능하다.
- ex> 동일 host상에 running중인 두 개의 Containe이다.

```
CONTAINER ID      IMAGE               COMMAND                  CREATED             STATUS              PORTS                    NAMES
ca7f026ff0ad        httpd               "httpd-foreground"       55 minutes ago      Up 55 minutes       0.0.0.0:80->80/tcp       web01
17b6c5f037a9       mysql               "docker-entrypoint.sh"   About an hour ago   Up 50 minutes       0.0.0.0:3306->3306/tcp   mysql
```

- ex> 두 Container mysql과 web01의 Private IP는 아래와 같다.

```
> docker inspect -f "{{ .NetworkSettings.IPAddress }}" mysql
172.17.0.6
> docker inspect -f "{{ .NetworkSettings.IPAddress }}" web01
172.17.0.3
```

- ex> 만약, web01 --> mysql(172.17.0.6)로 IP를 기반으로 ping을 보내면 아래와 같이 응답함을 볼 수 있다.

```
> docker exec -t web01 ping 172.17.0.6
PING 172.17.0.6 (172.17.0.6): 56 data bytes
64 bytes from 172.17.0.6: icmp_seq=0 ttl=64 time=0.103 ms
64 bytes from 172.17.0.6: icmp_seq=1 ttl=64 time=0.117 ms
64 bytes from 172.17.0.6: icmp_seq=2 ttl=64 time=0.111 ms
64 bytes from 172.17.0.6: icmp_seq=3 ttl=64 time=0.114 ms
64 bytes from 172.17.0.6: icmp_seq=4 ttl=64 time=0.111 ms
64 bytes from 172.17.0.6: icmp_seq=5 ttl=64 time=0.116 ms
...
```

- 하지만, Container 사이의 IP 기반 연동은 문제점을 안고 있다.
- **Container의 IP는 언제든 변할 수 있는 유동적인 성격을 띄고 있기 때문이다.**
- `Container는 일종의 Process이므로, 언제든 생성/소멸 될 수 있음을 전제로 해야한다.`
- 만약 Container가 중지 되었다가 시작하면, Process가 kill되었다가 다시 새롭게 생성되는 것과 같다.
- 이때, Container에게 부여되는 Private IP는 언제든 별할 수 있다.
- 따라서, Container 사이 연동을 하려면, IP 기반의 설정은 권고되지 않는다.
- 그 해결 방법으로 권고 되고 있는 방법이 바로 `link` 옵션이다.

`run --link`

- 서로 다른 두 개의 Container를 LINK로 연결하려면 docker container run 명령에 `--link` 옵션을 사용한다.
- ex> a1과 a2 Container를 생성한 뒤에 a2가 a1에 링크되도록 한다.
  - a2는 a1이라는 이름으로 통신이 가능하지만 a1은 a2라는 이름으로 통실할 수 없다.

```
> docker run -itd --name a1 alpine
1cca39d03936a80ab9917c50b1c87a68d273fa41998c89034084de6ce324aa9b
> docker run -itd --name a2 --link a1 alpine
46c3ed8634c6202d78970354211b07d50198b1642be300758d1b1e46162f2af2
> docker attach a2
/ # ping -c1 a1
PING a1 (172.17.0.2): 56 data bytes
64 bytes from 172.17.0.2: seq=0 ttl=64 time=0.131 ms
--- a1 ping statistics ---
1 packets transmitted, 1 packets received, 0% packet loss
round-trip min/avg/max = 0.131/0.131/0.131 ms
> docker attach a1
/ # ping -c1 a2
ping: bad address 'a2'
```  

- Container를 실행할 때, `--link`옵션을 사용하면 Container의 /etx/hosts 파일에 기록하기 때문이다.

```
> docker exec a1 cat /etc/hosts
...<생략>...
172.17.0.2      1cca39d03936
> docker exec a2 cat /etc/hosts
...<생략>...
172.17.0.2      a1 1cca39d03936
172.17.0.3      46c3ed8634c6
```

- `:`을 사용하여 별칭도 등록할 수 있다. alpine1으로 별칭을 등록한다.

```
> docker container run -itd --name a1 alpine
2bb5c8c798c93cbb5da86f10dcae8a9c17f4cf4f811bc6915604f380997b1c76
> docker run -itd --name a2 --link a1:alpine1 alpine
444775da89922c05a20d57b6a10c6741a3e3f9a1af4a4f2fe9e0a6bcf996de2f
> docker container exec a2 ping -c1 alpine1
PING alpine1 (172.17.0.2): 56 data bytes
64 bytes from 172.17.0.2: seq=0 ttl=64 time=0.177 ms
--- alpine1 ping statistics ---
1 packets transmitted, 1 packets received, 0% packet loss
round-trip min/avg/max = 0.177/0.177/0.177 ms
> docker container exec a2 cat /etc/hosts
...<생략>
172.17.0.2      alpine1 2bb5c8c798c9 a1
172.17.0.3      444775da8992
```

**link 방식의 한계**
- link옵션을 동일 docker host에 존재하는 Container 사이에서만 유효하다.
- 만약 다수의 docker host를 운영할 경우에 타 host에 상주하는 Container 사이에는 link 옵션 사용이 불가하다.
- Container의 hosts 파일의 관리를 docker host가 직접 수행하기 때문이다.
- 이러한 경우, docker swarm 같은 orchestration 툴을 도입하거나 dynamic DNS를 구축해 사용해야 한다.
