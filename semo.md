# SEMO · B2B 조달 플랫폼

NestJS · TypeScript · CQRS · Supabase/PostgreSQL · pg-boss · React · OpenAI API

## 견적 대상 공급사 식별 흐름

```mermaid
flowchart LR
    A["장바구니<br>외부 supplier ID"] --> B["NestJS / CQRS Handler"]
    B --> C["내부 users.id UUID 변환"]
    C --> D["quote_requests.target_supplier_ids 저장"]
    D --> E["목록 조회 권한 필터"]
    D --> F["pg-boss 자동견적 작업"]
    F --> G["대상 공급사만 quote_response 생성"]
```

## 핵심 ERD

```mermaid
erDiagram
    USERS ||--o{ QUOTE_REQUESTS : targets
    USERS ||--o{ QUOTE_RESPONSES : submits
    QUOTE_REQUESTS ||--o{ QUOTE_RESPONSES : receives
    USERS {
        uuid id PK
        string external_supplier_id
    }
    QUOTE_REQUESTS {
        uuid id PK
        uuid buyer_id FK
        text_array target_supplier_ids
        boolean is_public
    }
    QUOTE_RESPONSES {
        uuid id PK
        uuid quote_request_id FK
        uuid supplier_id FK
    }
```

## 관련 API

| Method | Endpoint | 역할 |
|---|---|---|
| POST | `/quote-requests` | 견적 생성과 대상 공급사 UUID 저장 |
| GET | `/quote-requests/public` | 공개 또는 대상 공급사 견적 조회 |
| POST | `/quote-requests/check-auto-quote` | 자동견적 가능 여부 확인 |
| GET | `/quote-requests/:id` | 견적 상세와 접근 권한 확인 |
