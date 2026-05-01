# 001 - Auditoría inicial de anclora-content-generator-ai

## Estado

Fecha: 2026-04-29  
Modo: read-only  
Repo: /home/toni/projects/anclora-content-generator-ai  
Agente: repo-auditor / content-strategist  

## Stack detectado

- Frontend: Next.js 15, React 19, TypeScript, Tailwind CSS v4, shadcn/ui.
- Backend y DB: Neon PostgreSQL + Drizzle ORM.
- Vector store: pgvector.
- RAG y embeddings: Transformers.js local con Xenova/all-MiniLM-L6-v2 y Google Embeddings opcional.
- Auth: Better Auth con organizaciones.
- Testing: Vitest y Playwright.
- LLMs previstos: Ollama, Groq y Anthropic.

## Scripts disponibles

- npm run dev
- npm run build
- npm run start
- npm run lint
- npm run type-check
- npm run test
- npm run test:ui
- npm run test:coverage
- npm run test:smoke
- npm run test:e2e
- npm run db:generate
- npm run db:push
- npm run db:studio
- npm run db:migrate

## Agentes existentes

### Generación

- content-strategist
- research-specialist
- evidence-composer
- insight-extractor

### Validación

- editorial-quality-validator
- compliance-validator
- intent-normalizer

### Post-proceso

- editorial-polisher
- channel-optimizer
- publication-scheduler

### Orquestación

- content-generation-graph

### Skills existentes

- anclorabot-multiagente-system
- anclora-product-ux-guardian
- frontend-design
- anclora-landing-page-conversion
- skill-creator
- ui-design-system

## RAG detectado

- Carpeta principal: src/lib/rag
- Embeddings: local y Google opcional.
- Vector store: pgvector en Neon PostgreSQL.
- API: endpoints bajo /api/rag.
- Fuentes: market, regulation, lifestyle, infrastructure, editorial, general.
- Configuración esperada:
  - RAG_VECTOR_BACKEND=pgvector
  - RAG_EMBEDDING_BACKEND=local|google

## Riesgos técnicos

1. Tenancy débil: algunas rutas podrían aceptar workspaceId desde cliente.
2. Documentación confusa entre Supabase Auth, Neon y RLS.
3. RAG todavía en desarrollo.
4. Complejidad por múltiples proveedores LLM.
5. Transformers.js puede impactar rendimiento si se carga en edge.
6. Gobernanza UI pendiente en /dashboard/*.
7. Dependencias sensibles a cambios.

## Infraestructura Hermes propuesta

### content-orchestrator

Coordina research-specialist, content-strategist y editorial-polisher.

Skill previsto:

- anclora-content-pipeline

### editorial-guardian

Ejecuta compliance-validator y editorial-quality-validator.

Skill previsto:

- anclora-editorial-review

### scheduler-optimizer

Usa publication-scheduler y channel-optimizer.

Skill previsto:

- anclora-publishing-workflow

### rag-custodian

Gestiona ingestas, re-embedding, limpieza de fuentes y trazabilidad.

Skill previsto:

- anclora-rag-maintenance

### tenancy-auditor

Verifica resolución segura de workspaceId en servidor.

Skill previsto:

- anclora-tenancy-hardening

### metrics-tracker

Cierra ciclo draft, review, approved, scheduled y published.

Skill previsto:

- anclora-editorial-telemetry

## Decisión operativa

Primero se implementarán agentes en modo read-only.

Orden recomendado:

1. editorial-guardian
2. content-orchestrator
3. rag-custodian
4. tenancy-auditor
5. metrics-tracker
6. scheduler-optimizer

## Próxima acción

Crear los archivos de agente en:

/home/toni/hermes/anclora-agent-infra/agents

y los prompts operativos en:

/home/toni/hermes/anclora-agent-infra/prompts
