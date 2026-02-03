# 에러 코드 Reference

## 에러 코드 체계

5자리 코드: `XXYYY`
- `XX`: HTTP 상태 코드 앞 2자리 (40=4xx, 50=5xx)
- `YYY`: 세부 에러 번호

## 자주 사용하는 에러 코드

### 인증 에러 (401xx)

| 코드 | 상수 | 설명 |
|------|------|------|
| 40101 | UNAUTHORIZED | 인증 필요 / 잘못된 자격증명 |
| 40102 | INVALID_TOKEN | 유효하지 않은 토큰 |
| 40103 | TOKEN_EXPIRED | 만료된 토큰 |

### 권한 에러 (403xx)

| 코드 | 상수 | 설명 |
|------|------|------|
| 40301 | FORBIDDEN | 접근 권한 없음 |
| 40302 | ADMIN_REQUIRED | 관리자 권한 필요 |

### 리소스 에러 (404xx)

| 코드 | 상수 | 설명 |
|------|------|------|
| 40401 | NOT_FOUND | 리소스를 찾을 수 없음 |
| 40402 | USER_NOT_FOUND | 사용자를 찾을 수 없음 |
| 40403 | LINK_NOT_FOUND | 링크를 찾을 수 없음 |

### 검증 에러 (400xx)

| 코드 | 상수 | 설명 |
|------|------|------|
| 40001 | BAD_REQUEST | 잘못된 요청 |
| 40002 | VALIDATION_ERROR | 입력 검증 실패 |
| 40003 | INVALID_INPUT | 유효하지 않은 입력 |

### 충돌 에러 (409xx)

| 코드 | 상수 | 설명 |
|------|------|------|
| 40901 | CONFLICT | 리소스 충돌 |
| 40902 | DUPLICATE_EMAIL | 중복된 이메일 |
| 40903 | DUPLICATE_ALIAS | 중복된 별칭 |

### 제한 에러 (429xx)

| 코드 | 상수 | 설명 |
|------|------|------|
| 42901 | RATE_LIMIT_EXCEEDED | Rate limit 초과 |
| 42902 | LIMIT_EXCEEDED | 생성 제한 초과 |

### 서버 에러 (500xx)

| 코드 | 상수 | 설명 |
|------|------|------|
| 50001 | INTERNAL_SERVER_ERROR | 서버 내부 오류 |
| 50002 | DATABASE_ERROR | 데이터베이스 오류 |

## 사용 예시

```typescript
import { AppError } from '@/shared/@withwiz/error/AppError';

// 리소스 없음
const link = await linkService.getById(id);
if (!link) {
  throw new AppError(40403); // LINK_NOT_FOUND
}

// 권한 없음
if (link.userId !== user.id) {
  throw new AppError(40301); // FORBIDDEN
}

// 중복 검사
const existing = await userService.getByEmail(email);
if (existing) {
  throw new AppError(40902); // DUPLICATE_EMAIL
}

// 제한 초과
if (user.linkCount >= MAX_LINKS) {
  throw new AppError(42902); // LIMIT_EXCEEDED
}
```

## 커스텀 에러 메시지

```typescript
// 기본 메시지 사용
throw new AppError(40401);

// 커스텀 메시지 추가
throw new AppError(40401, 'The requested link does not exist');
```
