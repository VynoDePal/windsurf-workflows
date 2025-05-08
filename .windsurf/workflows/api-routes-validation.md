---
description: Validation des schémas de requête avec Zod
---

steps:
  - name: "Installer les dépendances Zod et utilitaires"
    run: |
      npm install zod zod-error
    note: "Zod pour la validation de schémas, zod-error pour générer des messages d’erreur lisibles."  
    cite: :contentReference[oaicite:4]{index=4}

  - name: "Définir un customErrorMap Zod"
    create: |
      // src/utils/customErrorMap.ts
      import { ZodErrorMap, ZodIssue } from 'zod';

      export const customErrorMap: ZodErrorMap = (issue: ZodIssue, ctx) => {
        switch (issue.code) {
          case 'invalid_type':
            return { message: `Champ mal formé : attendu ${issue.expected}` };
          case 'too_small':
            return { message: `Valeur trop petite (minimum ${issue.minimum})` };
          default:
            return { message: ctx.defaultError };
        }
      };
    note: "Permet de remplacer les messages techniques par des libellés métier clairs."  
    cite: :contentReference[oaicite:5]{index=5}

  - name: "Créer un middleware de validation générique"
    create: |
      // src/middleware/validate.ts
      import { NextApiRequest, NextApiResponse } from 'next';
      import { AnyZodObject } from 'zod';

      export function validateSchema(schema: AnyZodObject) {
        return async (req: NextApiRequest, res: NextApiResponse, next: () => void) => {
          try {
            await schema.parseAsync(req.method === 'GET' ? req.query : req.body);
            return next();
          } catch (err) {
            if (err instanceof ZodError) {
              return res.status(400).json({
                status: 'error',
                errors: err.format(),
              });
            }
            throw err;
          }
        };
      }
    note: "Intercepte la requête avant la logique métier et renvoie un 400 sur validation échouée."  
    cite: :contentReference[oaicite:6]{index=6}

  - name: "Ajouter un middleware d’authentification"
    create: |
      // src/middleware/auth.ts
      import { NextResponse } from 'next/server';
      import type { NextRequest } from 'next/server';

      export function authMiddleware(req: NextRequest) {
        const token = req.cookies.get('authToken');
        if (!token || !isValidToken(token)) {
          return new NextResponse(JSON.stringify({
            status: 'error',
            message: 'Non autorisé',
          }), { status: 401 });
        }
        return NextResponse.next();
      }
    note: "Utiliser `middleware.ts` pour toutes les routes `/api/*`, sécuriser avec JWT ou session."  
    cite: :contentReference[oaicite:7]{index=7}

  - name: "Appliquer les middlewares dans `pages/api/…`"
    manual: |
      // pages/api/user.ts
      import { NextApiRequest, NextApiResponse } from 'next';
      import { z } from 'zod';
      import { validateSchema } from '../../middleware/validate';
      import { authMiddleware } from '../../middleware/auth';

      const userSchema = z.object({
        name: z.string().min(2),
        email: z.string().email(),
      }).refine(data => data.email.endsWith('@example.com'), {
        message: 'Email doit être @example.com',
      });

      export default function handler(req: NextApiRequest, res: NextApiResponse) {
        return authMiddleware(req as any, res as any, async () => {
          await validateSchema(userSchema)(req, res, async () => {
            try {
              // Logique métier ici
              const user = await createUser(req.body);
              res.status(201).json({ status: 'success', data: user });
            } catch (error) {
              console.error(error);
              res.status(500).json({
                status: 'error',
                message: 'Erreur interne du serveur',
              });
            }
          });
        });
      }
    note: "Chaînage : auth → validation → logique métier → try/catch pour erreurs serveur."  
    cite: :contentReference[oaicite:8]{index=8}

  - name: "Standardiser la structure de réponse"
    run: |
      # Créer un helper response.ts
      cat << 'EOF' > src/utils/response.ts
      export const success = (data: any) => ({ status: 'success', data });
      export const error = (message: string, code: number = 500) => ({ status: 'error', message, code });
      EOF
    note: "Utiliser ces helpers pour uniformiser tous les endpoints."  
    cite: :contentReference[oaicite:9]{index=9}

  - name: "Tester les routes API"
    run: |
      npm install --save-dev jest supertest
      # Exemple de test
      cat << 'EOF' > __tests__/user.test.ts
      import request from 'supertest';
      import { createServer } from 'http';
      import handler from '../pages/api/user';
      describe('POST /api/user', () => {
        it('rejette un payload invalide', async () => {
          const res = await request(createServer(handler)).post('/api/user').send({ name: 'A' });
          expect(res.status).toBe(400);
          expect(res.body.status).toBe('error');
        });
      });
      EOF
    note: "Vérifier validations, codes HTTP et messages d’erreur."  
    cite: :contentReference[oaicite:10]{index=10}