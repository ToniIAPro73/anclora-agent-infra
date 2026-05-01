# Reglas para agentes técnicos

- En modo dry-run, ningún agente puede escribir en disco, ni en el repo ni en `/tmp`.
- Un dry-run debe devolver propuesta, diff o plan en la respuesta, no crear archivos temporales.
- Las rutas deben copiarse exactamente desde los archivos leídos.
- Está prohibido inventar rutas aproximadas.
- Si un agente duda de una ruta, debe pedir confirmación o ejecutar solo lectura de `ls`/`find`, nunca asumir.
- Si un agente propone un comando con una ruta incorrecta, debe autocorregirse antes de continuar.

## Detalle de reglas para agentes técnicos

- En modo dry-run, ningún agente puede escribir en disco, ni en el repo ni en `/tmp`.
- Un dry-run debe devolver propuesta, diff o plan en la respuesta, no crear archivos temporales.
- Las rutas deben copiarse exactamente desde los archivos leídos.
- Está prohibido inventar rutas aproximadas.
- Si un agente duda de una ruta, debe pedir confirmación o ejecutar solo lectura de `ls`/`find`, nunca asumir.
- Si un agente propone un comando con una ruta incorrecta, debe autocorregirse antes de continuar.
