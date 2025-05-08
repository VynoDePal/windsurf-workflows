---
description: Use of next/image (native lazy loading, blur placeholders)
---

# Image Optimization Workflow

## Steps

### 1. Install necessary dependencies
```bash
npm install next react react-dom
```
> :contentReference[oaicite:1]{index=1}

### 2. Update next.config.js for modern formats
```javascript
// next.config.js
module.exports = {
  images: {
    formats: ['image/avif', 'image/webp'],       // Enables AVIF and WebP :contentReference[oaicite:2]{index=2}
    minimumCacheTTL: 60,                         // One-minute cache to prevent overload :contentReference[oaicite:3]{index=3}
  },
};
```
> :contentReference[oaicite:4]{index=4}

### 3. Replace <img> tags with <Image>
• Search for all <img src="..."> tags in `src/`  
• Import `Image`: `import Image from 'next/image'`  
• For each occurrence, convert:
```jsx
<img src="/image.png" alt="desc" />
```
to
```jsx
<Image
  src="/image.png"
  alt="desc"
  width={600}
  height={400}
  placeholder="blur"
  blurDataURL="/image-blur.png"
  loading="lazy"
/>
```
• If the image is a static import, Next.js automatically generates `blurDataURL` :contentReference[oaicite:5]{index=5}

### 4. Verify required attributes
```bash
grep -R "Image" -n src/ | xargs -L1 grep -nE "width|height|alt"
```
> Ensure each `<Image>` specifies `width`, `height`, and `alt`  
> (otherwise the build will fail with an error) :contentReference[oaicite:6]{index=6}

### 5. Optimize static images to WebP/AVIF
```bash
npx imagemin src/assets/images/* --plugin=imagemin-webp --plugin=imagemin-avif --out-dir=public/images
```
> Converts source files to WebP/AVIF before build  
> (improves compression without quality loss) :contentReference[oaicite:7]{index=7}

### 6. Run Next.js build
```bash
npm run build
```
> Verify there are no image-related errors and that format generation works. :contentReference[oaicite:8]{index=8}

### 7. Analyze Lighthouse report
```bash
npx lighthouse http://localhost:3000 \
  --only-categories=performance,accessibility \
  --output=json --output-path=./lighthouse-image-audit.json
```
> :contentReference[oaicite:9]{index=9}

### 8. Validate Core Web Vitals scores
Open `lighthouse-image-audit.json` and check:
- First Contentful Paint (FCP)  
- Largest Contentful Paint (LCP)  
- Cumulative Layout Shift (CLS)  
All indicators should be in the green zone (> 90) :contentReference[oaicite:10]{index=10}