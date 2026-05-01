# 003 - Tenancy fixes applied: anclora-content-generator-ai

## Estado

Modo: fixes aplicados y validados localmente  
Repo: /home/toni/projects/anclora-content-generator-ai  
Rama: fix/tenancy-content-generate-server-workspace  

## Fixes aplicados

### 1. /api/content/generate

Archivo:

- src/app/api/content/generate/route.ts

Cambio:

- El endpoint deja de usar `workspaceId` recibido desde el cliente.
- El workspace se resuelve en servidor con `getAuthenticatedWorkspace()`.
- `workspaceId` se elimina de los campos requeridos del body.
- `WorkspaceAuthError` devuelve 401, 403 o 500 según corresponda.

Impacto:

- Reduce riesgo de generación de contenido en workspace ajeno.

### 2. /api/content/status

Archivo:

- src/app/api/content/status/route.ts

Cambio:

- El endpoint requiere workspace autenticado.
- El job consultado debe pertenecer al workspace autenticado.
- Si el job no existe o no pertenece al workspace, devuelve 404.

Impacto:

- Reduce fuga de información por `jobId`.

### 3. /api/content/linkedin/publish

Archivo:

- src/app/api/content/linkedin/publish/route.ts

Cambio:

- El endpoint requiere workspace autenticado.
- El `jobId` debe pertenecer al workspace autenticado antes de publicar.
- Si el job no existe o no pertenece al workspace, devuelve 404.

Impacto:

- Reduce riesgo de publicación cruzada entre workspaces.

## Riesgo pendiente

El endpoint `linkedin/publish` sigue publicando `body.content`.

Riesgo:

- Un usuario autenticado podría publicar contenido arbitrario usando un `jobId` válido de su workspace.
- El flujo debería comprobar que el contenido procede del job aprobado o de una salida revisada.

## Próxima acción

Crear un agente `publishing-guard` para auditar seguridad de publicación, aprobación editorial y consistencia entre job, contenido y canal.
