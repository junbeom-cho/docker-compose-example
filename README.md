# docker-compose-example
여러 서비스의 `docker-compose.yaml`을 일관적으로 정리하기 위한 저장소입니다.

## 1. 목표
- 서비스별 compose 파일 구조를 통일합니다.
- 필요한 서비스를 빠르게 배포합니다.


## 2. 파일 작성 규칙

### 기본 원칙
- 민감정보(password, token, api-key)는 `.env-sample` 파일로 분리합니다.
- `docker-compose.yaml`에는 서비스를 실행시키기 위한 필수 옵션과 선택 옵션을 포함합니다.
- 컨테이너의 볼륨은 compose와 같은 폴더에 위치하는 것을 기본으로 한다.

### 네이밍 규칙
- 폴더 및 파일명 : `kebab-case`
- 컨테이너명 : `snake_case`
- 네트워크명 : `snake_case`
- API 및 비밀번호 등 민감정보 : `UPPER_SNAKE_CASE`

### compose 작성 규칙
- compose의 환경변수를 작성할 때 `KEY: VALUE`의 형태로 작성한다.
- 기본은 `latest` 버전으로 명시하나 서비스에 따라 수정한다.

### 3. 실행 및 관리

#### 1. 실행
1. `.env` 파일 채우기 (필요 시)
2. `docker-compose.yaml` 파일이 존재하는 폴더로 이동
3.
```bash
# 컨테이너를 백그라운드로 실행
docker compose up -d
```

#### 2. 컨테이너 관리

##### 로그 관리
```bash
# 컨테이너의 로그 확인
docker logs -f <container-name>
```

