---
description: Dependency audit and code analysis to detect known vulnerabilities and XSS risks, suggesting security updates or patches.
---

steps:
  - name: "Audit des dépendances"
    run: |
      cd <votre-projet>
      npm audit --json > audit-report.json
    output: "audit-report.json"
    description: >
      Exécute `npm audit --json` (ou `yarn audit --json`) pour générer un rapport détaillé des
      vulnérabilités connues dans vos dépendances. :contentReference[oaicite:0]{index=0}

  - name: "Application des correctifs automatiques"
    run: |
      npm audit fix --force || echo "Certains correctifs nécessitent une intervention manuelle"
    description: >
      Tente de corriger automatiquement les vulnérabilités. En cas d’échec, liste les actions manuelles
      requises (mise à jour majeure ou remplacement de paquet) :contentReference[oaicite:1]{index=1}

  - name: "Inventaire des vulnérabilités non corrigées"
    run: |
      jq '.advisories | to_entries[] | {package: .value.module_name, severity: .value.severity, url: .value.url}' audit-report.json
    description: >
      Utilise `jq` (ou équivalent) pour extraire l’arborescence des vulnérabilités non corrigées
      et planifier leur résolution :contentReference[oaicite:2]{index=2}

  - name: "Analyse XSS statique"
    run: |
      grep -R "innerHTML" src/ || echo "Aucun innerHTML détecté"
      grep -R "dangerouslySetInnerHTML" src/ || echo "Aucune utilisation de dangerouslySetInnerHTML"
    description: >
      Recherche toute assignation directe au DOM via `innerHTML` ou `dangerouslySetInnerHTML`,
      points d’injection XSS potentiels :contentReference[oaicite:3]{index=3}

  - name: "Sanitisation des entrées"
    run: |
      # Exemple avec la librairie 'he'
      npm install he --save
      # Remplacer dans le code :
      # element.innerHTML = he.encode(userInput);
      echo "Vérifier et appliquer l'encodage HTML via he.encode()" 
    description: >
      Implémente l’encodage des caractères spéciaux (HTML encoding) pour toute insertion dans le DOM
      (ex. `he.encode(userInput)`) :contentReference[oaicite:4]{index=4}

  - name: "Analyse avec ESLint Security Plugin"
    run: |
      npm install --save-dev eslint-plugin-security eslint-plugin-security-node
      npx eslint src/ --rule '{"security/detect-object-injection": "error"}'
    description: >
      Exécute ESLint avec `eslint-plugin-security` et `eslint-plugin-security-node` pour repérer
      les hotspots de sécurité (injections, fonctions dangereuses) :contentReference[oaicite:5]{index=5}

  - name: "Vérification CSP et autres headers"
    run: |
      grep -R "Content-Security-Policy" next.config.js || echo "Ajouter les headers via middleware"
    description: >
      Vérifie la présence d’en-têtes CSP, HSTS, X-Frame-Options, etc., dans `next.config.js` ou
      dans votre middleware. Propose des patterns standardisés :contentReference[oaicite:6]{index=6}

  - name: "Rapport synthétique et recommandations"
    run: |
      echo "Génération d’un résumé des vulnérabilités et des actions recommandées"
    description: >
      Compile les résultats précédents dans un rapport final (JSON ou Markdown), 
      listant pour chaque faille la gravité, la localisation et la solution préconisée :contentReference[oaicite:7]{index=7}
