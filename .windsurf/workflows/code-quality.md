---
description: Apply linting (ESLint), formatting (Prettier) and strict typing (TypeScript) to respect code conventions and reduce errors.
---

# Code Quality Workflow

## Steps

### 1. Install dependencies
```bash
npm install --save-dev \
  eslint eslint-config-airbnb eslint-config-airbnb-typescript \
  @typescript-eslint/parser @typescript-eslint/eslint-plugin \
  prettier eslint-plugin-prettier eslint-config-prettier \
  eslint-plugin-jsx-a11y eslint-plugin-import eslint-plugin-react \
  eslint-plugin-react-hooks
```
> :contentReference[oaicite:0]{index=0}

### 2. Create ESLint configuration file
```javascript
// .eslintrc.cjs
module.exports = {
  parser: '@typescript-eslint/parser',
  extends: [
    'airbnb-typescript', 
    'plugin:react/recommended',
    'plugin:react-hooks/recommended',
    'plugin:jsx-a11y/recommended',
    'prettier'
  ],
  parserOptions: {
    project: './tsconfig.json',
    ecmaFeatures: { jsx: true }
  },
  rules: {
    'react/jsx-filename-extension': ['error', { extensions: ['.tsx'] }],
    'import/extensions': ['error', 'ignorePackages', {
      ts: 'never',
      tsx: 'never'
    }]
  },
  settings: { react: { version: 'detect' } }
};
```

**Note:** We use `eslint-config-prettier` to disable rules that conflict with Prettier.
> :contentReference[oaicite:1]{index=1}

### 3. Create Prettier configuration
```javascript
// .prettierrc.js
module.exports = {
  singleQuote: true,
  trailingComma: 'all',
  printWidth: 80,
  arrowParens: 'avoid'
};
```

```
// .prettierignore
node_modules/
build/
dist/
```
> :contentReference[oaicite:2]{index=2}

### 4. Enable TypeScript strict mode
```json
// tsconfig.json
{
  "compilerOptions": {
    "strict": true,
    "noImplicitAny": true,
    "strictNullChecks": true,
    "strictFunctionTypes": true,
    "strictBindCallApply": true,
    "strictPropertyInitialization": true,
    "noImplicitThis": true,
    /* other standard options... */
  }
}
```

**Note:** The `strict: true` flag enables all strict checks for better code safety.
> :contentReference[oaicite:3]{index=3}

### 5. Add npm scripts
```json
// package.json
"scripts": {
  "lint": "eslint 'src/**/*.{ts,tsx}'",
  "format": "prettier --write 'src/**/*.{ts,tsx,js,jsx,json,css,md}'",
  "check": "npm run lint && npm run format -- --check"
}
```

**Note:** The `check` script runs lint and format in CI to reject any push containing style or typing errors.
> :contentReference[oaicite:4]{index=4}

### 6. Check and fix violations
```bash
npm run lint -- --fix
npm run format
```

**Note:** These commands automatically apply the corrections suggested by ESLint and Prettier.
> :contentReference[oaicite:5]{index=5}

### 7. Naming convention verification
- React components: name each file and folder in PascalCase (e.g., `UserCard.tsx`) :contentReference[oaicite:6]{index=6}
- Utility files/hooks: name in camelCase (e.g., `dateFormatter.ts`) :contentReference[oaicite:7]{index=7}
- Ensure that default component exports use the same name as the file.

> :contentReference[oaicite:8]{index=8}

### 8. Configure CI (e.g., GitHub Actions)
```yaml
# .github/workflows/lint.yml
name: Lint & Format
on: [push, pull_request]
jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with: { 'node-version': '18' }
      - run: npm ci
      - run: npm run check
```

**Note:** The CI job fails if ESLint or Prettier detects a problem.
> :contentReference[oaicite:9]{index=9}