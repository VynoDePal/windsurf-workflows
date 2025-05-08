---
description: Performs a WCAG audit: checks color contrast, ARIA attributes, semantic structure and keyboard navigation, then proposes remedies for violations.
---

steps:
  - name: "Installer et configurer axe-core et ESLint a11y"
    run: >
      npm install --save-dev axe-core @axe-core/react eslint-plugin-jsx-a11y
    note: "Ajoutez dans _app.tsx :\nimport React from 'react';\nif (process.env.NODE_ENV === 'development') {\n  const ReactAxe = require('@axe-core/react');\n  ReactAxe(React, document, 1000);\n}"
    cite: :contentReference[oaicite:1]{index=1}

  - name: "Lancer Lighthouse en CLI"
    run: >
      npx lighthouse http://localhost:3000 \
        --only-categories=accessibility \
        --output=json --output=html \
        --output-path=./lighthouse-a11y
    cite: :contentReference[oaicite:2]{index=2}

  - name: "Analyser le rapport Lighthouse"
    analyze: "Ouvrir lighthouse-a11y.html et identifier les audits `color-contrast`, `aria-*`, `html-has-lang`"
    cite: :contentReference[oaicite:3]{index=3}

  - name: "Exécuter axe-core via CLI"
    run: >
      npx axe http://localhost:3000 \
        --save=./axe-report.json
    note: "Importez axe-report.json dans Cascade pour l’analyse détaillée"
    cite: :contentReference[oaicite:4]{index=4}

  - name: "Vérifier le ratio de contraste"
    manual: "Utiliser l’outil WebAIM Contrast Checker et s’assurer d’un ratio ≥ 4.5 :1 pour le texte normal"
    cite: :contentReference[oaicite:5]{index=5}

  - name: "Tester la navigation au clavier"
    manual: >
      Désactivez la souris, utilisez Tab pour parcourir toute la page ;  
      identifiez et corrigez les éléments sans focus visible,  
      ajustez `tabindex` et styles `:focus-visible`.
    cite: :contentReference[oaicite:6]{index=6}

  - name: "Vérifier la structure sémantique"
    manual: >
      Ouvrir wave.webaim.org ou axe pour valider headings (h1-h6) et landmarks ;  
      corriger la hiérarchie, ajouter `role="banner"`, `role="main"`.
    cite: :contentReference[oaicite:7]{index=7}

  - name: "Générer le rapport de violations"
    run: >
      npx axe http://localhost:3000 \
        --save=./accessibility-report.json
    note: "Importer accessibility-report.json dans Cascade pour lister toutes les violations et suggestions"
    cite: :contentReference[oaicite:8]{index=8}

  - name: "Configurer ESLint a11y"
    run: >
      # Vérifier que .eslintrc.js contient:
      extends: ['plugin:jsx-a11y/recommended']
      && npm run lint -- --ext .tsx,.jsx
    cite: :contentReference[oaicite:9]{index=9}