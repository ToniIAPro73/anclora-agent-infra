# 002 - Tenancy Audit: anclora-content-generator-ai

## Estado

Fecha: 2026-04-29  
Modo: read-only  
Repo: /home/toni/projects/anclora-content-generator-ai  
Agente: tenancy-auditor  

## Archivos revisados

- src/lib/server-auth.ts
- src/lib/auth/workspace.ts
- src/lib/rag/retrieval-neon.ts
- src/app/api/content/generate/route.ts
- src/app/api/content/approve/route.ts
- src/app/api/content/status/route.ts
- src/app/api/content/drafts/route.ts
- src/app/api/content/linkedin/publish/route.ts
- src/app/api/content/metrics/route.ts
- src/app/api/content/ingest/route.ts
- src/app/api/rag/import-document/route.ts
- src/app/api/rag/knowledge-packs/route.ts
- src/app/api/rag/sources/route.ts
- src/app/api/workspace/settings/route.ts
- sdd/features/anclora-feat-tenancy-hardening/tenancy-hardening-spec-v1.md

## Hallazgos

### 1. POST /api/content/generate acepta workspaceId desde cliente

Severidad: crítica.

Evidencia reportada:

- La ruta extrae `workspaceId` directamente del body.
- No llama a `getAuthenticatedWorkspace`.

Impacto:

- Un usuario podría intentar generar contenido en un workspace ajeno.

Recomendación mínima:

- Reemplazar `workspaceId` del body por `getAuthenticatedWorkspace()`.

Recomendación robusta:

- Resolver siempre workspace en servidor.
- Validar membership/organization.
- Añadir tests cross-workspace.

### 2. POST /api/content/linkedin/publish no valida pertenencia del jobId

Severidad: alta.

Evidencia reportada:

- Usa `jobId` del body para recuperar estado.
- No verifica que el usuario tenga acceso al workspace del job.

Impacto:

- Riesgo de publicación cruzada si las credenciales o jobs no están aislados.

Recomendación mínima:

- Verificar que el workspace del job corresponde al usuario autenticado.

### 3. GET /api/content/status accesible por jobId

Severidad: media.

Evidencia reportada:

- Cualquier persona con `jobId` podría consultar estado.

Impacto:

- Posible fuga de información si el identificador es expuesto o adivinable.

Recomendación mínima:

- Requerir sesión.
- Filtrar jobs por workspace del usuario.

### 4. Endpoints mock sin autenticación

Severidad: baja.

Endpoints:

- GET /api/content/drafts
- GET /api/content/metrics

Impacto:

- Riesgo bajo si son mocks, pero patrón inseguro.

Recomendación:

- Proteger con autenticación o marcarlos explícitamente como desarrollo.

### 5. retrieval-neon.ts depende del workspaceId recibido por caller

Severidad: media.

Evidencia reportada:

- `retrieveSimilarChunks` recibe `workspaceId` como parámetro.

Impacto:

- Si el endpoint pasa un `workspaceId` no validado, se rompe aislamiento RAG.

Recomendación:

- Asegurar que todos los callers pasan un workspace resuelto en servidor.

## Prioridad de corrección

1. `/api/content/generate`
2. `/api/content/linkedin/publish`
3. `/api/content/status`
4. RAG retrieval callers
5. endpoints mock
6. tests cross-workspace

## Regla de hardening

No aceptar `workspaceId` desde body, query params o headers como fuente de verdad.

El workspace activo debe derivarse de:

- sesión autenticada,
- organization activa,
- membership,
- permisos internos.

## Próxima acción

Crear `tenancy-fix-planner` para generar plan de corrección sin modificar archivos.
