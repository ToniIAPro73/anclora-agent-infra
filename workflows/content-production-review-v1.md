# Workflow: content-production-review-v1

## Objetivo

Crear un flujo editorial seguro para Anclora usando dos agentes:

1. content-orchestrator
2. editorial-guardian

## Estado

Validado en piloto local con Hermes.

## Repos implicados

- /home/toni/projects/anclora-content-generator-ai

## Agentes Hermes implicados

- /home/toni/hermes/anclora-agent-infra/agents/content-orchestrator.md
- /home/toni/hermes/anclora-agent-infra/agents/editorial-guardian.md

## Prompts implicados

- /home/toni/hermes/anclora-agent-infra/prompts/content-orchestrator.prompt.md
- /home/toni/hermes/anclora-agent-infra/prompts/editorial-guardian.prompt.md

## Flujo

### 1. Solicitud editorial

El usuario solicita una pieza indicando:

- canal
- idioma
- público objetivo
- tema
- objetivo
- restricciones
- fuentes disponibles, si existen

### 2. content-orchestrator

Responsable de:

- normalizar intención,
- identificar canal, idioma y público,
- formular tesis prudente,
- distinguir señal, hipótesis y evidencia,
- proponer estructura,
- redactar borrador inicial,
- detectar riesgos,
- preparar handoff para editorial-guardian.

### 3. editorial-guardian

Responsable de:

- revisar compliance,
- revisar calidad editorial,
- detectar claims sin fuente,
- bloquear promesas o exageraciones,
- proponer versión corregida,
- emitir nota de publicación.

### 4. Criterios de validación

Una pieza es apta solo si:

- no promete rentabilidad,
- no usa claims absolutos,
- no inventa cifras,
- no afirma causalidad sin fuente,
- no convierte causalidad sin fuente en observación factual,
- distingue señal, hipótesis y evidencia,
- usa tono Anclora,
- mantiene CTA suave,
- no induce expectativas financieras.

## Resultado del piloto

El flujo detectó correctamente:

- afirmaciones categóricas,
- claims causales sin fuente,
- observaciones factuales no verificadas,
- riesgo reputacional,
- riesgo publicitario,
- necesidad de lenguaje de señales.

## Próxima evolución

Crear rag-custodian para validar qué claims tienen fuente, knowledge pack o evidencia interna.
