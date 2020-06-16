---
title: "docker 이미지 관리하기 - 이미지와 컨테이너 제거"
date: 2020-06-16 14:54:25 +0900
categories: docker
tags: managements
---

## 이미지와 컨테이너 제거

### 단독 이미지 제거

- 이미지를 다운로드 했거나 빌드했지만 더이상 사용하지 않는 이미지는 rmi명령으로 삭제가 가능하다. 사용방법은 help 옵션으로 쉽게 확인이 가능하며, 아래에 표시해보았다.

```bash
docker rmi --help

Usage:  docker rmi [OPTIONS] IMAGE [IMAGE...]

Remove one or more images

Options:
  -f, --force      Force removal of the image
      --no-prune   Do not delete untagged parents
```

- -f, --force는 강제적으로 삭제하는 옵션으로 해당 이미지가 컨테이너 등에 사용되어 일반적으로 삭제가 불가능 할 때도 삭제하게 하는 명령이다.

- --no-prune은 삭제하는 이미지가 가지고 있는 이름없는 상위 이미지의 삭제를 방지하는 옵션이다.

- rmi 명령은 여러개의 이미지를 같이 삭제할 수 있다. 여러개의 이미지의 이름 혹은 ID를 입력하여 해당하는 이미지들을 삭제 가능하다.

### 사용하지 않는 이름없는 이미지 제거

- 도커 이미지 중에는 이미지를 빌드할 때 에러가 발생하여 찌꺼기만 남아있는 이미지들이 있다. 해당 이미지들은 사용하지 않으면서도 용량만 차지하게 되는데, 이런 이미지들을 지우는 방법은 아래와 같다.

```bash
# 방법 1 - 이미지를 검색하여 직접 삭제하는 방법
docker rmi $(docker images -qa -f "dangling=true")

# 방법 2 - 도커에서 삭제하는 방법
docker image prune
```

- 첫번째 방법에서 $표시 이후의 부분을 확인해보자.

```bash
# 도커 이미지의 목록이 설명과 함께 표시된다.
docker images

# 도커 이미지의 목록 중 이름이 없는 이미지가 추가적으로 표시된다.
docker images -a

# 도커 이미지의 ID만 표시된다.
docker images -qa

# 사용하지 않는 도커 이미지의 ID만 표시된다.
docker images -qa -f "dangling=true"
```

- 이처럼 도커 이미지의 ID를 획득하여 입력으로 사용해서 이미지를 삭제하는 데 쓰이는 것을 알 수 있다.

### 컨테이너 제거

- 도커 컨테이너는 이미지가 도커에 올라가 실제로 구동 중인 가상환경이다. 따라서 컨테이너를 삭제하면 해당 컨테이너에서 작업했던 모든 내용을 잃어버릴 수 있다. 이미지 삭제와 같이 컨테이너 삭제도 도움말을 통해 쉽게 알 수 있다.

```bash
docker rm --help

Usage:  docker rm [OPTIONS] CONTAINER [CONTAINER...]

Remove one or more containers

Options:
  -f, --force     Force the removal of a running container (uses SIGKILL)
  -l, --link      Remove the specified link
  -v, --volumes   Remove anonymous volumes associated with the container
```

- -f, --force 옵션은 강제로 삭제하는 옵션으로 현재 구동 중인 컨테이너도 삭제하도록 하는 옵션이다.

- -l, -link 옵션은 컨테이너가 가지고 있는 다른 컨테이너와의 링크를 삭제하는 옵션이다.

- -v, --volumes 옵션은 컨테이너가 가지고 있는 볼륨을 같이 삭제하는 옵션이다.

- rm 명령도 rmi 명령과 같이 여러개의 컨테이너를 같이 삭제할 수 있다. 여러개의 컨테이너를 삭제하는 방법도 여러 컨테이너의 이름이나 ID를 입력하여 해당하는 컨테이너들을 삭제할 수 있다.

### 동작하지 않는 컨테이너 제거

- 동작하지 않는 컨테이너들은 가끔 나중에도 사용하지 않는 컨테이너일 확률이 높다. 따라서 현재 동작하지 않는 컨테이너가 있으면 아래와 같이 명령을 입력하여 삭제할 수 있다.

```bash
docker rm $(docker ps -qa)
```

- 위의 명령을 보면 도커 컨테이너를 표시하는 명령에 -qa 옵션을 이용하여 모든 컨테이너의 ID를 표시하도록 하고 있는 것을 확인할 수 있다. 따라서 사실 이 명령은 모든 컨테이너를 삭제하는 명령이다.

- 그러나 rm 명령의 옵션 중 -f 옵션을 참고하면, 해당 명령의 대상 중 현재 동작하고 있는 컨테이너는 삭제가 되지 않는다는 것을 알 수 있다. 만약 모든 컨테이너를 삭제하고 싶을 경우에는 -f 명령을 사용하면 된다. 이 때에는 동작 여부에 상관없이 모든 컨테이너가 삭제될 것이다.
