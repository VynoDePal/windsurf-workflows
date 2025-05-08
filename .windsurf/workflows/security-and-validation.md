---
description: Audit dependencies (npm audit), validate API entries (Zod) and configure secure HTTP headers.
---

# Security and Validation Workflow

## Steps

### 1. Audit dependencies and automatic correction
```bash
# Run dependency audit
npm audit --production --json > audit-report.json
```
> The JSON report will be analyzed by Cascade to list all vulnerabilities.

> :contentReference[oaicite:0]{index=0} :contentReference[oaicite:1]{index=1}

### 2. Apply critical fixes
```bash
# Automatically update vulnerable dependencies
npm audit fix --force
```
> Use `--force` only if you have tested locally,  
> or prefer `npm audit fix` without `--force` for a more cautious approach.

> :contentReference[oaicite:2]{index=2} :contentReference[oaicite:3]{index=3}

### 3. Record the final state of dependencies
```bash
# Lock versions in production
npm ci
npm shrinkwrap
```
> Generates an `npm-shrinkwrap.json` file for dependency immutability in production.

> :contentReference[oaicite:4]{index=4}

### 4. Install Zod for schema validation
```bash
npm install zod
```
> :contentReference[oaicite:5]{index=5}

### 5. Add Zod validation in API Routes
For each file `pages/api/*.ts` or `app/api/*/route.ts`, proceed as follows:

1. Import Zod:
```typescript
import { z } from 'zod';
```

2. Define a schema:
```typescript
const UserSchema = z.object({
  name: z.string().min(1),
  email: z.string().email(),
});
```

3. Parse and validate:
```typescript
export default function handler(req: NextApiRequest, res: NextApiResponse) {
  try {
    const data = UserSchema.parse(req.body);
    // rest of the logic...
  } catch (err) {
    return res.status(400).json({ error: err.errors });
  }
}
```

> Make sure to validate `req.query` in the same way for GET routes.

> :contentReference[oaicite:6]{index=6} :contentReference[oaicite:7]{index=7}

### 6. Configure security headers in next.config.js
```javascript
// next.config.js
module.exports = {
  async headers() {
    return [
      {
        source: '/(.*)',
        headers: [
          {
            key: 'Content-Security-Policy',
            value: "default-src 'self'; script-src 'self'; style-src 'self' 'unsafe-inline'; img-src 'self' data:; frame-ancestors 'none';"
          },
          { key: 'Strict-Transport-Security', value: 'max-age=63072000; includeSubDomains; preload' },
          { key: 'X-Frame-Options', value: 'DENY' },
          { key: 'X-Content-Type-Options', value: 'nosniff' },
          { key: 'Referrer-Policy', value: 'strict-origin-when-cross-origin' },
        ],
      },
    ];
  }
};
```
> Adapt the CSP directive according to your needs (CDN, fonts, etc.).

> :contentReference[oaicite:8]{index=8} :contentReference[oaicite:9]{index=9} :contentReference[oaicite:10]{index=10}

### 7. Verify the implementation of headers
```bash
# Start the development server
npm run dev
```

Using curl or Postman, send a GET request:  
`curl -I http://localhost:3000`  
Verify that CSP, HSTS, X-Frame-Options headers, etc. are present in the response.

> :contentReference[oaicite:11]{index=11}

### 8. Automate audit in CI
```yaml
# .github/workflows/security.yml
name: 'Security Audit'
on: [push, pull_request]
jobs:
  audit:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with: { 'node-version': '18' }
      - run: npm ci
      - run: npm audit --production --audit-level=moderate
      - run: npm run lint  # includes schema validation
```
> The CI job fails if `moderate` or higher vulnerabilities are detected.

> :contentReference[oaicite:12]{index=12} :contentReference[oaicite:13]{index=13}