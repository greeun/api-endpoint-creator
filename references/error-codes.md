# Common API Error Handling Patterns

프레임워크에 관계없이 적용되는 API 에러 처리 패턴입니다.
프로젝트에 커스텀 에러 클래스가 있으면 반드시 그것을 사용합니다.

## Standard HTTP Status Codes

| Status | 의미 | 사용 시점 |
|--------|------|-----------|
| 200 | OK | 조회/수정 성공 |
| 201 | Created | 리소스 생성 성공 |
| 204 | No Content | 삭제 성공 |
| 400 | Bad Request | 입력 검증 실패 |
| 401 | Unauthorized | 인증 필요/실패 |
| 403 | Forbidden | 권한 없음 |
| 404 | Not Found | 리소스 없음 |
| 409 | Conflict | 중복/충돌 |
| 422 | Unprocessable Entity | 비즈니스 규칙 위반 |
| 429 | Too Many Requests | Rate limit 초과 |
| 500 | Internal Server Error | 서버 오류 |

## Error Response Patterns

### Pattern A: Simple Object

```json
{ "error": "Resource not found" }
```

### Pattern B: Structured Object

```json
{
  "error": {
    "code": "NOT_FOUND",
    "message": "Resource not found"
  }
}
```

### Pattern C: With Details (Validation)

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid input",
    "details": [
      { "field": "email", "message": "Invalid email format" }
    ]
  }
}
```

## Common Error Scenarios

```
리소스 없음     → 404
권한 없음       → 403
입력 검증 실패  → 400 or 422
중복 데이터     → 409
인증 실패       → 401
제한 초과       → 429
```

## Detection Priority

프로젝트에서 다음 순서로 에러 처리 방식을 탐색:

1. **커스텀 에러 클래스** (AppError, HttpException, ApiError 등)
2. **에러 핸들러 미들웨어** (errorHandler, exception filter 등)
3. **기존 API에서 사용하는 패턴** (throw vs return)
4. **위 패턴이 없으면** 프레임워크 기본 방식 사용
