# api-endpoint-creator

Next.js 15 App Router API 엔드포인트 생성 가이드 스킬입니다.

## 트리거 키워드

- "API 만들어"
- "엔드포인트 추가"
- "route 생성"
- "API endpoint"
- "새 API"
- "CRUD API"

## 사용 예시

```bash
# Claude Code에서 자연어로 요청
"favorites CRUD API 만들어줘"
"사용자 프로필 조회 API 추가해줘"
"댓글 엔드포인트 생성해줘"
```

## 제공 기능

### 1. API 래퍼 가이드

| 래퍼 | 용도 | Rate Limit |
|------|------|------------|
| `withPublicApi` | 인증 불필요 (로그인, 회원가입) | 120/min |
| `withAuthApi` | 로그인 필수 | 120/min |
| `withAdminApi` | 관리자 전용 | 200/min |

### 2. Zod 검증 패턴

```typescript
import { z } from 'zod';

const createSchema = z.object({
  name: z.string().min(1).max(100),
  description: z.string().max(500).optional(),
});
```

### 3. 에러 처리

```typescript
import { AppError } from '@/shared/@withwiz/error/AppError';

if (!resource) throw new AppError(40401); // NOT_FOUND
if (resource.userId !== user.id) throw new AppError(40301); // FORBIDDEN
```

### 4. 응답 형식

```typescript
return NextApiResponse.success(data);       // 200
return NextApiResponse.created(newItem);    // 201
return NextApiResponse.paginated(...);      // 페이지네이션
return NextApiResponse.noContent();         // 204
```

## 파일 구조

```
api-endpoint-creator/
├── SKILL.md                    # 메인 가이드
├── README.md                   # 이 파일
└── references/
    ├── crud-template.md        # CRUD 엔드포인트 템플릿
    └── error-codes.md          # 에러 코드 레퍼런스
```

## 참조 문서

- [CRUD 템플릿](references/crud-template.md) - GET/POST/PUT/DELETE 전체 예제
- [에러 코드](references/error-codes.md) - 5자리 에러 코드 체계

## 체크리스트

API 엔드포인트 생성 시 확인 사항:

- [ ] 올바른 래퍼 사용 (`withPublicApi`/`withAuthApi`/`withAdminApi`)
- [ ] Zod 스키마로 입력 검증
- [ ] AppError로 비즈니스 에러 처리
- [ ] NextApiResponse 헬퍼로 응답
- [ ] 서비스 레이어에 비즈니스 로직 위임
- [ ] `src/shared/` → `src/lib/` 역방향 의존 금지

## 프로젝트 호환성

이 스킬은 다음 프로젝트 구조에 최적화되어 있습니다:

- **Next.js 15** App Router
- **Prisma** ORM
- **Zod** 검증
- **JWT** 인증
- **Clean Architecture** (shared/@withwiz 패턴)

## 라이선스

MIT
