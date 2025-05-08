---
description: Checks the site's meta tags (title, description), semantic HTML structure and performance (loading time) to optimize SEO.
---

steps:

  - name: "Générer un rapport Lighthouse SEO"
    run: >
      npx lighthouse http://localhost:3000 \
        --only-categories=seo,performance \
        --output=json --output=html \
        --output-path=./lighthouse-seo-report
    note: "Assurez-vous que le serveur dev Next.js tourne sur le port 3000"  
    cite: :contentReference[oaicite:2]{index=2}

  - name: "Extraire et valider les balises meta"
    run: |
      # Extraire <title>
      grep -Po '(?<=<title>).*?(?=</title>)' ./lighthouse-seo-report.html > titles.txt
      # Extraire meta description
      grep -Po '(?<=<meta name="description" content=").*?(?=")' ./lighthouse-seo-report.html > descriptions.txt
    manual: |
      - Vérifier que chaque titre fait moins de 60 caractères (±310 pixels) et est unique:contentReference[oaicite:3]{index=3}  
      - Vérifier que chaque description fait entre 50 et 155 caractères et inclut un Call-to-Action si possible:contentReference[oaicite:4]{index=4}  
    cite: :contentReference[oaicite:5]{index=5}

  - name: "Vérifier la structure des en-têtes Hn"
    manual: |
      - Ouvrir `lighthouse-seo-report.html` et rechercher les audits `document-title` et `heading-order`.  
      - S’assurer d’un seul `<h1>` par page, suivi d’une hiérarchie cohérente `<h2>`→`<h3>`…:contentReference[oaicite:6]{index=6}  
      - Corriger dans le code Next.js (`<Head>` et JSX) si nécessaire.  
    cite: :contentReference[oaicite:7]{index=7}

  - name: "Contrôler robots.txt et sitemap.xml"
    run: |
      # Vérifier l'existence
      test -f public/robots.txt && echo "robots.txt présent" || echo "robots.txt manquant"
      test -f public/sitemap.xml && echo "sitemap.xml présent" || echo "sitemap.xml manquant"
    manual: |
      - `robots.txt` doit lister ou bloquer les sections critiques, sans empêcher le crawl des pages clés:contentReference[oaicite:8]{index=8}  
      - `sitemap.xml` doit référencer toutes les pages importantes, généré automatiquement via bibliothèques (e.g. `next-sitemap`).  
    cite: :contentReference[oaicite:9]{index=9}

  - name: "Valider les données structurées"
    run: |
      # Tester avec l’API Google Rich Results (via cURL)
      curl -s -H "Content-Type: application/json" \
        -d "{\"url\":\"http://localhost:3000\"}" \
        "https://searchconsole.googleapis.com/v1/urlTestingTools/richResults:run" \
        > rich-results.json
    manual: |
      - Importer `rich-results.json` et identifier les erreurs/warnings de Schema.org  
      - Corriger les JSON-LD dans `_document.tsx` ou composants Next.js selon les recommandations Google:contentReference[oaicite:10]{index=10}  
    cite: :contentReference[oaicite:11]{index=11}

  - name: "Analyser les attributs alt des images"
    run: |
      # Lister les images sans alt ou avec alt vide
      grep -R --include="*.tsx" "<Image" -n pages/ | \
        xargs grep -L 'alt='
    manual: |
      - Ajouter un `alt` descriptif et concis (<100 caractères) pour chaque image importante:contentReference[oaicite:12]{index=12}:contentReference[oaicite:13]{index=13}  
      - S’assurer que la description reflète le contenu et utilise au besoin les mots-clés ciblés.  
    cite: :contentReference[oaicite:14]{index=14}

  - name: "Synthèse et rapport final"
    run: |
      echo "SEO Audit terminé. Rapport disponible dans lighthouse-seo-report.html et rich-results.json"
    manual: |
      - Regrouper vos observations et corrections dans un document de suivi (CHANGELOG ou ticket tracker).  
      - Planifier les itérations pour corriger les points critiques en priorité.  
    cite: :contentReference[oaicite:15]{index=15}