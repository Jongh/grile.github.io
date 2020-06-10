---
title: "docker 이미지 빌드하기 - Dockerfile 만들기"
date: 2020-06-10 14:25:10 +0900
categories: docker python
---

## docker 이미지 만들기

### docker 이미지를 만드는 이유

- 다양하게 구축된 docker 이미지는 [docker hub](https://hub.docker.com/)에서 찾아서 빌드할 수 있다. 그러나 업무 등을 위하여 자신만의 서버 이미지를 구축하여 사용해야 할 경우도 있다. 이럴 때 사용하기 위해서 docker 이미지를 직접 만드는 방법을 정리했다.

### Dockerfile 명령어

- Dockerfile은 Docker이미지를 만들기 위한 스크립트이다. 여기에서는 파이썬3을 실행하는 Dockerfile생성하는 과정을 통해 자주 사용하는 명령을 알아보고자 한다.

- 이번 Dockerfile 만들기에서 사용할 명령은 FROM, WORKDIR, RUN, ADD, CMD이다. 다른 명령들도 있지만, 기초적인 Dockerfile을 만들기 위해서는 위의 다섯 명령만 알고 있어도 충분한 작업이 가능하다.

- FROM은 Dockerfile을 통해 docker이미지를 생성할 때 사용할 바탕이 되는 이미지를 불러오는 명령이다. 자주 사용하는 리눅스 OS인 centos 7 이미지를 사용하기 위해서는 파일의 가장 위에 아래와 같이 입력한다.

```bash
FROM centos:7
```

- WORKDIR은 Docker 이미지 내부에서 경로를 이동할 때 사용하는 명령이다. 이 작업 이후에는 가상환경에서 어떤 명령을 수행하든 해당 경로에서 작업을 수행하게 되며, 작업 수행 경로를 바꾸기 위해서는 다시 WORKDIR 명령을 입력해야 한다. 시스템 작업은 root로 수행하기 때문에 root의 홈 경로인 /root로 다음과 같이 입력한다.

```bash
WORKDIR /root
```

- RUN은 가상환경에서 수행할 명령을 보내기위해 사용하는 명령이다. 이 명령을 통해서 가상환경은 다양한 작업을 수행하게 된다. 파이썬3을 빌드하기 위해 필요한 패키지 설치를 RUN 명령을 통해 수행할 수 있으며, 작업의 수행을 위해 다음과 같이 입력한다.

```bash
RUN yum install gcc openssl-devel libffi-devel bzip2-devel wget make -y
```

- ADD는 로컬에 있는 파일을 가상환경에 추가하는 명령이다. 이 명령을 통해 가상환경에 적재하려고 하는 파일을 올릴 수 있다. 여기에서는 파이썬을 구동하기 위해 사용하는 requirements.txt 파일을 가상환경에 추가할 예정이며, 작업의 수행은 다음과 같이 입력한다.

```bash
ADD requirements.txt ./
```

- CMD는 가상환경을 구동할 때 수행할 작업을 입력하는 명령이다. 이 명령이 있으면, docker를 구동할 때 우선 이 명령을 수행하게 된다. 구동하기 위한 환경이 파이썬이기 때문에 다음과 같이 명령을 작성하면 가상환경을 구동할 때 파이썬이 실행된다.

```bash
CMD python3.8
```

### Dockerfile 구성하기

- 위의 내용을 통해 Dockerfile에서 자주 사용하는 내용들을 확인해보았다. 이번에는 위의 명령을 조합해서 파이썬을 구동하기 위한 이미지를 구성하는 방법을 알아보도록 한다.

- 이미지를 구성하기 위해서는 다양한 작업을 차례대로 수행해야 한다. 수행하는 작업을 아래와 같이 정리해보았다.

 1. 바탕이미지를 설정한다. (centos 7)
 2. 작업을 수행할 경로를 지정한다.
 3. 파이썬 설치에 필요한 패키지를 설치한다.
 4. 파이썬 설치 파일을 다운로드하고, 압축을 푼다.
 5. 설치파일 경로로 이동하여 설치를 한다.
 6. requirements.txt 파일을 추가하고, pip를 이용하여 라이브러리를 설치한다.
 7. 가상환경 구동 시 파이썬을 실행하도록 한다.

- 해당 순서의 작업을 Dockerfile에 구성해보도록 한다. 구성은 아래와 같이 수행할 수 있다.

```bash
# 바탕 이미지를 설정한다.
FROM centos:7

# 작업을 수행할 경로를 지정한다.
WORKDIR /root

# 파이썬 설치에 필요한 패키지를 설치한다.
RUN yum install gcc openssl-devel libffi-devel bzip2-devel wget make -y

# 파이썬 설치파일을 다운로드하고, 압축을 푼다.
RUN wget https://www.python.org/ftp/python/3.7.1/Python-3.8.3.tgz
RUN tar xzf Python-3.8.3.tgz

# 압축해체한 경로로 이동하고, 설치한다.
WORKDIR /root/Python-3.8.3
RUN ./configure --enable-optimizations
RUN make altinstall

# requirements.txt 파일을 추가하고, pip를 이용하여 라이브러리를 설치한다.
ADD requirements.txt ./
RUN pip install --upgrade pip
RUN pip install -r requirements.txt

# 가상환경 구동 시 파이썬을 실행하도록 한다.
WORKDIR /root
CMD python3.8
```

- 도커 이미지의 빌드를 위한 Dockerfile의 생성은 완료되었다. 다음엔 docker 이미지를 빌드하고, 이를 컨테이너에 올려 실행해보도록 하겠다.
