# Asnaro · 다국어 뉴스·CMS

TypeScript · Next.js · PostgreSQL · Drizzle ORM · Vercel · GitHub Actions · Turborepo

## 시스템 구조

```mermaid
flowchart LR
    A["Admin<br>기사·설정 변경"] --> D["packages/data<br>Repository facade"]
    N["News<br>공개 기사 조회"] --> D
    F["Foundation<br>공개 사이트"] --> D
    D --> P["PostgreSQL"]
    A --> I["서명된 Internal API"]
    I --> N
    N --> C["Next.js cache tags<br>공개 경로 재검증"]
```

- News와 Foundation은 공개 화면, Admin은 비공개 운영 화면입니다.
- 애플리케이션은 모놀리식 모노레포이며, 서비스별 MSA로 분리된 구조가 아닙니다.

## 기사 변경 → 공개 캐시 재검증 흐름

```mermaid
flowchart LR
    A["Admin<br>발행·수정·취소"] --> B["서명된 재검증 API"]
    B --> C["News<br>DB 상태 재확인"]
    C --> D["기사·목록 캐시 태그 무효화"]
    C --> E["일본어·한국어 공개 경로 재검증"]
    D --> F["변경된 콘텐츠 제공"]
    E --> F
```

## 아키텍처와 데이터 경계

| 계층 | 역할 |
|---|---|
| Admin | 기사 상태 변경, 예약 발행, 감사 기록 |
| Data / PostgreSQL | 발행 상태·시각·언어 조건과 분석 집계 저장 |
| News | 공개 가능한 기사만 조회하고 공개 캐시 관리 |
| Internal API | 서명 검증 후 DB 상태를 재확인하고 캐시 무효화 |
