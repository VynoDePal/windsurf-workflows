---
description: Apply linting (ESLint), formatting (Prettier) and strict typing (TypeScript) to respect code conventions and reduce errors.
---

steps:
  - name: "Installer les dépendances"
    run: >
      npm install --save-dev \
        eslint eslint-config-airbnb eslint-config-airbnb-typescript \
        @typescript-eslint/parser @typescript-eslint/eslint-plugin \
        prettier eslint-plugin-prettier eslint-config-prettier \
        eslint-plugin-jsx-a11y eslint-plugin-import eslint-plugin-react \
        eslint-plugin-react-hooks
    cite: :contentReference[oaicite:0]{index=0}

  - name: "Créer le fichier de configuration ESLint"
    create: |
      // .eslintrc.cjs
      module.exports = {
        parser: '@typescript-eslint/parser',
        extends: [
          'airbnb-typescript', 
          'plugin:react/recommended',
          'plugin:react-hooks/recommended',
          'plugin:jsx-a11y/recommended',
          'prettier'
        ],
        parserOptions: {
          project: './tsconfig.json',
          ecmaFeatures: { jsx: true }
        },
        rules: {
          'react/jsx-filename-extension': ['error', { extensions: ['.tsx'] }],
          'import/extensions': ['error', 'ignorePackages', {
            ts: 'never',
            tsx: 'never'
          }]
        },
        settings: { react: { version: 'detect' } }
      };
    note: "On utilise `eslint-config-prettier` pour désactiver les règles conflictuelles avec Prettier."  
    cite: :contentReference[oaicite:1]{index=1}

  - name: "Créer la configuration Prettier"
    create: |
      // .prettierrc.js
      module.exports = {
        singleQuote: true,
        trailingComma: 'all',
        printWidth: 80,
        arrowParens: 'avoid'
      };
      // .prettierignore
      node_modules/
      build/
      dist/
    cite: :contentReference[oaicite:2]{index=2}

  - name: "Activer le mode strict de TypeScript"
    modify: |
      // tsconfig.json
      {
        "compilerOptions": {
          "strict": true,
          "noImplicitAny": true,
          "strictNullChecks": true,
          "strictFunctionTypes": true,
          "strictBindCallApply": true,
          "strictPropertyInitialization": true,
          "noImplicitThis": true,
          /* autres options standards… */
        }
      }
    note: "Le flag `strict: true` active l'ensemble des contrôles stricts pour une meilleure sûreté de code."  
    cite: :contentReference[oaicite:3]{index=3}

  - name: "Ajouter les scripts npm"
    modify: |
      // package.json
      "scripts": {
        "lint": "eslint 'src/**/*.{ts,tsx}'",
        "format": "prettier --write 'src/**/*.{ts,tsx,js,jsx,json,css,md}'",
        "check": "npm run lint && npm run format -- --check"
      }
    note: >
      Le script `check` permet d’exécuter lint et format en CI pour refuser  
      tout push contenant des erreurs de style ou de typage.
    cite: :contentReference[oaicite:4]{index=4}

  - name: "Vérifier et corriger les violations"
    run: |
      npm run lint -- --fix
      npm run format
    note: "Ces commandes appliquent automatiquement les corrections suggérées par ESLint et Prettier."  
    cite: :contentReference[oaicite:5]{index=5}

  - name: "Vérification des conventions de nommage"
    manual: |
      • Composants React : nommer chaque fichier et dossier en PascalCase (ex. `UserCard.tsx`):contentReference[oaicite:6]{index=6}  
      • Fichiers utilitaires/hooks : nommer en camelCase (ex. `dateFormatter.ts`):contentReference[oaicite:7]{index=7}  
      • Assurer que les exports par défaut de composants utilisent le même nom que le fichier.
    cite: :contentReference[oaicite:8]{index=8}

  - name: "Configurer la CI (ex: GitHub Actions)"
    create: |
      # .github/workflows/lint.yml
      name: Lint & Format
      on: [push, pull_request]
      jobs:
        lint:
          runs-on: ubuntu-latest
          steps:
            - uses: actions/checkout@v3
            - name: Setup Node.js
              uses: actions/setup-node@v3
              with: { 'node-version': '18' }
            - run: npm ci
            - run: npm run check
    note: "Le job CI échoue si ESLint ou Prettier détecte un problème."  
    cite: :contentReference[oaicite:9]{index=9}