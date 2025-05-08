---
description: Use of next/image (native lazy loading, blur placeholders)
---

steps:
  - name: "Installer les dépendances nécessaires"
    run: >
      npm install next react react-dom
    cite: :contentReference[oaicite:1]{index=1}

  - name: "Mettre à jour next.config.js pour formats modernes"
    modify: |
      // next.config.js
      module.exports = {
        images: {
          formats: ['image/avif', 'image/webp'],       # Active AVIF et WebP :contentReference[oaicite:2]{index=2}
          minimumCacheTTL: 60,                         # Cache d’une minute pour éviter la surcharge :contentReference[oaicite:3]{index=3}
        },
      };
    cite: :contentReference[oaicite:4]{index=4}

  - name: "Remplacer les <img> par <Image>"
    manual: |
      • Rechercher toutes les balises <img src="..."> dans `src/`  
      • Importer `Image` : `import Image from 'next/image'`  
      • Pour chaque occurrence, convertir :
        ```jsx
        <img src="/image.png" alt="desc" />
        ```
        en
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
      • Si l’image est une importation statique, Next.js génère automatiquement `blurDataURL` :contentReference[oaicite:5]{index=5}

  - name: "Vérifier les attributs obligatoires"
    run: >
      grep -R "Image" -n src/ | xargs -L1 grep -nE "width|height|alt"
    note: >
      Assurer que chaque `<Image>` spécifie `width`, `height` et `alt`  
      (sinon le build échouera avec une erreur) :contentReference[oaicite:6]{index=6}

  - name: "Optimiser les images statiques vers WebP/AVIF"
    run: >
      npx imagemin src/assets/images/* --plugin=imagemin-webp --plugin=imagemin-avif --out-dir=public/images
    note: >
      Convertit les fichiers sources en WebP/AVIF avant le build  
      (améliore la compression sans perte de qualité) :contentReference[oaicite:7]{index=7}

  - name: "Exécuter le build Next.js"
    run: >
      npm run build
    note: "Vérifier qu’il n’y a aucune erreur liée aux images et que la génération de formats fonctionne." :contentReference[oaicite:8]{index=8}

  - name: "Analyser le rapport Lighthouse"
    run: >
      npx lighthouse http://localhost:3000 \
        --only-categories=performance,accessibility \
        --output=json --output-path=./lighthouse-image-audit.json
    cite: :contentReference[oaicite:9]{index=9}

  - name: "Valider les scores Core Web Vitals"
    analyze: >
      Ouvrir `lighthouse-image-audit.json` et vérifier :
      • First Contentful Paint (FCP)  
      • Largest Contentful Paint (LCP)  
      • Cumulative Layout Shift (CLS)  
      Tous les indicateurs doivent être dans la zone verte (> 90) :contentReference[oaicite:10]{index=10}