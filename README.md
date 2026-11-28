# El Heraldo — public CI runner (private source)

Este repositorio contiene únicamente el workflow público que solicita un runner gratuito.
**No contiene ni debe contener código, tests, documentación, historial, backups ni artefactos de El Heraldo.**

## Preparación manual requerida

1. Crea un repositorio nuevo llamado `el-heraldo-ci` bajo `ElCreador5` y configúralo como **Public**.
2. Sube la carpeta `.github/workflows/` de este paquete a la rama predeterminada del repositorio público.
3. En GitHub, crea un Fine-grained personal access token:
   - Resource owner: `ElCreador5`
   - Repository access: Only select repositories → `el-heraldo-bot`
   - Repository permissions: **Contents: Read-only**
   - No concedas permisos de escritura, Actions, Administration, Secrets ni otros permisos.
4. En el repositorio público `el-heraldo-ci`, abre Settings → Secrets and variables → Actions → New repository secret:
   - Name: `PRIVATE_REPO_READ_TOKEN`
   - Value: el token recién creado.
   - Nunca pegues el token en ChatGPT, en un issue, en un archivo o en un commit.
5. Abre Actions → `El Heraldo - private source checks` → Run workflow. También se ejecuta al actualizar este workflow en la rama `main` del repositorio público.

## Qué hace el workflow

- Se dispara manualmente o al actualizar el workflow en la rama `main` de este repositorio; no se ejecuta con `pull_request` ni con eventos de `push` del repositorio privado.
- Descarga exclusivamente la rama privada `Pruebas`.
- Usa el token solo durante el paso de checkout y desactiva `persist-credentials`.
- Instala dependencias, compila los módulos de condenas y persistencia; ejecuta regresiones seleccionadas de condenas, repositorio SQLite de Sugerencias, recuperación tras reinicio, transacciones del panel y cierre de conexiones.
- No publica artifacts, logs de pruebas, cobertura ni archivos del repositorio privado.
- Redirige el detalle de las pruebas a un archivo temporal del runner y solo publica un resultado genérico.
- La ejecución usa salida de pruebas detallada dentro del archivo temporal; el resumen público sigue limitado a identificadores de pruebas y estado.
- No toca `main`, la rama `Pruebas` del repositorio privado, Railway ni Enzonic.

## Límites y seguridad

- El código privado se descarga temporalmente en un runner de GitHub Actions. No se incorpora al historial del repositorio público.
- Los metadatos de ejecución (éxito/fallo, duración y nombre del workflow) serán públicos.
- El código de workflow es público. No añadas disparadores de PR ni permitas que colaboradores externos modifiquen el workflow mientras el secreto esté configurado.
- Un token de solo lectura reduce el impacto, pero no hace imposible la exfiltración si alguien consigue modificar el workflow que se ejecuta en la rama predeterminada. Mantén el repositorio público bajo control exclusivo del propietario.
- Los logs detallados se omiten deliberadamente; un fallo requerirá ejecutar el mismo comando en un entorno privado para diagnosticarlo.

## Alcance inicial

Este primer workflow comprueba el runner público y el bloque de modularización de condenas. No sustituye todavía todos los workflows de regresión del repositorio privado.
Una vez comprobado que asigna runner, se puede ampliar por etapas copiando sus comandos de CI sin publicar la fuente ni los informes internos.
