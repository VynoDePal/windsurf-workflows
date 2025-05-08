---
description: Checks the site's meta tags (title, description), semantic HTML structure and performance (loading time) to optimize SEO.
---

# SEO Optimization Workflow

## Steps

### 1. Generate Lighthouse SEO Report
```bash
npx lighthouse http://localhost:3000 \
  --only-categories=seo,performance \
  --output=json --output=html \
  --output-path=./lighthouse-seo-report
```
**Note:** Make sure the Next.js dev server is running on port 3000
> :contentReference[oaicite:2]{index=2}

### 2. Extract and Validate Meta Tags
```bash
# Extract <title>
grep -Po '(?<=<title>).*?(?=</title>)' ./lighthouse-seo-report.html > titles.txt
# Extract meta description
grep -Po '(?<=<meta name="description" content=").*?(?=")' ./lighthouse-seo-report.html > descriptions.txt
```

**Manual checks:**
- Verify that each title is less than 60 characters (±310 pixels) and unique :contentReference[oaicite:3]{index=3}
- Verify that each description is between 50 and 155 characters and includes a Call-to-Action if possible :contentReference[oaicite:4]{index=4}
> :contentReference[oaicite:5]{index=5}

### 3. Check Heading Structure (Hn)
**Manual checks:**
- Open `lighthouse-seo-report.html` and look for `document-title` and `heading-order` audits
- Ensure there is only one `<h1>` per page, followed by a coherent hierarchy `<h2>`→`<h3>`... :contentReference[oaicite:6]{index=6}
- Fix in Next.js code (`<Head>` and JSX) if necessary
> :contentReference[oaicite:7]{index=7}

### 4. Check robots.txt and sitemap.xml
```bash
# Verify existence
test -f public/robots.txt && echo "robots.txt present" || echo "robots.txt missing"
test -f public/sitemap.xml && echo "sitemap.xml present" || echo "sitemap.xml missing"
```

**Manual checks:**
- `robots.txt` should list or block critical sections without preventing crawling of key pages :contentReference[oaicite:8]{index=8}
- `sitemap.xml` should reference all important pages, generated automatically via libraries (e.g., `next-sitemap`)
> :contentReference[oaicite:9]{index=9}

### 5. Validate Structured Data
```bash
# Test with Google Rich Results API (via cURL)
curl -s -H "Content-Type: application/json" \
  -d "{\"url\":\"http://localhost:3000\"}" \
  "https://searchconsole.googleapis.com/v1/urlTestingTools/richResults:run" \
  > rich-results.json
```

**Manual checks:**
- Import `rich-results.json` and identify Schema.org errors/warnings
- Fix JSON-LD in `_document.tsx` or Next.js components according to Google recommendations :contentReference[oaicite:10]{index=10}
> :contentReference[oaicite:11]{index=11}

### 6. Analyze Image Alt Attributes
```bash
# List images without alt or with empty alt
grep -R --include="*.tsx" "<Image" -n pages/ | \
  xargs grep -L 'alt='
```

**Manual checks:**
- Add a descriptive and concise `alt` (<100 characters) for each important image :contentReference[oaicite:12]{index=12} :contentReference[oaicite:13]{index=13}
- Ensure the description reflects the content and uses target keywords when appropriate
> :contentReference[oaicite:14]{index=14}

### 7. Summary and Final Report
```bash
echo "SEO Audit completed. Report available in lighthouse-seo-report.html and rich-results.json"
```

**Manual checks:**
- Group your observations and corrections in a tracking document (CHANGELOG or ticket tracker)
- Plan iterations to fix critical issues as a priority
> :contentReference[oaicite:15]{index=15}