# CRUD API Template

## 목록/생성 엔드포인트

```typescript
// src/app/api/[feature]/route.ts
import { withAuthApi } from '@/shared/@withwiz/middleware/wrappers';
import { NextApiResponse } from '@/lib/utils/api-response-nextjs';
import { IApiContext } from '@/shared/@withwiz/middleware/types';
import { featureService } from '@/lib/services/featureService';
import { createFeatureSchema } from '@/lib/validators/featureSchema';
import { parsePaginationParams } from '@/lib/utils/pagination';

// GET /api/[feature] - 목록 조회
export const GET = withAuthApi(async (context: IApiContext) => {
  const user = context.user!;
  const { page, pageSize } = parsePaginationParams(context.request);

  const result = await featureService.getList({
    userId: user.id,
    page,
    pageSize,
  });

  return NextApiResponse.paginated(
    result.items,
    page,
    pageSize,
    result.total,
    'features'
  );
});

// POST /api/[feature] - 생성
export const POST = withAuthApi(async (context: IApiContext) => {
  const user = context.user!;
  const body = await context.request.json();
  const data = createFeatureSchema.parse(body);

  const newFeature = await featureService.create({
    ...data,
    userId: user.id,
  });

  return NextApiResponse.created(newFeature);
});
```

## 단일 리소스 엔드포인트

```typescript
// src/app/api/[feature]/[id]/route.ts
import { withAuthApi } from '@/shared/@withwiz/middleware/wrappers';
import { NextApiResponse } from '@/lib/utils/api-response-nextjs';
import { IApiContext } from '@/shared/@withwiz/middleware/types';
import { AppError } from '@/shared/@withwiz/error/AppError';
import { featureService } from '@/lib/services/featureService';
import { updateFeatureSchema } from '@/lib/validators/featureSchema';

// GET /api/[feature]/[id] - 단일 조회
export const GET = withAuthApi(async (context: IApiContext) => {
  const user = context.user!;
  const { id } = context.params;

  const feature = await featureService.getById(id);

  if (!feature) {
    throw new AppError(40401); // NOT_FOUND
  }

  if (feature.userId !== user.id) {
    throw new AppError(40301); // FORBIDDEN
  }

  return NextApiResponse.success(feature);
});

// PUT /api/[feature]/[id] - 수정
export const PUT = withAuthApi(async (context: IApiContext) => {
  const user = context.user!;
  const { id } = context.params;
  const body = await context.request.json();
  const data = updateFeatureSchema.parse(body);

  const feature = await featureService.getById(id);

  if (!feature) {
    throw new AppError(40401);
  }

  if (feature.userId !== user.id) {
    throw new AppError(40301);
  }

  const updated = await featureService.update(id, data);

  return NextApiResponse.success(updated);
});

// DELETE /api/[feature]/[id] - 삭제
export const DELETE = withAuthApi(async (context: IApiContext) => {
  const user = context.user!;
  const { id } = context.params;

  const feature = await featureService.getById(id);

  if (!feature) {
    throw new AppError(40401);
  }

  if (feature.userId !== user.id) {
    throw new AppError(40301);
  }

  await featureService.delete(id);

  return NextApiResponse.noContent();
});
```

## 관리자 전용 엔드포인트

```typescript
// src/app/api/admin/[feature]/route.ts
import { withAdminApi } from '@/shared/@withwiz/middleware/wrappers';
import { NextApiResponse } from '@/lib/utils/api-response-nextjs';
import { IApiContext } from '@/shared/@withwiz/middleware/types';

export const GET = withAdminApi(async (context: IApiContext) => {
  // context.user.role === 'ADMIN' 자동 검증됨
  const result = await featureService.getAllForAdmin();

  return NextApiResponse.success(result);
});
```

## 공개 API 엔드포인트

```typescript
// src/app/api/public/[feature]/route.ts
import { withPublicApi } from '@/shared/@withwiz/middleware/wrappers';
import { NextApiResponse } from '@/lib/utils/api-response-nextjs';
import { IApiContext } from '@/shared/@withwiz/middleware/types';

export const GET = withPublicApi(async (context: IApiContext) => {
  // 인증 없이 접근 가능
  const result = await featureService.getPublicData();

  return NextApiResponse.success(result);
});
```

## 동적 라우트 파라미터 접근

```typescript
// src/app/api/links/[shortCode]/route.ts
export const GET = withPublicApi(async (context: IApiContext) => {
  const { shortCode } = context.params;
  // shortCode 사용
});

// src/app/api/users/[userId]/links/[linkId]/route.ts
export const GET = withAuthApi(async (context: IApiContext) => {
  const { userId, linkId } = context.params;
  // 중첩 파라미터 접근
});
```
