## Docker Commit: 컨테이너의 현재 상태를 이미지로 박제하기

`docker commit`은 **실행 중이거나 멈춰있는 컨테이너의 변경 사항을 새로운 이미지로 생성**할 때 사용하는 명령어입니다. 흔히 '스냅샷'을 찍는 것에 비유하며, 소스 코드 수정, 패키지 설치, 설정 변경 등이 완료된 컨테이너를 재사용 가능한 이미지로 만들 때 유용합니다.

---

### 1. 기본 문법 및 옵션

```bash
docker commit [OPTIONS] CONTAINER [REPOSITORY[:TAG]]

```

| 옵션 | 설명 |
| --- | --- |
| **`-a, --author`** | 이미지의 작성자를 지정합니다. (예: "John Doe [john@example.com](mailto:john@example.com)") |
| **`-m, --message`** | 변경 사항에 대한 커밋 메시지를 작성합니다. (Git의 commit 메시지와 유사) |
| **`-c, --change`** | Dockerfile 지시어(ENV, EXPOSE, CMD 등)를 적용하여 이미지를 변경합니다. |
| **`-p, --pause`** | 커밋하는 동안 컨테이너를 일시 정지합니다. (기본값: true, 데이터 일관성을 위해 권장) |

---

### 2. 실전 사용 예시

웹 서버 컨테이너에 접속하여 특정 설정을 변경한 후, 이를 나만의 이미지로 만드는 과정입니다.

1. **컨테이너 실행 및 수정**:
```bash
docker run -it --name my_web_env ubuntu:22.04 /bin/bash
# (컨테이너 내부) apt update && apt install -y nginx
# (컨테이너 탈출) exit 또는 Ctrl+P, Q

```


2. **변경사항 확인 (`diff`)**:
```bash
docker diff my_web_env

```


3. **이미지로 커밋**:
```bash
docker commit -a "Expert_Instructor" -m "Installed nginx" my_web_env my_custom_nginx:v1.0

```


4. **생성된 이미지 확인**:
```bash
docker images

```



---

### 3. 전문가의 실전 TIP 💡

#### ⚠️ 왜 `docker commit`보다 `Dockerfile`을 권장하는가?

전문가들은 운영 환경에서 `docker commit` 사용을 지양하고 [Dockerfile](https://docs.docker.com/engine/reference/builder/)을 통한 이미지 빌드를 권장합니다. 그 이유는 다음과 같습니다.

* **재현성(Reproducibility)**: `commit`으로 만든 이미지는 내부에서 어떤 명령어가 실행되었는지 기록(History)을 추적하기 어렵습니다.
* **투명성**: `Dockerfile`은 이미지 생성 과정을 코드로 관리하므로 팀원 간 공유와 리뷰가 가능합니다.
* **용량 최적화**: 레이어 구조를 효율적으로 설계하기 위해서는 `Dockerfile`의 [Multi-stage build](https://docs.docker.com/build/building/multi-stage/)가 필수적입니다.

#### ✅ 그럼 언제 `commit`을 쓰는가?

* **트러블슈팅**: 복잡한 환경에서 오류를 수정하던 중, 현재의 "망가진" 상태나 "수정된" 상태를 즉시 보존해야 할 때.
* **보안 분석**: 침해 사고가 발생한 컨테이너의 상태를 그대로 복제하여 분석 환경으로 옮길 때.

---

### 4. 자주 오해하는 부분

* **데이터 볼륨(Volume)**: 컨테이너에 마운트된 외부 볼륨(`-v` 옵션 사용)의 데이터는 `docker commit` 시 **이미지에 포함되지 않습니다.** 오직 컨테이너 내부의 Writable Layer에 저장된 내용만 커밋됩니다.
* **중지 상태**: 컨테이너가 `Exited` 상태여도 커밋은 가능합니다. 삭제하기 전 상태를 저장하고 싶다면 유용합니다.

---

### 관련 링크 및 출처

* [Official Docker Documentation: docker commit](https://docs.docker.com/engine/reference/commandline/commit/)
* [Docker Best Practices](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)

**다음 단계로 이 명령어를 통해 생성된 이미지의 레이어 구조를 확인하는 `docker inspect`나 `docker history` 사용법을 알아볼까요?**
