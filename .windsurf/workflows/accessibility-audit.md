---
description: Performs a WCAG audit: checks color contrast, ARIA attributes, semantic structure and keyboard navigation, then proposes remedies for violations.
---

# Accessibility Audit Workflow

## Steps

### 1. Install and configure axe-core and ESLint a11y
```bash
npm install --save-dev axe-core @axe-core/react eslint-plugin-jsx-a11y
```

**Implementation note:**  
Add in _app.tsx:
```javascript
import React from 'react';
if (process.env.NODE_ENV === 'development') {
  const ReactAxe = require('@axe-core/react');
  ReactAxe(React, document, 1000);
}
```
> :contentReference[oaicite:1]{index=1}

### 2. Run Lighthouse via CLI
```bash
npx lighthouse http://localhost:3000 \
  --only-categories=accessibility \
  --output=json --output=html \
  --output-path=./lighthouse-a11y
```
> :contentReference[oaicite:2]{index=2}

### 3. Analyze the Lighthouse report
Open lighthouse-a11y.html and identify audits for `color-contrast`, `aria-*`, `html-has-lang`
> :contentReference[oaicite:3]{index=3}

### 4. Run axe-core via CLI
```bash
npx axe http://localhost:3000 \
  --save=./axe-report.json
```

**Implementation note:**  
Import axe-report.json into Cascade for detailed analysis
> :contentReference[oaicite:4]{index=4}

### 5. Check contrast ratio
Use the WebAIM Contrast Checker tool and ensure a ratio ≥ 4.5:1 for normal text
> :contentReference[oaicite:5]{index=5}

### 6. Test keyboard navigation
Disable your mouse, use Tab to navigate through the entire page;  
identify and fix elements without visible focus,  
adjust `tabindex` and `:focus-visible` styles.
> :contentReference[oaicite:6]{index=6}

### 7. Check semantic structure
Open wave.webaim.org or axe to validate headings (h1-h6) and landmarks;  
correct the hierarchy, add `role="banner"`, `role="main"`.
> :contentReference[oaicite:7]{index=7}

### 8. Generate violations report
```bash
npx axe http://localhost:3000 \
  --save=./accessibility-report.json
```

**Implementation note:**  
Import accessibility-report.json into Cascade to list all violations and suggestions
> :contentReference[oaicite:8]{index=8}

### 9. Configure ESLint a11y
```bash
# Verify that .eslintrc.js contains:
extends: ['plugin:jsx-a11y/recommended']
&& npm run lint -- --ext .tsx,.jsx
```
> :contentReference[oaicite:9]{index=9}