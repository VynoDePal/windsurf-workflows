---
description: Détection des textes non traduits et extraction de clés via i18next-scanner
---

steps:
  - name: "1. Installer i18next-scanner"
    run: >
      npm install --save-dev i18next-scanner
    note: "i18next-scanner analyse votre code pour extraire les clés de traduction ; voir doc officielle."  
    cite: :contentReference[oaicite:1]{index=1}

  - name: "2. Créer le fichier de configuration scanner"
    create: |
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
    note: "Cette config extrait via `t('key')`, `i18n.t` ou `useTranslation()` selon l’API i18next."  
    cite: :contentReference[oaicite:2]{index=2}

  - name: "3. Exécuter l’extraction des clés"
    run: >
      npx i18next-scanner --config i18next-scanner.config.js
    result: |
      • Génère/ met à jour `public/locales/<locale>/<namespace>.json`  
      • Produit un log des clés ajoutées, mises à jour ou supprimées  
    cite: :contentReference[oaicite:3]{index=3}

  - name: "4. Identifier les chaînes codées en dur"
    manual: >
      • Parcourir le log de i18next-scanner pour tout texte non trouvé dans les ressources existantes (hardcoded):contentReference[oaicite:4]{index=4}  
      • Pour chaque chaîne détectée, ajouter une clé dans le bon namespace et replacer le texte par `t('namespace:key')`.
    cite: :contentReference[oaicite:5]{index=5}

  - name: "5. Vérifier la configuration i18n de Next.js"
    manual: |
      Ouvrir `next.config.js` et s’assurer que l’objet `i18n` contient bien :
      ```js
      module.exports = {
        i18n: {
          locales: ['en', 'fr', 'es'],    // toutes les langues supportées
          defaultLocale: 'en',            // langue par défaut
          localeDetection: true           // détection automatique
        }
      };
      ```
      (Next.js gère l’i18n routing depuis v10.0.0):contentReference[oaicite:6]{index=6}  
    cite: :contentReference[oaicite:7]{index=7}

  - name: "6. Valider les fichiers de ressources JSON"
    manual: >
      • Vérifier que chaque locale possède les mêmes namespaces et clés (pas d’entrées manquantes).  
      • Utiliser un diff ou un outil de comparaison JSON pour détecter les désalignements.  
      • Committer les fichiers mis à jour (`public/locales/**`).
    cite: :contentReference[oaicite:8]{index=8}

  - name: "7. Générer un rapport de couverture i18n"
    run: >
      node scripts/report-i18n-coverage.js
    note: |
      – Ce script parcourt `src/` et `public/locales/`  
      – Calcule le pourcentage de chaînes couvertes par les fichiers de traduction  
      – Produit un rapport HTML/JSON pour identifier les lacunes
    cite: :contentReference[oaicite:9]{index=9}

  - name: "8. Intégrer au CI/CD"
    create: |
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
    note: "Échoue le pipeline si la couverture i18n est inférieure à 90 %."  
    cite: :contentReference[oaicite:10]{index=10}