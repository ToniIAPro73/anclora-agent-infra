# Workflow: content-rag-editorial-review-v1

## Objetivo

Crear un flujo seguro de generación y validación editorial para Anclora usando tres agentes:

1. content-orchestrator
2. rag-custodian
3. editorial-guardian

## Estado

Validado en piloto local con Hermes.

## Orden del flujo

### 1. content-orchestrator

Convierte una solicitud editorial en:

- brief,
- estructura,
- borrador inicial,
- riesgos,
- handoff.

No publica, no aprueba, no inventa datos y no modifica archivos.

### 2. rag-custodian

Revisa los claims del borrador y determina:

- claims sensibles,
- clasificación,
- nivel de soporte,
- evidencia requerida,
- recomendación,
- versión prudente si falta fuente.

Si el nivel de soporte es nulo, usa la versión prudente obligatoria:

> La disponibilidad de activos premium en determinadas ubicaciones de Mallorca puede ser una señal relevante para el análisis, pero no debe interpretarse de forma aislada. Conviene contrastarla con ubicación, regulación, liquidez, calidad del activo, contexto de mercado y horizonte de inversión.

### 3. editorial-guardian

Revisa:

- compliance,
- tono Anclora,
- claims sin fuente,
- causalidad no verificada,
- observaciones factuales sin soporte,
- riesgo reputacional,
- aptitud para publicación.

## Criterios de bloqueo

Bloquear si aparece:

- promesa de rentabilidad,
- retorno garantizado,
- causalidad económica sin fuente,
- tendencia de mercado sin fuente,
- fuente implícita,
- cifras sin origen,
- claims absolutos,
- publicación directa sin aprobación humana.

## Resultado del piloto

El flujo detectó y bloqueó correctamente la frase:

> La escasez estructural de suelo premium en Mallorca y la demanda sostenida de activos de alta gama están reduciendo la oferta disponible y reforzando la valoración de ubicaciones prime.

Motivo:

- escasez estructural sin fuente,
- demanda sostenida sin fuente,
- causalidad económica,
- afectación de valoración,
- nivel de soporte nulo.

## Próxima evolución

Crear un cuarto agente:

- `tenancy-auditor`

Objetivo:

- revisar rutas, workspaceId, auth, organizaciones, RLS, endpoints y riesgos multi-tenant en `anclora-content-generator-ai`.
