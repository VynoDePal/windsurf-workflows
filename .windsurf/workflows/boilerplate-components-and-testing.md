---
description: Automate the generation of reusable React components, custom hooks and unit tests to boost productivity.
---

steps:
  - name: "Installer Plop.js en dépendance de développement"
    run: "npm install --save-dev plop"  
    cite: :contentReference[oaicite:0]{index=0}

  - name: "Créer le fichier de configuration plopfile.js"
    create: |
      // plopfile.js
      module.exports = function (plop) {
        plop.setGenerator('component', {
          description: 'Génère un composant React avec CSS et test',
          prompts: [
            { type: 'input', name: 'name', message: 'Nom du composant ?' },
            { type: 'input', name: 'path', message: 'Chemin relatif (src/components) ?' }
          ],
          actions: [
            {
              type: 'add',
              path: 'src/{{path}}/{{pascalCase name}}/{{pascalCase name}}.tsx',
              templateFile: 'plop-templates/component/Component.tsx.hbs'
            },
            {
              type: 'add',
              path: 'src/{{path}}/{{pascalCase name}}/{{pascalCase name}}.module.css',
              templateFile: 'plop-templates/component/Component.module.css.hbs'
            },
            {
              type: 'add',
              path: 'src/{{path}}/{{pascalCase name}}/index.ts',
              templateFile: 'plop-templates/component/index.ts.hbs'
            },
            {
              type: 'add',
              path: 'src/{{path}}/{{pascalCase name}}/{{pascalCase name}}.test.tsx',
              templateFile: 'plop-templates/component/Component.test.tsx.hbs'
            }
          ]
        });

        plop.setGenerator('hook', {
          description: 'Génère un hook React + test',
          prompts: [
            { type: 'input', name: 'name', message: 'Nom du hook (sans "use") ?' },
            { type: 'input', name: 'path', message: 'Chemin relatif (src/hooks) ?' }
          ],
          actions: [
            {
              type: 'add',
              path: 'src/{{path}}/use{{pascalCase name}}.ts',
              templateFile: 'plop-templates/hook/hook.ts.hbs'
            },
            {
              type: 'add',
              path: 'src/{{path}}/use{{pascalCase name}}.test.ts',
              templateFile: 'plop-templates/hook/hook.test.ts.hbs'
            }
          ]
        });
      };
    note: "Les templates doivent être placés sous `plop-templates/` comme indiqué."  
    cite: :contentReference[oaicite:1]{index=1}

  - name: "Préparer les templates de composant et hook"
    manual: |
      Créez les fichiers suivants dans `plop-templates/` :
      - `component/Component.tsx.hbs`  
      - `component/Component.module.css.hbs`  
      - `component/index.ts.hbs`  
      - `component/Component.test.tsx.hbs`  
      - `hook/hook.ts.hbs`  
      - `hook/hook.test.ts.hbs`  
    cite: :contentReference[oaicite:2]{index=2}

  - name: "Exécuter la génération du composant"
    run: "npx plop component"  
    interactive: |
      ▶ Entrez `MyButton` pour le nom  
      ▶ Entrez `ui/buttons` pour le chemin  
    result: |
      - `src/ui/buttons/MyButton/MyButton.tsx`  
      - `src/ui/buttons/MyButton/MyButton.module.css`  
      - `src/ui/buttons/MyButton/index.ts`  
      - `src/ui/buttons/MyButton/MyButton.test.tsx`  
    cite: :contentReference[oaicite:3]{index=3}

  - name: "Exécuter la génération du hook"
    run: "npx plop hook"  
    interactive: |
      ▶ Entrez `auth` pour le nom → `useAuth.ts`  
      ▶ Entrez `hooks` pour le chemin  
    result: |
      - `src/hooks/useAuth.ts`  
      - `src/hooks/useAuth.test.ts`  
    cite: :contentReference[oaicite:4]{index=4}

  - name: "Configurer Jest et React Testing Library"
    run: >
      npm install --save-dev jest @testing-library/react @testing-library/jest-dom ts-jest
    note: |
      Ajoutez dans `jest.config.js` :
      ```js
      module.exports = {
        preset: 'ts-jest',
        testEnvironment: 'jsdom',
        setupFilesAfterEnv: ['<rootDir>/jest.setup.ts']
      };
      ```
      et dans `jest.setup.ts` :
      ```ts
      import '@testing-library/jest-dom';
      ```
    cite: :contentReference[oaicite:5]{index=5}

  - name: "Lancer tous les tests générés"
    run: "npm test -- --passWithNoTests"
    note: "Vérifier que chaque test initial passe et inspecter le snapshot créé."  
    cite: :contentReference[oaicite:6]{index=6}