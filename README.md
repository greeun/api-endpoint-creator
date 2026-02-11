# api-endpoint-creator

프로젝트의 프레임워크와 기존 패턴을 자동 감지하여 API 엔드포인트를 생성하는 Claude Code 스킬입니다.

## 지원 프레임워크

Next.js (App Router / Pages Router), Express, Fastify, NestJS, Hono, Koa

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

## 동작 방식

1. **프로젝트 감지**: `package.json`, 기존 API 파일, `tsconfig.json`을 분석하여 프레임워크와 패턴 식별
2. **요구사항 확인**: 리소스명, HTTP 메서드, 인증 여부 등 확인
3. **엔드포인트 생성**: 감지된 프로젝트 패턴에 맞춰 코드 생성 (기존 미들웨어, 응답 헬퍼, 에러 처리를 그대로 사용)

## 파일 구조

```
api-endpoint-creator/
├── SKILL.md                    # 메인 가이드 (프레임워크 감지 + 워크플로우)
└── references/
    ├── crud-template.md        # 6개 프레임워크별 CRUD 템플릿
    └── error-codes.md          # 범용 에러 처리 패턴
```

## 라이선스

MIT
