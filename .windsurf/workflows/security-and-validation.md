---
description: Audit dependencies (npm audit), validate API entries (Zod) and configure secure HTTP headers.
---

steps:
  - name: "1. Audit des dépendances et correction automatique"
    run: >
      # Exécuter l’audit des dépendances
      npm audit --production --json > audit-report.json
    note: "Le rapport JSON sera analysé par Cascade pour lister toutes les vulnérabilités."  
    cite: :contentReference[oaicite:0]{index=0} :contentReference[oaicite:1]{index=1}

  - name: "2. Appliquer les correctifs critiques"
    run: >
      # Mettre à jour automatiquement les dépendances vulnérables
      npm audit fix --force
    note: >
      Utiliser `--force` uniquement si vous avez testé en local,  
      ou préférez `npm audit fix` sans `--force` pour une approche plus prudente.  
    cite: :contentReference[oaicite:2]{index=2} :contentReference[oaicite:3]{index=3}

  - name: "3. Enregistrer l’état final des dépendances"
    run: |
      # Verrouiller les versions en production
      npm ci
      npm shrinkwrap
    note: "Génère un fichier `npm-shrinkwrap.json` pour immuabilité des dépendances en production."  
    cite: :contentReference[oaicite:4]{index=4}

  - name: "4. Installer Zod pour la validation des schémas"
    run: >
      npm install zod
    cite: :contentReference[oaicite:5]{index=5}

  - name: "5. Ajouter la validation Zod dans les API Routes"
    manual: |
      Pour chaque fichier `pages/api/*.ts` ou `app/api/*/route.ts`, procédez comme suit :
      1. Importer Zod :
         ```ts
         import { z } from 'zod';
         ```
      2. Définir un schéma :
         ```ts
         const UserSchema = z.object({
           name: z.string().min(1),
           email: z.string().email(),
         });
         ```
      3. Parsez et validez :
         ```ts
         export default function handler(req: NextApiRequest, res: NextApiResponse) {
           try {
             const data = UserSchema.parse(req.body);
             // suite de la logique...
           } catch (err) {
             return res.status(400).json({ error: err.errors });
           }
         }
         ```
    note: "Assurez-vous de valider `req.query` de la même manière pour les routes GET."  
    cite: :contentReference[oaicite:6]{index=6} :contentReference[oaicite:7]{index=7}

  - name: "6. Configurer les en-têtes de sécurité dans next.config.js"
    create: |
      // next.config.js
      module.exports = {
        async headers() {
          return [
            {
              source: '/(.*)',
              headers: [
                {
                  key: 'Content-Security-Policy',
                  value: \"default-src 'self'; script-src 'self'; style-src 'self' 'unsafe-inline'; img-src 'self' data:; frame-ancestors 'none';\"
                },
                { key: 'Strict-Transport-Security', value: 'max-age=63072000; includeSubDomains; preload' },
                { key: 'X-Frame-Options', value: 'DENY' },
                { key: 'X-Content-Type-Options', value: 'nosniff' },
                { key: 'Referrer-Policy', value: 'strict-origin-when-cross-origin' },
              ],
            },
          ];
        }
      };
    note: "Adaptez la directive CSP selon vos besoins (CDN, fonts, etc.)."  
    cite: :contentReference[oaicite:8]{index=8} :contentReference[oaicite:9]{index=9} :contentReference[oaicite:10]{index=10}

  - name: "7. Vérifier la mise en place des headers"
    run: >
      # Démarrer le serveur de développement
      npm run dev
    manual: |
      Avec curl ou Postman, envoyer une requête GET :  
      `curl -I http://localhost:3000`  
      Vérifier que les en-têtes CSP, HSTS, X-Frame-Options, etc. sont bien présents dans la réponse.
    cite: :contentReference[oaicite:11]{index=11}

  - name: "8. Automatiser l’audit en CI"
    create: |
      # .github/workflows/security.yml
      name: 'Security Audit'
      on: [push, pull_request]
      jobs:
        audit:
          runs-on: ubuntu-latest
          steps:
            - uses: actions/checkout@v3
            - name: Setup Node.js
              uses: actions/setup-node@v3
              with: { 'node-version': '18' }
            - run: npm ci
            - run: npm audit --production --audit-level=moderate
            - run: npm run lint  # inclut la validation des schémas
    note: "Le job CI échoue si des vulnérabilités `moderate` ou supérieures sont détectées."  
    cite: :contentReference[oaicite:12]{index=12} :contentReference[oaicite:13]{index=13}