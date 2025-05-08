---
description: Detection of untranslated texts and key extraction via i18next-scanner
---

# String Extraction for Internationalization

## Steps

### 1. Install i18next-scanner
```bash
npm install --save-dev i18next-scanner
```
> i18next-scanner analyzes your code to extract translation keys; see official documentation.

> :contentReference[oaicite:1]{index=1}

### 2. Create scanner configuration file
```javascript
// i18next-scanner.config.js
module.exports = {
  input: ['src/**/*.{js,jsx,ts,tsx}'],
  output: './public/locales/$LOCALE/$NAMESPACE.json',
  options: {
    debug: false,
    removeUnusedKeys: true,
    sort: true,
  },
  transform: function (file, enc, done) {
    const parser = this.parser;
    let content = file.contents.toString(enc);
    parser.parseFuncFromString(content, { list: ['t', 'i18n.t', 'useTranslation'] });
    done();
  }
};
```
> This configuration extracts via `t('key')`, `i18n.t` or `useTranslation()` according to the i18next API.

> :contentReference[oaicite:2]{index=2}

### 3. Run key extraction
```bash
npx i18next-scanner --config i18next-scanner.config.js
```

**Result:**
- Generates/updates `public/locales/<locale>/<namespace>.json`
- Produces a log of keys that were added, updated, or removed

> :contentReference[oaicite:3]{index=3}

### 4. Identify hardcoded strings
- Review the i18next-scanner log for any text not found in existing resources (hardcoded) :contentReference[oaicite:4]{index=4}
- For each detected string, add a key in the right namespace and replace the text with `t('namespace:key')`.

> :contentReference[oaicite:5]{index=5}

### 5. Check Next.js i18n configuration
Make sure that `next.config.js` contains the correct `i18n` object:
```javascript
module.exports = {
  i18n: {
    locales: ['en', 'fr', 'es'],    // all supported languages
    defaultLocale: 'en',            // default language
    localeDetection: true           // automatic detection
  }
};
```
(Next.js has been handling i18n routing since v10.0.0) :contentReference[oaicite:6]{index=6}

> :contentReference[oaicite:7]{index=7}

### 6. Validate JSON resource files
- Verify that each locale has the same namespaces and keys (no missing entries)
- Use a diff or JSON comparison tool to detect misalignments
- Commit the updated files (`public/locales/**`)

> :contentReference[oaicite:8]{index=8}

### 7. Generate i18n coverage report
```bash
node scripts/report-i18n-coverage.js
```

**Note:**
- This script scans `src/` and `public/locales/`
- Calculates the percentage of strings covered by translation files
- Produces an HTML/JSON report to identify gaps

> :contentReference[oaicite:9]{index=9}

### 8. Integrate with CI/CD
```yaml
# .github/workflows/i18n-validate.yml
name: "i18n Validation"
on: [push, pull_request]
jobs:
  i18n-coverage:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - run: npm ci
      - run: npx i18next-scanner --config i18next-scanner.config.js
      - run: node scripts/report-i18n-coverage.js --failBelow 90
```
> Fails the pipeline if i18n coverage is below 90%.

> :contentReference[oaicite:10]{index=10}