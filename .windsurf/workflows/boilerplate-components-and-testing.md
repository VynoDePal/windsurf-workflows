---
description: Automate the generation of reusable React components, custom hooks and unit tests to boost productivity.
---

# Component and Testing Boilerplate Workflow

## Steps

### 1. Install Plop.js as a development dependency
```bash
npm install --save-dev plop
```
> :contentReference[oaicite:0]{index=0}

### 2. Create the plopfile.js configuration file
```javascript
// plopfile.js
module.exports = function (plop) {
  plop.setGenerator('component', {
    description: 'Generates a React component with CSS and test',
    prompts: [
      { type: 'input', name: 'name', message: 'Component name?' },
      { type: 'input', name: 'path', message: 'Relative path (src/components)?' }
    ],
    actions: [
      {
        type: 'add',
        path: 'src/{{path}}/{{pascalCase name}}/{{pascalCase name}}.tsx',
        templateFile: 'plop-templates/component/Component.tsx.hbs'
      },
      {
        type: 'add',
        path: 'src/{{path}}/{{pascalCase name}}/{{pascalCase name}}.module.css',
        templateFile: 'plop-templates/component/Component.module.css.hbs'
      },
      {
        type: 'add',
        path: 'src/{{path}}/{{pascalCase name}}/index.ts',
        templateFile: 'plop-templates/component/index.ts.hbs'
      },
      {
        type: 'add',
        path: 'src/{{path}}/{{pascalCase name}}/{{pascalCase name}}.test.tsx',
        templateFile: 'plop-templates/component/Component.test.tsx.hbs'
      }
    ]
  });

  plop.setGenerator('hook', {
    description: 'Generates a React hook + test',
    prompts: [
      { type: 'input', name: 'name', message: 'Hook name (without "use")?' },
      { type: 'input', name: 'path', message: 'Relative path (src/hooks)?' }
    ],
    actions: [
      {
        type: 'add',
        path: 'src/{{path}}/use{{pascalCase name}}.ts',
        templateFile: 'plop-templates/hook/hook.ts.hbs'
      },
      {
        type: 'add',
        path: 'src/{{path}}/use{{pascalCase name}}.test.ts',
        templateFile: 'plop-templates/hook/hook.test.ts.hbs'
      }
    ]
  });
};
```

**Note:** Templates must be placed under `plop-templates/` as indicated.
> :contentReference[oaicite:1]{index=1}

### 3. Prepare component and hook templates
Create the following files in `plop-templates/`:
- `component/Component.tsx.hbs`
- `component/Component.module.css.hbs`
- `component/index.ts.hbs`
- `component/Component.test.tsx.hbs`
- `hook/hook.ts.hbs`
- `hook/hook.test.ts.hbs`

> :contentReference[oaicite:2]{index=2}

### 4. Run component generation
```bash
npx plop component
```

**Interactive inputs:**
- Enter `MyButton` for the name
- Enter `ui/buttons` for the path

**Result:**
- `src/ui/buttons/MyButton/MyButton.tsx`
- `src/ui/buttons/MyButton/MyButton.module.css`
- `src/ui/buttons/MyButton/index.ts`
- `src/ui/buttons/MyButton/MyButton.test.tsx`

> :contentReference[oaicite:3]{index=3}

### 5. Run hook generation
```bash
npx plop hook
```

**Interactive inputs:**
- Enter `auth` for the name → `useAuth.ts`
- Enter `hooks` for the path

**Result:**
- `src/hooks/useAuth.ts`
- `src/hooks/useAuth.test.ts`

> :contentReference[oaicite:4]{index=4}

### 6. Configure Jest and React Testing Library
```bash
npm install --save-dev jest @testing-library/react @testing-library/jest-dom ts-jest
```

**Note:**
Add to `jest.config.js`:
```js
module.exports = {
  preset: 'ts-jest',
  testEnvironment: 'jsdom',
  setupFilesAfterEnv: ['<rootDir>/jest.setup.ts']
};
```

And in `jest.setup.ts`:
```ts
import '@testing-library/jest-dom';
```

> :contentReference[oaicite:5]{index=5}

### 7. Run all generated tests
```bash
npm test -- --passWithNoTests
```

**Note:** Verify that each initial test passes and inspect the created snapshot.

> :contentReference[oaicite:6]{index=6}