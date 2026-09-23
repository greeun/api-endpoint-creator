# api-endpoint-creator

A Claude Code skill that automatically detects your project's framework and existing patterns to generate API endpoints.

## Supported Frameworks

Next.js (App Router / Pages Router), Express, Fastify, NestJS, Hono, Koa

## Trigger Keywords

- "API 만들어"
- "엔드포인트 추가"
- "route 생성"
- "API endpoint"
- "새 API"
- "CRUD API"

## Usage Examples

```bash
# Request in natural language within Claude Code
"favorites CRUD API 만들어줘"
"사용자 프로필 조회 API 추가해줘"
"댓글 엔드포인트 생성해줘"
```

## How It Works

1. **Project detection**: Analyzes `package.json`, existing API files, and `tsconfig.json` to identify the framework and patterns
2. **Requirements confirmation**: Confirms the resource name, HTTP methods, whether authentication is required, etc.
3. **Endpoint generation**: Generates code matching the detected project patterns (reusing existing middleware, response helpers, and error handling as-is)

## File Structure

```
api-endpoint-creator/
├── SKILL.md                    # Main guide (framework detection + workflow)
└── references/
    ├── crud-template.md        # CRUD templates for 6 frameworks
    └── error-codes.md          # General error handling patterns
```

## License

MIT
