---
description: Request Schema Validation with Zod
---

# API Routes Validation Workflow

## Steps

### 1. Install Zod and utility dependencies
```bash
npm install zod zod-error
```
> Zod for schema validation, zod-error to generate readable error messages.

> :contentReference[oaicite:4]{index=4}

### 2. Define a Zod customErrorMap
```typescript
// src/utils/customErrorMap.ts
import { ZodErrorMap, ZodIssue } from 'zod';

export const customErrorMap: ZodErrorMap = (issue: ZodIssue, ctx) => {
  switch (issue.code) {
    case 'invalid_type':
      return { message: `Malformed field: expected ${issue.expected}` };
    case 'too_small':
      return { message: `Value too small (minimum ${issue.minimum})` };
    default:
      return { message: ctx.defaultError };
  }
};
```
> Allows replacing technical messages with clear business labels.

> :contentReference[oaicite:5]{index=5}

### 3. Create a generic validation middleware
```typescript
// src/middleware/validate.ts
import { NextApiRequest, NextApiResponse } from 'next';
import { AnyZodObject } from 'zod';

export function validateSchema(schema: AnyZodObject) {
  return async (req: NextApiRequest, res: NextApiResponse, next: () => void) => {
    try {
      await schema.parseAsync(req.method === 'GET' ? req.query : req.body);
      return next();
    } catch (err) {
      if (err instanceof ZodError) {
        return res.status(400).json({
          status: 'error',
          errors: err.format(),
        });
      }
      throw err;
    }
  };
}
```
> Intercepts the request before business logic and returns a 400 on failed validation.

> :contentReference[oaicite:6]{index=6}

### 4. Add an authentication middleware
```typescript
// src/middleware/auth.ts
import { NextResponse } from 'next/server';
import type { NextRequest } from 'next/server';

export function authMiddleware(req: NextRequest) {
  const token = req.cookies.get('authToken');
  if (!token || !isValidToken(token)) {
    return new NextResponse(JSON.stringify({
      status: 'error',
      message: 'Unauthorized',
    }), { status: 401 });
  }
  return NextResponse.next();
}
```
> Use `middleware.ts` for all `/api/*` routes, secure with JWT or session.

> :contentReference[oaicite:7]{index=7}

### 5. Apply middlewares in `pages/api/…`
```typescript
// pages/api/user.ts
import { NextApiRequest, NextApiResponse } from 'next';
import { z } from 'zod';
import { validateSchema } from '../../middleware/validate';
import { authMiddleware } from '../../middleware/auth';

const userSchema = z.object({
  name: z.string().min(2),
  email: z.string().email(),
}).refine(data => data.email.endsWith('@example.com'), {
  message: 'Email must be @example.com',
});

export default function handler(req: NextApiRequest, res: NextApiResponse) {
  return authMiddleware(req as any, res as any, async () => {
    await validateSchema(userSchema)(req, res, async () => {
      try {
        // Business logic here
        const user = await createUser(req.body);
        res.status(201).json({ status: 'success', data: user });
      } catch (error) {
        console.error(error);
        res.status(500).json({
          status: 'error',
          message: 'Internal server error',
        });
      }
    });
  });
}
```
> Chaining: auth → validation → business logic → try/catch for server errors.

> :contentReference[oaicite:8]{index=8}

### 6. Standardize the response structure
```bash
# Create a response.ts helper
cat << 'EOF' > src/utils/response.ts
export const success = (data: any) => ({ status: 'success', data });
export const error = (message: string, code: number = 500) => ({ status: 'error', message, code });
EOF
```
> Use these helpers to standardize all endpoints.

> :contentReference[oaicite:9]{index=9}

### 7. Test API routes
```bash
npm install --save-dev jest supertest
# Test example
cat << 'EOF' > __tests__/user.test.ts
import request from 'supertest';
import { createServer } from 'http';
import handler from '../pages/api/user';
describe('POST /api/user', () => {
  it('rejects invalid payload', async () => {
    const res = await request(createServer(handler)).post('/api/user').send({ name: 'A' });
    expect(res.status).toBe(400);
    expect(res.body.status).toBe('error');
  });
});
EOF
```
> Verify validations, HTTP codes, and error messages.

> :contentReference[oaicite:10]{index=10}