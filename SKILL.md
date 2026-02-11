---
name: api-endpoint-creator
description: |
  API endpoint creation guide that auto-detects project framework and conventions.
  Use when "API 만들어", "엔드포인트 추가", "route 생성", "API endpoint", "새 API", "CRUD API" 요청 시.
  Supports Next.js, Express, Fastify, NestJS, Hono, and more.
---

# API Endpoint Creator

프로젝트의 프레임워크와 기존 패턴을 자동 감지하여 일관된 API 엔드포인트를 생성합니다.

## Step 1: Project Detection

**반드시 엔드포인트 생성 전에 프로젝트를 분석합니다.**

### 1-1. 프레임워크 감지

`package.json`의 dependencies에서 프레임워크를 식별:

| 감지 기준 | 프레임워크 | 라우트 방식 |
|-----------|-----------|-------------|
| `next` + `app/` 디렉토리 존재 | Next.js App Router | 파일 기반 (`route.ts`) |
| `next` + `pages/api/` 존재 | Next.js Pages Router | 파일 기반 (`[name].ts`) |
| `express` | Express.js | 코드 기반 (`router.get()`) |
| `fastify` | Fastify | 코드 기반 (`fastify.get()`) |
| `@nestjs/core` | NestJS | 데코레이터 (`@Controller`) |
| `hono` | Hono | 코드 기반 (`app.get()`) |
| `@hono/node-server` | Hono (Node) | 코드 기반 |
| `koa` | Koa | 코드 기반 (`router.get()`) |

### 1-2. 기존 패턴 분석

기존 API 파일을 2-3개 읽어 다음을 파악:

- **미들웨어/래퍼**: 인증, rate limit 처리 방식
- **응답 헬퍼**: 성공/에러 응답 유틸리티
- **에러 처리**: 커스텀 에러 클래스, 에러 핸들러
- **검증 라이브러리**: zod, joi, class-validator, yup 등
- **DB/ORM**: prisma, drizzle, typeorm, mongoose, knex 등
- **디렉토리 구조**: 라우트/컨트롤러/서비스 파일 위치
- **네이밍 컨벤션**: camelCase, kebab-case, 파일명 패턴
- **TypeScript 여부**: `.ts`/`.js` 확장자

### 1-3. 감지 명령 (내부 수행)

```
1. Read package.json → 프레임워크, 검증 라이브러리, ORM 식별
2. Glob "**/*route*" or "**/api/**" or "**/controller*" → API 파일 위치 파악
3. Read 기존 API 파일 2-3개 → 패턴 파악
4. Read tsconfig.json (있으면) → path alias 확인
```

## Step 2: Requirements Gathering

사용자에게 확인:

- 어떤 리소스의 API인가? (users, posts, products 등)
- 필요한 HTTP 메서드는? (GET, POST, PUT, DELETE)
- 인증 필요 여부? (공개/인증/관리자)
- 동적 라우트 파라미터? (`[id]`, `:id`, `{id}`)

## Step 3: Generate Endpoint

**감지된 패턴에 완전히 맞춰서 생성합니다.**

핵심 원칙:
1. 기존 프로젝트의 import 경로를 그대로 사용
2. 기존 미들웨어/래퍼를 그대로 사용
3. 기존 응답 헬퍼를 그대로 사용
4. 기존 에러 처리 패턴을 그대로 사용
5. 기존 파일 네이밍/구조를 그대로 따름
6. 새로운 유틸리티나 패턴을 발명하지 않음

## Framework-Specific Reference

프레임워크별 CRUD 패턴: [references/crud-template.md](references/crud-template.md)
공통 에러 처리 패턴: [references/error-codes.md](references/error-codes.md)

## Checklist

- [ ] 프로젝트 프레임워크 감지 완료
- [ ] 기존 API 파일 패턴 분석 완료
- [ ] 기존 미들웨어/래퍼 사용
- [ ] 기존 검증 라이브러리로 입력 검증
- [ ] 기존 에러 처리 패턴 따름
- [ ] 기존 응답 형식 따름
- [ ] 비즈니스 로직은 서비스 레이어에 위임 (프로젝트에 서비스 레이어가 있는 경우)
- [ ] 기존 디렉토리 구조와 네이밍 컨벤션 준수
