Conclusión:
Aquí tienes el contenido del runbook para pegar en `~/hermes/anclora-agent-infra/runbooks/hermes-wsl2-installation.md`.

````markdown
# Runbook: Instalación y validación de Hermes Agent en WSL2 Ubuntu

## Estado

Validado y operativo.

## Entorno

- Sistema: Windows 11 + WSL2 Ubuntu
- Usuario Linux: `toni`
- Directorio base: `/home/toni`
- No usar `/mnt/c` para el repo ni para el runtime de Hermes.

## Estructura final

```text
/home/toni/
├── .hermes/                         # Runtime, config, .env, memoria, sesiones, logs
├── projects/
│   └── hermes-agent/                 # Repo Git + venv activo
└── hermes/
    └── anclora-agent-infra/          # Infraestructura propia Anclora
````

Si existe:

```text
/home/toni/.hermes/hermes-agent.OLD
```

es la instalación antigua en cuarentena. No borrar hasta confirmar varios días de funcionamiento correcto.

## Instalación activa

El binario activo es:

```bash
/home/toni/.local/bin/hermes
```

Debe apuntar a:

```bash
/home/toni/projects/hermes-agent/venv/bin/hermes
```

Comprobación:

```bash
which hermes
readlink -f ~/.local/bin/hermes
hermes --version
```

Resultado esperado:

```text
/home/toni/.local/bin/hermes
/home/toni/projects/hermes-agent/venv/bin/hermes
Hermes Agent v0.12.0
```

## Repositorios Git

Repositorio activo:

```bash
cd ~/projects/hermes-agent
```

Remotes recomendados:

```text
origin   git@github.com:ToniIAPro73/hermes-agent.git
upstream git@github.com:NousResearch/hermes-agent.git
```

Comprobación:

```bash
git remote -v
git status -sb
git branch -vv
```

Estado esperado:

```text
main sincronizado con origin/main
origin apunta al fork personal
upstream apunta al repo oficial
```

La rama antigua con el cambio local de `package-lock.json` quedó guardada como:

```text
toni/package-lock-update
```

## Configuración principal

Archivo principal:

```bash
~/.hermes/config.yaml
```

Configuración validada:

```yaml
model:
  default: deepseek/deepseek-v3.2
  provider: openrouter
```

OpenRouter está operativo.

Comprobación segura:

```bash
grep -nE "model:|provider:|default:" ~/.hermes/config.yaml | head -20
```

## API keys y secretos

Archivo de secretos:

```bash
~/.hermes/.env
```

No leer ni pegar completo este archivo.

Comprobación segura con redacción:

```bash
grep -nE "OPENROUTER|OPENAI|ANTHROPIC|TELEGRAM|TWILIO|TOKEN|API_KEY" ~/.hermes/.env \
  | sed -E 's/(=).*/=***REDACTED***/'
```

Validación directa de OpenRouter:

```bash
set -a
source ~/.hermes/.env
set +a

curl -sS https://openrouter.ai/api/v1/key \
  -H "Authorization: Bearer $OPENROUTER_API_KEY" \
  | jq
```

Resultado esperado:

```json
{
  "data": {
    "label": "...",
    "limit_remaining": ...
  }
}
```

Si devuelve `HTTP 401` o `User not found`, generar una API key nueva en OpenRouter y reemplazar `OPENROUTER_API_KEY`.

## Permisos recomendados

Aplicar:

```bash
chmod 700 ~/.hermes
chmod 600 ~/.hermes/.env
```

Comprobar:

```bash
ls -ld ~/.hermes
ls -la ~/.hermes/.env
```

## Seguridad operativa

Reglas:

* No pegar API keys, tokens, cookies, claves SSH privadas ni `.env` completo en chats.
* No leer `~/.hermes/.env` completo desde Hermes.
* Para revisar variables, usar comandos con `sed` redacted.
* No ejecutar comandos destructivos sin confirmación explícita.
* No arrancar `hermes gateway` salvo petición expresa.
* No activar nuevas plataformas de mensajería sin revisar allowlists.
* No guardar `SUDO_PASSWORD` en `.env` salvo caso excepcional y controlado.

Recomendación: comentar o eliminar:

```bash
# SUDO_PASSWORD=
```

Si una contraseña de email, token o API key se expone accidentalmente, rotarla.

## Estado de `hermes doctor`

Comando:

```bash
hermes doctor
```

Estado esperado:

```text
✓ Python 3.11.x
✓ ~/.hermes/.env file exists
✓ ~/.hermes/config.yaml exists
✓ Config version up to date
✓ ~/.local/bin/hermes → correct target
✓ OpenRouter API
✓ Built-in memory active
```

Avisos aceptables/no críticos:

```text
docker not found
agent-browser not installed
tinker-atropos not found
No GITHUB_TOKEN
web missing EXA/PARALLEL/TAVILY/FIRECRAWL
image_gen missing keys
vision system dependency not met
discord missing token
Skills Hub not initialized
```

Estos avisos solo indican herramientas opcionales no configuradas.

## Aviso conocido: title_generation

Puede aparecer:

```text
Auxiliary title generation failed: No LLM provider configured for task=title_generation provider=auto.
```

Estado: conocido y no bloqueante.

Impacto:

* Solo afecta al título automático de sesión.
* No impide usar Hermes.
* No afecta al modelo principal.
* No afecta a OpenRouter.

Configuración neutra recomendada:

```yaml
auxiliary:
  title_generation:
    provider: auto
    model: ''
    base_url: ''
    api_key: ''
    timeout: 30
    extra_body: {}
