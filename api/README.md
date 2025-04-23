# 코딩테스트 스터디 서비스 API 문서

이 디렉토리는 코딩테스트 스터디 서비스의 API 명세를 [OpenAPI 3.0](https://spec.openapis.org/oas/v3.0.3.html) 포맷으로 정의합니다.

## 디렉토리 구조

```
openapi/
├── openapi.yaml                # 메인 문서 (모든 정의를 가져오는 root)
├── info.yaml                   # API 기본 정보
├── resources/                  # 리소스별 폴더
│   ├── users/
│   │   ├── api.yaml            # 사용자 API 경로
│   │   └── schemas.yaml        # 사용자 관련 스키마
│   ├── studies/
│   │   ├── api.yaml            # 스터디 API 경로
│   │   └── schemas.yaml        # 스터디 관련 스키마
│   └── ... (다른 리소스들)
└── security/                 # 보안 관련 정의
│   └── auth-session.yaml     # 세션 기반 인증 정의
└── errors/                   # 오류 관련 정의
    ├── schemas.yaml          # 오류 스키마 (Error, ValidationError 등)
    └── responses.yaml        # 공통 오류 응답
```

1. **메인 문서**: `openapi.yaml`은 모든 API를 조합하는 진입점입니다. 서버 정보, 태그, 경로 참조를 포함합니다.
2. **리소스별 폴더**: 각 리소스(users, studies 등)는 독립적인 폴더에 위치하며, API와 스키마 정의가 각각 분리되어 있습니다.
    - `api.yaml`: 해당 리소스의 모든 API 엔드포인트 정의
    - `schemas.yaml`: 해당 리소스의 데이터 모델 정의
3. **공통 컴포넌트**: 여러 리소스에서 공유하는 요소들을 `common/` 폴더에 집중하여 중복을 최소화합니다.

## 사용 방법

Docker와 Docker Compose가 설치되어 있다면, 다음 명령어로 Swagger UI를 통해 API 문서를 확인할 수 있습니다:

```bash
# 프로젝트 루트 디렉토리에서
docker-compose up -d

# 브라우저에서 확인
# http://localhost:8080
```

## API 요청 및 응답 형식

### 정상 요청 및 응답

모든 정상 요청과 응답은 [JSON:API](https://jsonapi.org/) 표준을 따르며,   
Spring Boot 3에서 구현합니다.(구현 상세: [JSONAPI-springboot3/README](https://github.com/biddan606/JSONAPI-springboot3) 참조)

### 오류 응답

모든 오류 응답은 [Problem Details for HTTP APIs](https://datatracker.ietf.org/doc/html/rfc7807) 표준을 따르며,   
Spring Boot 3에서 구현합니다.(구현 상세: [ProblemJSON-Springboot3/README](https://github.com/biddan606/ProblemJSON-SpringBoot3) 참조)

## 컨벤션

1. **경로 설계**:
    - 리소스 중심 경로 사용 (e.g., `/studies/{studyId}/members`)
    - REST 메서드 의미에 맞게 사용 (GET: 조회, POST: 생성, PATCH: 부분 수정, DELETE: 삭제)

2. **명명 규칙**:
    - 파일명: 소문자, 하이픈 사용 (e.g., `api.yaml`, `schemas.yaml`)
    - 스키마: PascalCase (e.g., `UserResource`, `StudyResponse`)
        - Spring Boot와의 통일성을 위해 Java 클래스 명명 규칙과 일치시킴
          경로 변수: camelCase (e.g., userId, studyId)
    - 경로 변수: camelCase (e.g., `userId`, `studyId`)
      - 리소스명(kebab-case)과 시각적으로 구분하기 위해 camelCase 사용 
      - 경로 변수는 리소스 식별자로서 Java 객체의 필드명과 직접 매핑될 때가 많아 camelCase가 적합함 
      - 예: /studies/{studyId}/members/{memberId} - 여기서 studyId와 memberId는 경로 변수
    - 리소스명: kebab-case (e.g., `coding-tests`, `study-members`)
      - URI의 스키마(scheme)와 호스트(host)는 대소문자를 구별하지 않지만 경로(path), 쿼리(query), 프래그먼트(fragment)는 대소문자를 구별함 
          - 그러나 일관성 유지와 가독성을 위해 항상 kebab-case 형식만 사용
      - snake_case는 하이퍼링크와 같이 밑줄이 쳐져 있으면 공백으로 구별되어 사용하지 않음
    - 쿼리 파라미터: kebab-case (e.g., `page[filter-by]`, `sort-direction`)
        - RFC 3986 표준 준수: URI 경로 세그먼트는 대소문자를 구분하지만 실제 구현에서는 혼란 방지를 위해 소문자 통일이 권장됨
        - 가독성: 카멜케이스는 URL에서 가독성이 떨어지고, 스네이크케이스는 밑줄과 함께 있을 때 구별하기 어려움
        - 웹 브라우저 사용성: 많은 브라우저에서 단어를 옆으로 이동할 때 하이픈('-')을 띄어쓰기처럼 인식하여 단어 단위 탐색이 용이함
   - 필드 변수(속성): camelCase (e.g., `loginId`, `preferredRegion`, `lastLoginDate`)
      - Spring Boot 일관성: Java 클래스 필드명과 동일한 규칙을 적용하여 백엔드 코드와의 변환 작업 최소화
      - JSON 표준 관행: JavaScript와 JSON 생태계에서 camelCase가 사실상 표준
      - JSON:API 호환성: JSON:API 스펙은 일반적으로 리소스 속성에 camelCase 사용
      - 프론트엔드 통합 용이성: JavaScript 프론트엔드와 통합 시 필드명 변환 없이 바로 사용 가능

3. **태그 사용**:
    - 모든 엔드포인트에 적절한 태그 지정
    - 태그는 관련 리소스나 기능을 나타내는 명사 사용

4. **설명 문구**:
    - 모든 경로, 파라미터, 스키마에 명확한 설명 추가
    - 예시 값 포함 권장

## API 버전 관리 방식

### 기본 원칙

의미 기반 버전 관리(Semantic Versioning): X.Y.Z 형식 사용
- X: 메이저 버전 (하위 호환성을 깨는 변경)
- Y: 마이너 버전 (하위 호환성을 유지하는 기능 추가)
- Z: 패치 버전 (버그 수정)

**MVP 단계 접근법:** MVP 기간 동안에는 API가 변경되더라도 버전은 1.0.0으로 유지, 정식 출시 후에만 Semantic Versioning에 따라 버전 관리 시작

### API 기본 경로

`/api/v1` 형태를 사용합니다.

**/api를 사용하는 이유**
- **리소스 구분:**
  - API 엔드포인트를 웹사이트의 다른 리소스(정적 파일, 웹페이지 등)와 명확히 분리
  - 하나의 도메인에서 프론트엔드와 API를 함께 호스팅할 때 충돌 방지

**/v1을 사용하는 이유**
- **향후 호환성 보장:**
  - API가 크게 변경될 때 /api/v2와 같이 새 버전 도입 가능
  - 기존 클라이언트는 /api/v1을 계속 사용하여 호환성 유지
- **점진적 마이그레이션:**
  - 여러 API 버전을 동시에 운영할 수 있어 클라이언트가 자체 일정에 맞춰 새 버전으로 전환 가능

## 문제 해결

문서 관련 흔한 이슈와 해결 방법:

1. **$ref 경로 오류**:
    - 상대 경로가 정확한지 확인
    - 참조하는 컴포넌트 이름이 정확한지 확인
    - 경로에 `#/` 구분자가 올바르게 사용되었는지 확인

2. **Swagger UI 렌더링 문제**:
    - YAML 문법이 올바른지 확인
    - Docker Compose 로그 확인: `docker-compose logs swagger-ui`
    - 브라우저 개발자 도구에서 오류 메시지 확인

3. **문서 번들링 문제**:
    - 순환 참조가 없는지 확인
    - [swagger-cli](https://github.com/APIDevTools/swagger-cli) 또는 [redocly](https://redocly.com/docs/cli/) 도구로 문서 유효성 검증


## 문서 수정 가이드

### 새 리소스 추가

1. `resources/` 아래 새 폴더 생성 (예: `resources/new-resource/`)
2. 해당 폴더에 `api.yaml`과 `schemas.yaml` 파일 생성
3. `openapi.yaml`에 새 경로와 스키마 참조 추가:

```yaml
paths:
  /new-resource:
    $ref: './resources/new-resource/api.yaml#/new_resource'

components:
  schemas:
    NewResourceModel:
      $ref: './resources/new-resource/schemas.yaml#/NewResourceModel'
```

### 기존 리소스 수정

1. 해당 리소스 폴더 내 파일 수정 (`api.yaml` 또는 `schemas.yaml`)
2. 변경 사항이 다른 리소스에 영향을 주는지 확인
3. 문서 유효성 검증 수행 (아래 명령어 사용)

```bash
# 문서 유효성 검증 (npm 패키지 설치 필요)
npx @apidevtools/swagger-cli validate openapi/openapi.yaml
```

### 공통 컴포넌트 수정

1. `common/` 폴더 내 해당 파일 수정
2. 변경 사항이 사용되는 모든 리소스에 미치는 영향을 고려
3. 큰 변경의 경우 사전에 팀원들과 논의
