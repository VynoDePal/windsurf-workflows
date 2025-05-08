---
description: This workflow analyzes bundle size, identifies the heaviest modules and recommends splitting strategies (lazy loading, code splitting) to improve loading performance.
---

# Bundle Optimization Workflow

## Steps

### 1. Install and configure analysis tools
```bash
npm install --save-dev source-map-explorer webpack-bundle-analyzer compression-webpack-plugin  
```
> Installs source-map-explorer (map-based analysis) and webpack-bundle-analyzer (visual treemap) :contentReference[oaicite:0]{index=0}

### 2. Run a production build with analysis enabled
```bash
ANALYZE=true npm run build  
```
> Activates webpack-bundle-analyzer during production build (see "production" mode) :contentReference[oaicite:1]{index=1}

### 3. Explore generated reports
```bash
npx source-map-explorer .next/static/chunks/*.js  
```
> Displays a treemap showing the origin of each byte to identify large modules :contentReference[oaicite:2]{index=2}

### 4. Identify optimization opportunities
- Note libraries > 100 KB  
- List overly large single-page bundles  

> Document dependencies and pages that need splitting :contentReference[oaicite:3]{index=3}

### 5. Implement code splitting
```javascript
// next.config.js or webpack.config.js
module.exports = {
  optimization: {
    splitChunks: {
      chunks: 'all',
      minSize: 20000,
      maxSize: 244000
    }
  }
};
```
> Configure SplitChunksPlugin to split chunks into 'all' targets :contentReference[oaicite:4]{index=4}

### 6. Implement lazy loading for critical components
```javascript
import dynamic from 'next/dynamic';
const HeavyComponent = dynamic(() => import('../components/Heavy'), {
  loading: () => <Spinner />,
  ssr: false
});
```
> Uses `next/dynamic` (or React.lazy/Suspense) to load on demand :contentReference[oaicite:5]{index=5}

### 7. Add asset compression
```javascript
const CompressionPlugin = require('compression-webpack-plugin');
// in webpack/plugins or next.config.js:
plugins: [
  new CompressionPlugin({ algorithm: 'gzip', test: /\.(js|css|html)$/ }),
  new CompressionPlugin({ algorithm: 'brotliCompress', filename: '[path].br', test: /\.(js|css|html)$/ })
]
```
> Generates gzip and Brotli versions for static files :contentReference[oaicite:6]{index=6}

### 8. Re-generate the build & re-analyze
```bash
npm run build && npx source-map-explorer .next/static/chunks/*.js
```
> Verify size reduction and effective bundle splitting :contentReference[oaicite:7]{index=7}

### 9. Automate the audit as "always-on"
```json
{
  "event": "onCommand",
  "command": "performance-bundle-audit",
  "files": ["next.config.js", "webpack.config.js", ".next/**"]
}
```
> Allows triggering the workflow on demand or on saving key configs :contentReference[oaicite:8]{index=8}