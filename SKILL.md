---
name: api-endpoint-creator
description: |
  Next.js 15 App Router API 엔드포인트 생성 가이드.
  Use when "API 만들어", "엔드포인트 추가", "route 생성", "API endpoint", "새 API", "CRUD API" 요청 시.
---

# API Endpoint Creator

Next.js 15 App Router 패턴에 맞는 API 엔드포인트를 생성합니다.

## Quick Start

```typescript
// src/app/api/[feature]/route.ts
import { withAuthApi } from '@/shared/@withwiz/middleware/wrappers';
import { NextApiResponse } from '@/lib/utils/api-response-nextjs';
import { IApiContext } from '@/shared/@withwiz/middleware/types';
import { z } from 'zod';

const createSchema = z.object({
  name: z.string().min(1).max(100),
});

export const POST = withAuthApi(async (context: IApiContext) => {
  const body = await context.request.json();
  const data = createSchema.parse(body);

  // 비즈니스 로직
  const result = await someService.create(data, context.user!.id);

  return NextApiResponse.created(result);
});
```

## Workflow

### 1. 요구사항 확인

질문:
- 어떤 리소스의 API인가? (links, users, favorites 등)
- 필요한 HTTP 메서드는? (GET, POST, PUT, DELETE)
- 인증 필요 여부? (공개/인증/관리자)
- 동적 라우트 필요? (`[id]`, `[shortCode]`)

### 2. 파일 위치 결정

```
src/app/api/
├── [feature]/
│   ├── route.ts              # 목록/생성 (GET, POST)
│   └── [id]/
│       └── route.ts          # 단일 리소스 (GET, PUT, DELETE)
```

### 3. 래퍼 선택

| 래퍼 | 용도 | Rate Limit |
|------|------|------------|
| `withPublicApi` | 인증 불필요 (로그인, 회원가입) | 120/min |
| `withAuthApi` | 로그인 필수 | 120/min |
| `withAdminApi` | 관리자 전용 | 200/min |

### 4. Zod 스키마 작성

```typescript
// src/lib/validators/[feature]Schema.ts
import { z } from 'zod';
import { sanitizeInput } from '@/shared/@withwiz/utils/sanitize';

export const createFeatureSchema = z.object({
  name: z.string()
    .min(1, 'Name is required')
    .max(100)
    .transform(sanitizeInput),
  description: z.string().max(500).optional(),
});

export const updateFeatureSchema = createFeatureSchema.partial();
```

### 5. 에러 처리

```typescript
import { AppError } from '@/shared/@withwiz/error/AppError';

// 리소스 없음
if (!resource) throw new AppError(40401); // NOT_FOUND

// 권한 없음
if (resource.userId !== user.id) throw new AppError(40301); // FORBIDDEN

// 비즈니스 규칙 위반
if (user.linkCount >= limit) throw new AppError(40901); // LIMIT_EXCEEDED
```

### 6. 응답 형식

```typescript
// 단일 리소스
return NextApiResponse.success(data);

// 생성 (201)
return NextApiResponse.created(newResource);

// 페이지네이션
return NextApiResponse.paginated(items, page, pageSize, total, 'resources');

// 삭제 (204)
return NextApiResponse.noContent();
```

## Templates

CRUD 엔드포인트 템플릿: [references/crud-template.md](references/crud-template.md)
에러 코드 목록: [references/error-codes.md](references/error-codes.md)

## Checklist

- [ ] 올바른 래퍼 사용 (withPublicApi/withAuthApi/withAdminApi)
- [ ] Zod 스키마로 입력 검증
- [ ] AppError로 비즈니스 에러 처리
- [ ] NextApiResponse 헬퍼로 응답
- [ ] 서비스 레이어에 비즈니스 로직 위임
- [ ] `src/shared/` → `src/lib/` 역방향 의존 금지
