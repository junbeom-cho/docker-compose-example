# docker-compose-example

셀프호스팅 서비스들의 `docker-compose.yaml` 예제 모음.
각 폴더가 독립된 스택이며, 해당 폴더에서 바로 실행할 수 있다.

```bash
cd <서비스-폴더>
cp .env-sample .env   # .env-sample 이 있는 경우에만. 값을 채워야 한다
docker compose up -d
```

## 구조

```
<서비스명>/
  docker-compose.yaml   # 스택 정의 (폴더당 하나)
  .env-sample           # 비밀값이 필요한 경우에만
```

런타임 데이터(`./data`, `./config` 등)는 컨테이너가 첫 실행 때 폴더 안에 만들며 git 에는 올리지 않는다.

## 컨벤션

| 항목 | 규칙 |
| --- | --- |
| 파일 | 폴더당 `docker-compose.yaml` 1개 + 필요시 `.env-sample`. README·설정파일 두지 않는다 |
| 독립 실행 | clone 직후 `docker compose up -d` 로 뜬다. `external` 네트워크·호스트 절대경로 금지, DB/Redis 는 같은 파일 안에 포함 |
| 포트 | 호스트 포트 = 컨테이너 기본 포트 (`"3000:3000"`). 어쩔 수 없이 다르면 바로 위에 이유를 주석으로 남긴다 |
| 볼륨 | 상대경로 바인드 마운트 (`./data:/data`). named volume 금지. 도커 소켓·호스트 장치(`/var/run/docker.sock`, `/dev`, `/etc/localtime`)는 원래 경로 유지 |
| 리소스 | 모든 서비스에 `deploy.resources.limits` (cpus, memory) |
| 설정 | 최소한만. `logging:` 블록·장식용 healthcheck·배너 주석은 두지 않는다. healthcheck 는 `depends_on: condition: service_healthy` 로 실제 의존될 때만 |
| 주석 | 파일 맨 위에 서비스 설명 2~4줄, 각 서비스와 자명하지 않은 옵션에 한 줄씩 |
| 환경변수 | `${}` 는 비밀번호·API 키·토큰 등 노출되면 안 되는 값에만. 포트·경로·도메인·TZ·DB이름은 리터럴. `${}` 를 쓰면 `.env-sample` 에 같은 변수명으로 넣는다 |
| 네트워크 | `networks.default.name: <폴더명>` 하나만. 각 서비스는 `networks: [default]` |

### YAML 스타일

```yaml
services:
  app:
    environment:      # 맵 형식 (- KEY=VALUE 리스트 금지)
      KEY: VALUE
      SECRET: ${SECRET}
    ports:            # 리스트 형식, 따옴표
      - "3000:3000"
    volumes:
      - ./data:/data
    networks:
      - default

networks:
  default:
    name: app
```

## 서비스 목록

| 서비스 | 포트 | .env | 설명 |
| --- | --- | :---: | --- |
| [actual](actual) | 5006 | | 개인 가계부 (복식부기 예산 관리) |
| [adguard-home](adguard-home) | 53, 3000, 80 | | 네트워크 전체 DNS 광고/추적 차단 |
| [affine](affine) | 3010 | O | Notion 스타일 문서/화이트보드 워크스페이스 |
| [authentik](authentik) | 9000, 9443 | O | SSO / IdP (OAuth2, SAML, LDAP) |
| [caddy](caddy) | 80, 443 | O | 리버스 프록시 (Cloudflare DNS 인증서) |
| [cloudbeaver](cloudbeaver) | 8978 | | 웹 기반 DB 관리 도구 |
| [code-server](code-server) | 8443 | O | 브라우저에서 쓰는 VS Code |
| [dockge](dockge) | 5001 | | Docker Compose 스택 관리 UI |
| [dockhand](dockhand) | 3000 | O | 도커 컨테이너/스택 관리 UI |
| [dozzle](dozzle) | 8080 | | 컨테이너 로그 실시간 뷰어 |
| [filebrowser](filebrowser) | 80 | | 웹 파일 탐색기 |
| [forgejo](forgejo) | 3000, 2222 | O | 자체 호스팅 Git 서비스 |
| [homelabel](homelabel) | 3000, 8001 | O | 홈랩 인프라 시각화 |
| [homepage](homepage) | 3000 | | 서비스 링크/상태 대시보드 |
| [it-tools](it-tools) | 8080 | | 개발자용 웹 유틸리티 모음 |
| [kopia](kopia) | 51515 | O | 백업/스냅샷 관리 서버 |
| [mealie](mealie) | 9000 | O | 레시피/식단 관리 |
| [npmplus](npmplus) | 80, 443, 81 | | 리버스 프록시 + 인증서 자동 발급 |
| [outline](outline) | 3000 | O | 팀 협업용 마크다운 위키 |
| [paperless-ngx](paperless-ngx) | 8000 | O | 문서 OCR / 아카이빙 |
| [pingvin-share-x](pingvin-share-x) | 3000 | | 파일 공유 서비스 |
| [portainer](portainer) | 9443 | | 도커 관리 웹 UI |
| [postgresql](postgresql) | 5432 | O | 공용 PostgreSQL 서버 |
| [rallly](rallly) | 3000 | O | 일정 투표 미팅 스케줄러 |
| [scrutiny](scrutiny) | 8080 | | 디스크 S.M.A.R.T / NVMe 모니터링 |
| [shlink](shlink) | 8080, 8081 | O | URL 단축 서비스 (백엔드 + 웹 UI) |
| [silverbullet](silverbullet) | 3000 | O | 마크다운 기반 개인 노트 / PKM |
| [snapotter](snapotter) | 1349 | O | 파일 변환/압축/OCR 도구 모음 |
| [stirling-pdf](stirling-pdf) | 8080 | | 웹 기반 PDF 도구 모음 |
| [studywatch](studywatch) | 3000 | | 공부 시간 스톱워치 |
| [syncthing](syncthing) | 8384, 22000, 21027 | | 기기 간 P2P 파일 동기화 |
| [tugtainer](tugtainer) | 80 | O | 컨테이너 자동 업데이트 관리 |
| [uptime-kuma](uptime-kuma) | 3001 | | 서비스 상태 모니터링 |
| [vaultwarden](vaultwarden) | 80 | O | Bitwarden 호환 비밀번호 관리자 |

여러 스택을 한 호스트에서 같이 띄우면 포트가 겹칠 수 있다 (3000, 8080, 80 등).
그럴 때는 호스트 쪽 포트만 바꾸거나 리버스 프록시(caddy / npmplus)를 앞에 둔다.
