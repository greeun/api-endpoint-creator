# CRUD API Templates by Framework

프레임워크별 기본 CRUD 패턴입니다. 실제 생성 시 프로젝트의 기존 패턴(미들웨어, 응답 헬퍼, 에러 처리)으로 대체합니다.

## Next.js App Router

```typescript
// app/api/[resource]/route.ts
import { NextRequest, NextResponse } from 'next/server';

// GET /api/[resource] - 목록 조회
export async function GET(request: NextRequest) {
  const items = await db.resource.findMany();
  return NextResponse.json(items);
}

// POST /api/[resource] - 생성
export async function POST(request: NextRequest) {
  const body = await request.json();
  const item = await db.resource.create({ data: body });
  return NextResponse.json(item, { status: 201 });
}
```

```typescript
// app/api/[resource]/[id]/route.ts
import { NextRequest, NextResponse } from 'next/server';

type Params = { params: Promise<{ id: string }> };

// GET /api/[resource]/[id] - 단일 조회
export async function GET(request: NextRequest, { params }: Params) {
  const { id } = await params;
  const item = await db.resource.findUnique({ where: { id } });
  if (!item) return NextResponse.json({ error: 'Not found' }, { status: 404 });
  return NextResponse.json(item);
}

// PUT /api/[resource]/[id] - 수정
export async function PUT(request: NextRequest, { params }: Params) {
  const { id } = await params;
  const body = await request.json();
  const item = await db.resource.update({ where: { id }, data: body });
  return NextResponse.json(item);
}

// DELETE /api/[resource]/[id] - 삭제
export async function DELETE(request: NextRequest, { params }: Params) {
  const { id } = await params;
  await db.resource.delete({ where: { id } });
  return new NextResponse(null, { status: 204 });
}
```

## Next.js Pages Router

```typescript
// pages/api/[resource]/index.ts
import type { NextApiRequest, NextApiResponse } from 'next';

export default async function handler(req: NextApiRequest, res: NextApiResponse) {
  switch (req.method) {
    case 'GET': {
      const items = await db.resource.findMany();
      return res.status(200).json(items);
    }
    case 'POST': {
      const item = await db.resource.create({ data: req.body });
      return res.status(201).json(item);
    }
    default:
      res.setHeader('Allow', ['GET', 'POST']);
      return res.status(405).end(`Method ${req.method} Not Allowed`);
  }
}
```

```typescript
// pages/api/[resource]/[id].ts
import type { NextApiRequest, NextApiResponse } from 'next';

export default async function handler(req: NextApiRequest, res: NextApiResponse) {
  const { id } = req.query;

  switch (req.method) {
    case 'GET': {
      const item = await db.resource.findUnique({ where: { id: String(id) } });
      if (!item) return res.status(404).json({ error: 'Not found' });
      return res.status(200).json(item);
    }
    case 'PUT': {
      const item = await db.resource.update({ where: { id: String(id) }, data: req.body });
      return res.status(200).json(item);
    }
    case 'DELETE': {
      await db.resource.delete({ where: { id: String(id) } });
      return res.status(204).end();
    }
    default:
      res.setHeader('Allow', ['GET', 'PUT', 'DELETE']);
      return res.status(405).end(`Method ${req.method} Not Allowed`);
  }
}
```

## Express.js

```typescript
// routes/resource.routes.ts
import { Router, Request, Response } from 'express';

const router = Router();

// GET /api/resources
router.get('/', async (req: Request, res: Response) => {
  const items = await db.resource.findMany();
  res.json(items);
});

// POST /api/resources
router.post('/', async (req: Request, res: Response) => {
  const item = await db.resource.create({ data: req.body });
  res.status(201).json(item);
});

// GET /api/resources/:id
router.get('/:id', async (req: Request, res: Response) => {
  const item = await db.resource.findUnique({ where: { id: req.params.id } });
  if (!item) return res.status(404).json({ error: 'Not found' });
  res.json(item);
});

// PUT /api/resources/:id
router.put('/:id', async (req: Request, res: Response) => {
  const item = await db.resource.update({
    where: { id: req.params.id },
    data: req.body,
  });
  res.json(item);
});

// DELETE /api/resources/:id
router.delete('/:id', async (req: Request, res: Response) => {
  await db.resource.delete({ where: { id: req.params.id } });
  res.status(204).end();
});

export default router;
```