```

## Modelos auxiliares

Modelo principal:

```text
openrouter → deepseek/deepseek-v3.2
```

Modelo auxiliar económico propuesto para compresión:

```text
qwen/qwen3.6-flash
```

Bloque recomendado si funciona correctamente:

```yaml
auxiliary:
  compression:
    provider: auto
    model: qwen/qwen3.6-flash
    base_url: ''
    api_key: ''
    timeout: 120
    extra_body: {}
```

Si da problemas, revertir a:

```yaml
auxiliary:
  compression:
    provider: auto
    model: ''
    base_url: ''
    api_key: ''
    timeout: 120
    extra_body: {}
```

## Uso normal

Arrancar Hermes:

```bash
hermes
```

Prompt inicial recomendado:

```text
Trabaja en modo seguro. Primero diagnostica. No ejecutes herramientas ni cambios destructivos sin confirmación explícita. No leas ~/.hermes/.env completo. Si necesitas revisar variables, usa comandos redacted.
```

Prompt de verificación:

```text
Confirma provider y modelo actual. No ejecutes herramientas. Responde solo con proveedor, modelo y estado.
```

Resultado esperado:

```text
Provider: openrouter
Model: deepseek/deepseek-v3.2
Estado: conectado
```

## Gateway

No arrancar por defecto:

```bash
hermes gateway
```

Antes de activar gateway revisar:

* Allowlist de Telegram/Email/Twilio/WhatsApp.
* `GATEWAY_ALLOW_ALL_USERS=false`.
* `TELEGRAM_ALLOWED_USERS`.
* `TELEGRAM_HOME_CHANNEL`.
* Webhooks activos.
* Tokens rotados.
* Logs.
* Riesgos de ejecución remota.

## Comandos destructivos que requieren confirmación explícita

No ejecutar sin aprobación humana:

```bash
rm -rf
git reset --hard
git clean -fd
git push --force
docker system prune
docker volume rm
chmod 777
sudo rm
drop database
DROP TABLE
```

## Backup

Backup básico del runtime:

```bash
mkdir -p ~/backups/hermes
tar -czf ~/backups/hermes/hermes-backup-$(date +%F_%H%M%S).tar.gz ~/.hermes
```

Antes de tocar `config.yaml`:

```bash
cp ~/.hermes/config.yaml ~/.hermes/config.yaml.bak-$(date +%F_%H%M%S)
```

Antes de tocar `.env`:

```bash
cp ~/.hermes/.env ~/.hermes/.env.bak-$(date +%F_%H%M%S)
chmod 600 ~/.hermes/.env.bak-*
```

## Actualización de Hermes

Antes de actualizar:

```bash
hermes --version
tar -czf ~/backups/hermes/hermes-pre-upgrade-$(date +%F_%H%M%S).tar.gz ~/.hermes
```

Actualizar repo:

```bash
cd ~/projects/hermes-agent
git fetch upstream
git status -sb
```

Si se quiere alinear `main` con upstream:

```bash
git switch main
git reset --hard upstream/main
git push --force-with-lease origin main
```

Usar este flujo solo si no hay cambios locales importantes o si están guardados en otra rama.

## Checklist final de validación

```bash
which hermes
readlink -f ~/.local/bin/hermes
hermes --version
hermes doctor
grep -nE "model:|provider:|default:" ~/.hermes/config.yaml | head -20
```

Resultado esperado:

```text
/home/toni/.local/bin/hermes
/home/toni/projects/hermes-agent/venv/bin/hermes
Hermes Agent v0.12.0
model.default: deepseek/deepseek-v3.2
model.provider: openrouter
OpenRouter API OK
```

## Estado final

Hermes está listo para uso por CLI en WSL2.

No activar gateway ni automatizaciones hasta completar revisión de seguridad específica.

```

