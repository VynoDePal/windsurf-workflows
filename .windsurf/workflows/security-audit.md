---
description: Dependency audit and code analysis to detect known vulnerabilities and XSS risks, suggesting security updates or patches.
---

# Security Audit Workflow

## Steps

### 1. Dependency Audit
```bash
cd <your-project>
npm audit --json > audit-report.json
```
> Runs `npm audit --json` (or `yarn audit --json`) to generate a detailed report of known vulnerabilities in your dependencies. :contentReference[oaicite:0]{index=0}

### 2. Apply Automatic Fixes
```bash
npm audit fix --force || echo "Some fixes require manual intervention"
```
> Attempts to automatically fix vulnerabilities. If unsuccessful, lists the required manual actions (major updates or package replacements) :contentReference[oaicite:1]{index=1}

### 3. Inventory of Unfixed Vulnerabilities
```bash
jq '.advisories | to_entries[] | {package: .value.module_name, severity: .value.severity, url: .value.url}' audit-report.json
```
> Uses `jq` (or equivalent) to extract the tree of unfixed vulnerabilities and plan their resolution :contentReference[oaicite:2]{index=2}

### 4. Static XSS Analysis
```bash
grep -R "innerHTML" src/ || echo "No innerHTML detected"
grep -R "dangerouslySetInnerHTML" src/ || echo "No dangerouslySetInnerHTML usage detected"
```
> Searches for any direct assignments to the DOM via `innerHTML` or `dangerouslySetInnerHTML`, potential XSS injection points :contentReference[oaicite:3]{index=3}

### 5. Input Sanitization
```bash
# Example with the 'he' library
npm install he --save
# Replace in code:
# element.innerHTML = he.encode(userInput);
echo "Verify and apply HTML encoding via he.encode()"
```
> Implements encoding of special characters (HTML encoding) for any insertion into the DOM (e.g., `he.encode(userInput)`) :contentReference[oaicite:4]{index=4}

### 6. Analysis with ESLint Security Plugin
```bash
npm install --save-dev eslint-plugin-security eslint-plugin-security-node
npx eslint src/ --rule '{"security/detect-object-injection": "error"}'
```
> Runs ESLint with `eslint-plugin-security` and `eslint-plugin-security-node` to identify security hotspots (injections, dangerous functions) :contentReference[oaicite:5]{index=5}

### 7. CSP and Other Headers Verification
```bash
grep -R "Content-Security-Policy" next.config.js || echo "Add headers via middleware"
```
> Checks for the presence of CSP, HSTS, X-Frame-Options, etc. headers in `next.config.js` or in your middleware. Suggests standardized patterns :contentReference[oaicite:6]{index=6}

### 8. Summary Report and Recommendations
```bash
echo "Generating a summary of vulnerabilities and recommended actions"
```
> Compiles previous results into a final report (JSON or Markdown), listing for each vulnerability its severity, location, and recommended solution :contentReference[oaicite:7]{index=7}
