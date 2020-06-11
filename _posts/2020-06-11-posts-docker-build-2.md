---
title: "docker 이미지 빌드하기 - 빌드하고 구동하기"
date: 2020-06-10 14:25:10 +0900
categories: docker
tags: build python
---

## 빌드하고 구동하기

### Dockerfile 빌드하기

- docker에서 이미지 빌드하는 명령은 docker build를 사용한다. 로컬에서 빌드를 할 때 사용하는 명령은 아래와 같다.

```bash
docker build -f [DOCKERFILE] -t [NAME]:[TAG] [PATH]
```

- DOCKERFILE은 빌드에 사용할 Dockerfile의 경로를 포함한 파일명을 입력하는 부분이다.

- NAME은 이미지의 이름을 입력하는 부분이다.

- TAG는 버전이나 빌드 특성과 같은 이미지의 태그를 입력하는 부분이다.

- PATH는 빌드 작업을 수행할 때 사용할 작업 경로를 입력하는 부분이다.

- 만약 Dockerfile과 ADD할 데이터가 있는 로컬의 작업경로를 입력하는 부분이다.

- 앞에서 작성한 파이썬 환경을 구현한 이미지를 1.0 버전으로 빌드하려면 아래와 같이 명령을 사용하여 빌드할 수 있다.

```bash
docker build -f ./Dockerfile -t mypython:1.0 .
```

- 위의 명령을 실행하면 Dockerfile에 입력한 명령대로 작업이 수행되는 것을 확인할 수 있다.

- 만약 [dockerfile 만들기](https://jongh.github.io/docker/posts-docker-build-1/)에서 작성한 Dockerfile을 이용하다가 문제가 되었다면 아래의 내용을 지우고 다시 빌드를 수행하면 된다. 따로 준비하지 않았다면 requirements.txt 파일을 만들지 않았기 때문에 파일이 없어 에러가 발생할 수 있기 때문이다.

```bash
# requirements.txt 파일을 추가하고, pip를 이용하여 라이브러리를 설치한다.
ADD requirements.txt ./
RUN pip install --upgrade pip
RUN pip install -r requirements.txt
```

### docker 이미지를 등록하고 구동하기

- 도커 이미지의 등록과 구동은 [tensorflow 가상환경 구축하기](https://jongh.github.io/docker/posts-docker/) 에서 사용한 방식을 다시 확인하면서 수행할 예정이다. docker 이미지 구동은 아래와 같은 명령으로 수행하게 된다.

```bash
docker run [-it] [-p [HOSTPORT]:[CONTAINERPORT]] [--name [CONTAINERNAME]] [NAME]:[TAG] [CMD]
```

- docker 이미지의 구동은 해당 이미지를 docker 컨테이너에 올려 가상환경을 구성하고, 구성한 가상환경을 실행하여 이루어지게 된다. run 명령은 이미지를 컨테이너에 새로 올리고 구동시키는 명령이다.

- it 옵션은 interactive 옵션과 tty 옵션의 조합으로 사용자가 직접 터미널을 이용하여 서버와 상호작용을 하겠다는 의미이다. 사용하지 않으면 구동할 때 Dockerfile의 CMD를 수행하고 바로 종료한다.

- p 옵션은 포트를 지정하는 옵션으로 구동하는 이미지의 포트인 CONTAINERPORT와 docker를 구동하는 호스트의 HOSTPORT를 연결해준다. 설정하지 않으면 포트를 연결하지 않는다.

- CONTAINERNAME은 컨테이너에 등록할 이름을 입력한다. 이후 docker의 컨테이너를 관리할 때 해당 이름을 사용하여 관리를 수행할 수 있다. 입력하지 않으면 docker에서 생성하는 무작위 이름을 생성된다.

- NAME과 TAG는 구동하기 위한 이미지의 이름과 태그를 입력한다. 해당하는 이름의 이미지를 컨테이너에 올려 구동하게 된다.

- CMD는 구동할 때 실행한 명령을 입력한다. 이 명령은 Dockerfile에서 사용한 CMD를 덮어써서 실행하게 된다.

- 생성한 이미지를 컨테이너로 등록하고 구동하기 위해서는 아래와 같은 명령을 수행하면 된다.

```bash
docker run -it mypython:1.0 --name mypy
```

- 명령을 수행하였으면, bash 콘솔을 확인할 수 있다. 여기서 python3.8을 입력하면 설치한 파이썬을 실행시킬 수 있다.

### 등록된 docker 이미지를 구동하기

- 컨테이너에 등록된 이미지의 구동은 아래와 같은 명령을 실행하여 수행할 수 있다.

```bash
docker start [-i] [CONTAINERNAME]
```

- i 옵션은 interactive 옵션으로 실행 시 주어진 명령을 실행한 다음 바로 종료하지 않고 서버와 상호작용을 하며 작업할 수 있다.

- CONTAINERNAME은 컨테이너에 등록된 이름으로 구동할 컨테이너의 이름을 입력하면 된다.

- 이번에 구성한 컨테이너를 실행시켜서 작업하기 위해서는 아래와 같이 입력하면 된다.

```bash
docker start -i mypy
```
