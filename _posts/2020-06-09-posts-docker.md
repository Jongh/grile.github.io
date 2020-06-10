---
title: "tensorflow 가상환경을 구현하면서 배우는 docker 사용법"
date: 2020-06-09 15:04:19 +0900
categories: docker, tensorflow
---

## docker 가상환경 구축하기

### docker 설치 및 확인

- [docker 다운로드 페이지](https://docs.docker.com/get-docker/)에서 window용 docker를 다운로드한다.

- 다운로드한 파일을 실행하여 docker를 설치한다.

- docker는 자동으로 구동되며, 다음과 같이 명령을 수행하여 버전을 확인한다.

```bash
docker version
```

- 도커 이미지의 다운로드와 실행이 되는지 확인하기 위해 다음과 같이 명령을 수행한다.

```bash
docker run hello-world
```

### tensorflow docker 설치

- [tensorflow 설치 - Docker 페이지](https://www.tensorflow.org/install/docker?hl=ko)에서 다양한 설치 방법을 확인할 수 있다.

- 여기서는 python3을 사용하며, jupyter notebook을 지원하는 버전의 tensorflow를 설치한다. 해당하는 버전의 이미지의 태그는 latest-py3-jupyter이다.

- window cmd 혹은 power shell에서 docker에 tensorflow 이미지를 pull한다.

```bash
docker pull tensorflow/tensorflow:latest-py3-jupyter
```

- pull한 이미지를 tensorflow라는 이름으로 docker에 올려서 구동한다.

```bash
docker run -it -p 8888:8888 tensorflow/tensorflow:latest-py3-jupyter --name tensorflow
```

- 구동을 수행하면 jupyter notebook에 접속이 가능한 url을 아래와 같이 로그로 출력하는데, 해당 url을 복사해서 웹페이지에서 구동한다.

```bash
http://127.0.0.1:8888/?token=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

### 가상환경에서 예제코드를 사용하기 위한 작업

- jupyter notebook을 이용하여 tensorflow 예제를 실행하다보면 에러가 발생하는 코드가 있다. 예를 들면 아래에 표시한 overfit_and_underfit.ipynb 파일의 2번째 입력 부분이 있다.

```python
!pip install git+https://github.com/tensorflow/docs

import tensorflow_docs as tfdocs
import tensorflow_docs.modeling
import tensorflow_docs.plots
```

- 해당 부분은 git이 해당 가상환경에 설치되어 있지 않기 때문에 수행할 때 에러가 발생하며, 해당 가상환경에 git 패키지를 설치하는 작업이 필요하다.

- 일단 git 패키지를 설치하기 위해 cmd나 power shell을 이용하여 가상환경에 접속한다.

```bash
docker exec -it tensorflow bash
```

- 접속을 하면 git 패키지를 설치한다. tensorflow 가상환경은 ubuntu 18.04.3 LTS 버전이므로, 아래와 같이 명령을 수행하여 git을 설치한다.

```bash
apt-get update
apt-get install git -y
```

- 작업 수행을 완료하면, jupyter notebook의 모든 예제를 수행할 수 있다. 웹페이지에서 localhost:8888에 접속해 tensorflow 예제를 실행해보도록 하자.
