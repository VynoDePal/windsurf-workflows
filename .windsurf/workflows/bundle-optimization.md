---
description: This workflow analyzes bundle size, identifies the heaviest modules and recommends splitting strategies (lazy loading, code splitting) to improve loading performance.
---

1. Installer et configurer les outils d’analyse

run: npm install --save-dev source-map-explorer webpack-bundle-analyzer compression-webpack-plugin

2. Lancer un build en mode production avec analyse

run: ANALYZE=true npm run build

3. Explorer les rapports générés

analyze: npx source-map-explorer .next/static/chunks/*.js

4. Identifier les points d’optimisation

manual:
        - Relever les librairies > 100 KB
        - Lister les bundles mono-page trop volumineux

5. Mettre en place le code splitting

patch:
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