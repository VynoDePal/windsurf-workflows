---
description: Dependency audit and code analysis to detect known vulnerabilities and XSS risks, suggesting security updates or patches.
---

steps:
  1. # Installer et configurer les outils d’analyse
     run: |
       npm install --save-dev source-map-explorer webpack-bundle-analyzer compression-webpack-plugin  
     # Installe source-map-explorer (analyse via map) et webpack-bundle-analyzer (treemap visuel) :contentReference[oaicite:0]{index=0}  

  2. # Lancer un build en mode production avec analyse
     run: |
       ANALYZE=true npm run build  
     # Active webpack-bundle-analyzer pendant la compilation prod (voir mode “production”) :contentReference[oaicite:1]{index=1}  

  3. # Explorer les rapports générés
     analyze: |
       npx source-map-explorer .next/static/chunks/*.js  
     # Affiche un treemap montrant l’origine de chaque byte pour repérer les gros modules :contentReference[oaicite:2]{index=2}  

  4. # Identifier les points d’optimisation
     manual: |
       - Relever les librairies > 100 KB  
       - Lister les bundles mono-page trop volumineux  
     # Notez les dépendances et pages à découper :contentReference[oaicite:3]{index=3}  

  5. # Mettre en place le code splitting
     patch: |
       // next.config.js ou webpack.config.js
       module.exports = {
         optimization: {
           splitChunks: {
             chunks: 'all',
             minSize: 20000,
             maxSize: 244000
           }
         }
       };
     # Configure SplitChunksPlugin pour fractionner les chunks en cibles ‘all’ :contentReference[oaicite:4]{index=4}  

  6. # Implémenter le lazy loading des composants critiques
     patch: |
       import dynamic from 'next/dynamic';
       const HeavyComponent = dynamic(() => import('../components/Heavy'), {
         loading: () => <Spinner />,
         ssr: false
       });
     # Utilise `next/dynamic` (ou React.lazy/Suspense) pour charger à la demande :contentReference[oaicite:5]{index=5}  

  7. # Ajouter la compression des assets
     patch: |
       const CompressionPlugin = require('compression-webpack-plugin');
       // dans webpack/plugins ou next.config.js:
       plugins: [
         new CompressionPlugin({ algorithm: 'gzip', test: /\.(js|css|html)$/ }),
         new CompressionPlugin({ algorithm: 'brotliCompress', filename: '[path].br', test: /\.(js|css|html)$/ })
       ]
     # Génére des versions gzip et Brotli pour les fichiers statiques :contentReference[oaicite:6]{index=6}  

  8. # Re-générer le build & ré-analyser
     run: |
       npm run build && npx source-map-explorer .next/static/chunks/*.js
     # Vérifier la réduction de taille et le découpage effectif des bundles :contentReference[oaicite:7]{index=7}  

  9. # Automatiser l’audit en “always-on”
     file: ".windsurf/rules/performance-bundle.rule.json"
     content: |
       {
         "event": "onCommand",
         "command": "performance-bundle-audit",
         "files": ["next.config.js", "webpack.config.js", ".next/**"]
       }
     # Permet de déclencher le workflow à la demande ou sur sauvegarde des configs clés :contentReference[oaicite:8]{index=8}  