## Fastify

```typescript
// routes/resource.routes.ts
import { FastifyInstance } from 'fastify';

export default async function resourceRoutes(fastify: FastifyInstance) {
  // GET /api/resources
  fastify.get('/', async (request, reply) => {
    const items = await db.resource.findMany();
    return items;
  });

  // POST /api/resources
  fastify.post('/', async (request, reply) => {
    const item = await db.resource.create({ data: request.body });
    reply.status(201);
    return item;
  });

  // GET /api/resources/:id
  fastify.get('/:id', async (request, reply) => {
    const { id } = request.params as { id: string };
    const item = await db.resource.findUnique({ where: { id } });
    if (!item) {
      reply.status(404);
      return { error: 'Not found' };
    }
    return item;
  });

  // PUT /api/resources/:id
  fastify.put('/:id', async (request, reply) => {
    const { id } = request.params as { id: string };
    const item = await db.resource.update({ where: { id }, data: request.body });
    return item;
  });

  // DELETE /api/resources/:id
  fastify.delete('/:id', async (request, reply) => {
    const { id } = request.params as { id: string };
    await db.resource.delete({ where: { id } });
    reply.status(204).send();
  });
}
```

## NestJS

```typescript
// resource/resource.controller.ts
import { Controller, Get, Post, Put, Delete, Param, Body } from '@nestjs/common';
import { ResourceService } from './resource.service';
import { CreateResourceDto, UpdateResourceDto } from './resource.dto';

@Controller('resources')
export class ResourceController {
  constructor(private readonly service: ResourceService) {}

  @Get()
  findAll() {
    return this.service.findAll();
  }

  @Post()
  create(@Body() dto: CreateResourceDto) {
    return this.service.create(dto);
  }

  @Get(':id')
  findOne(@Param('id') id: string) {
    return this.service.findOne(id);
  }

  @Put(':id')
  update(@Param('id') id: string, @Body() dto: UpdateResourceDto) {
    return this.service.update(id, dto);
  }

  @Delete(':id')
  remove(@Param('id') id: string) {
    return this.service.remove(id);
  }
}
```

## Hono

```typescript
// routes/resource.ts
import { Hono } from 'hono';

const app = new Hono();

// GET /api/resources
app.get('/', async (c) => {
  const items = await db.resource.findMany();
  return c.json(items);
});

// POST /api/resources
app.post('/', async (c) => {
  const body = await c.req.json();
  const item = await db.resource.create({ data: body });
  return c.json(item, 201);
});

// GET /api/resources/:id
app.get('/:id', async (c) => {
  const id = c.req.param('id');
  const item = await db.resource.findUnique({ where: { id } });
  if (!item) return c.json({ error: 'Not found' }, 404);
  return c.json(item);
});

// PUT /api/resources/:id
app.put('/:id', async (c) => {
  const id = c.req.param('id');
  const body = await c.req.json();
  const item = await db.resource.update({ where: { id }, data: body });
  return c.json(item);
});

// DELETE /api/resources/:id
app.delete('/:id', async (c) => {
  const id = c.req.param('id');
  await db.resource.delete({ where: { id } });
  return c.body(null, 204);
});

export default app;
```

## Adaptation Rules

위 템플릿은 **기본 뼈대**일 뿐입니다. 실제 생성 시:

1. **DB 코드 교체**: 프로젝트의 실제 ORM/DB 클라이언트 사용
2. **미들웨어 적용**: 프로젝트에 인증 미들웨어가 있으면 그것을 사용
3. **응답 형식 맞춤**: 프로젝트에 응답 헬퍼가 있으면 그것을 사용
4. **에러 처리 맞춤**: 프로젝트의 에러 클래스/핸들러 사용
5. **검증 맞춤**: 프로젝트의 검증 라이브러리(zod/joi/class-validator) 사용
6. **파일 위치**: 프로젝트의 기존 디렉토리 구조에 맞춤
