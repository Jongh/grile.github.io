---
title: "docker 이미지 관리하기 - 알아두면 쓸만한 명령어들"
date: 2020-06-18 14:33:26 +0900
categories: docker
tags: managements
---

## 알아두면 쓸만한 명령어들

### 컨테이너 이름 바꾸기

- 컨테이너의 이름이 여러 이유에 의해 바꿔야 하거나 최초 컨테이너의 이름을 할당하지 않은 경우 컨테이너의 이름을 바꿔야 할 필요가 있다. 도커를 처음 사용할 땐 이러한 경우 컨테이너를 다시 올리는 경우도 있지만 컨테이너의 이름을 바꾸는 기능은 rename이라는 명령으로 도커에서 지원하고 있다.

```bash
# help 사용
docker rename --help

Usage:  docker rename CONTAINER NEW_NAME

Rename a container

# rename 수행
docker rename container_name new_name
```

- rename은 해당하는 컨테이너의 이름만 바꾸는 기능을 가지고 있기 때문에 옵션이 없는 것을 볼 수 있다.

### 로컬과 컨테이너사이의 파일 복사

- 로컬과 컨테이너사이에서 파일을 복사하는 cp라는 명령을 지원한다.

```bash
# help 사용
docker cp --help

Usage:  docker cp [OPTIONS] CONTAINER:SRC_PATH DEST_PATH|-
        docker cp [OPTIONS] SRC_PATH|- CONTAINER:DEST_PATH

Copy files/folders between a container and the local filesystem

Use '-' as the source to read a tar archive from stdin
and extract it to a directory destination in a container.
Use '-' as the destination to stream a tar archive of a
container source to stdout.

Options:
  -a, --archive       Archive mode (copy all uid/gid information)
  -L, --follow-link   Always follow symbol link in SRC_PATH

# cp 수행, my_container에 있는 /home/user 경로에 파일이 복사된다.
docker copy readme.txt my_container:/home/user

# tar 아카이브 복사는 표준 입출력을 이용하여 사용이 가능하다.
# 표준 입력을 통해 cp를 사용하면 tar 아카이브 압축을 해당 경로에서 바로 해제한다.
docker cp - my_container:/home/user < mytar.tar
# 컨테이너의 파일을 표준 출력으로 출력할 경우 출력 데이터는 바로 tar 아카이브로 변환하여 출력된다.
docker cp my_container:/home/user/myfiles - > myfiles.tar
```

- 도커 cp의 사용방법은 컨테이너의 이름을 넣는다는 특징을 빼면 전체적으로 터미널에서 사용하는 cp명령과 유사한 것을 알 수 있다. 그러나 tar 아카이브와 표준 입출력을 이용한 방법을 이용하여 다양한 파일을 이동시키거나 백업할 수 있다.

### 컨테이너 설정 업데이트

- 컨테이너 설정을 업데이트 하는 명령으로 update라는 명령이 있다. 해당 명령은 컨테이너가 사용하는 CPU와 메모리를 관리하기 위해 가지고 있는 명령이지만, 재시작 설정을 변경할 수 있는 restart옵션이 있다.

```bash
# help 사용
docker update --help

Usage:  docker update [OPTIONS] CONTAINER [CONTAINER...]

Update configuration of one or more containers

Options:
      --blkio-weight uint16        Block IO (relative weight), between 10
                                   and 1000, or 0 to disable (default 0)
      --cpu-period int             Limit CPU CFS (Completely Fair
                                   Scheduler) period
      --cpu-quota int              Limit CPU CFS (Completely Fair
                                   Scheduler) quota
      --cpu-rt-period int          Limit the CPU real-time period in
                                   microseconds
      --cpu-rt-runtime int         Limit the CPU real-time runtime in
                                   microseconds
  -c, --cpu-shares int             CPU shares (relative weight)
      --cpus decimal               Number of CPUs
      --cpuset-cpus string         CPUs in which to allow execution (0-3, 0,1)
      --cpuset-mems string         MEMs in which to allow execution (0-3, 0,1)
      --kernel-memory bytes        Kernel memory limit
  -m, --memory bytes               Memory limit
      --memory-reservation bytes   Memory soft limit
      --memory-swap bytes          Swap limit equal to memory plus swap:
                                   '-1' to enable unlimited swap
      --pids-limit int             Tune container pids limit (set -1 for
                                   unlimited)
      --restart string             Restart policy to apply when a
                                   container exits

# 프로세스 종료시 자동 restart하기
docker update --restart always my_container
```

- restart옵션의 정책은 no, on-failure, always, unless-stopped의 네가지가 있으며, no는 재시작을 안함, on-failure는 오류와 함께 종료했을 때 재시작, always는 컨테이너가 꺼졌을 때 무조건 재시작, unless-sopped는 종료 후 도커를 재실행할 때 재시작하는 정책이다.

- onfilure 정책은 재시작 횟수를 지정할 수 있으며, 해당 명령은 아래와 같다.

```bash
# 에러로 종료되면 3번까지 재실행하기
docker update --restart on-failure:3 my_container
```

- restart옵션은 run명령으로 컨테이너를 생성할 때도 사용할 수 있다.
