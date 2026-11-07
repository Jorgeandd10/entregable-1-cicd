# Entregable 1 - CI/CD: Introducción a Git, GitHub y GitHub Actions

## Integrantes

**Nombres usados en AUTOR:** Jorge Andres Duran Cotamo

| Nombre completo           | Usuario de GitHub                              | Rol                         |
| ------------------------- | ---------------------------------------------- | --------------------------- |
| Jorge Andres Duran Cotamo | [@Jorgeandd10](https://github.com/Jorgeandd10) | Propietario del repositorio |

## Ejecución exitosa del workflow

Enlace a una ejecución exitosa de `mi_workflow.yml`: https://github.com/Jorgeandd10/entregable-1-cicd/actions/runs/42392900291

## Respuestas de la plantilla del workflow

1. **Nombre del workflow:** `Entregable 1 - Mi primer workflow`. Es el nombre que aparece en la pestaña Actions.
2. **Evento al subir cambios:** `push`, limitado a la rama `main`.
   - 2b. **Evento manual:** `workflow_dispatch`, que habilita el botón "Run workflow".
3. **Nombres en el saludo:** se reemplazó `[NOMBRES COMPLETOS]` por el nombre del integrante, igual que en `AUTOR` y en esta sección de Integrantes.
4. **Comando para la fecha y hora:** `date`, usado como `$(date)`. Bash ejecuta el comando y sustituye su resultado dentro del texto. La hora mostrada es la del runner (UTC), no la hora local.
5. **Versión de Python:** `'3.13'`, entre comillas para que YAML la trate como texto y no como número decimal.
6. **Ejecución del script:** `python mi_script.py`.

## Preguntas del taller

### 1. ¿Qué eventos configuraste para disparar tu workflow y por qué?

Configuré dos eventos:

- **`push` en la rama `main`:** el workflow se ejecuta automáticamente cada vez que subo cambios a la rama principal. Así verifico que cada cambio integrado funciona, sin tener que acordarme de ejecutarlo. Esta es la idea base de la integración continua.
- **`workflow_dispatch`:** permite ejecutar el workflow manualmente desde la pestaña Actions. Es útil para volver a probarlo sin hacer un commit nuevo, por ejemplo para revisar los logs o validar que todo sigue funcionando.

### 2. ¿Qué sistema operativo utiliza el runner `ubuntu-latest` y por qué puede ejecutar tu workflow aunque tu computadora sea Windows o macOS? ¿Qué relación tiene el runner con el shell de los pasos `run`?

`ubuntu-latest` es una máquina virtual con **Ubuntu Linux** (la versión estable más reciente que ofrece GitHub). Por eso el script muestra `Sistema Operativo: Linux`.

El workflow funciona aunque mi computadora sea Windows porque **no se ejecuta en mi computadora**. GitHub crea una máquina virtual limpia en sus servidores, descarga mi repositorio con `actions/checkout` y ejecuta allí los pasos. Mi PC solo sirve para escribir el código y hacer push.

El runner determina el **shell por defecto** de los pasos `run`: en Ubuntu y macOS normalmente es **Bash**, y en Windows es **PowerShell**. Por eso un mismo comando puede funcionar en un runner y fallar en otro. En este taller se fijó `shell: bash` en el paso del mensaje, para asegurar que `$(date)` se interprete con sintaxis de Bash.

### 3. Acciones pre-construidas del Marketplace de GitHub Actions

- **`actions/setup-node`:** instala una versión específica de Node.js en el runner y puede guardar en caché las dependencias de npm, yarn o pnpm. Sirve para compilar y probar proyectos de JavaScript o TypeScript.
- **`actions/cache`:** guarda y restaura carpetas entre ejecuciones (por ejemplo dependencias de `pip`, `npm` o `maven`). Así no se descargan todo cada vez, y el pipeline es más rápido.
- **`docker/build-push-action`:** construye imágenes de Docker a partir de un `Dockerfile` y las publica en un registro como Docker Hub o GitHub Container Registry. Sirve para empaquetar aplicaciones y prepararlas para el despliegue.
- **`actions/github-script`:** permite escribir pequeños scripts en JavaScript que usan la API de GitHub dentro del workflow. Sirve para automatizar tareas del repositorio, como comentar en un Pull Request, crear issues o poner etiquetas.

### 4. ¿Qué es un secreto en GitHub Actions y para qué se usa? ¿Cómo lo usarías en tu workflow?

Un **secreto** es un valor sensible (una contraseña, un token o una clave de API) que se guarda cifrado en la configuración del repositorio y no dentro del código. Se usa para que el workflow pueda conectarse a servicios externos (un proveedor cloud, Docker Hub, una base de datos) sin exponer credenciales en el repositorio, que es público.

Para usarlo:

1. En GitHub voy a **Settings > Secrets and variables > Actions > New repository secret** y creo, por ejemplo, `DOCKERHUB_TOKEN`.
2. En el workflow lo referencio con `${{ secrets.DOCKERHUB_TOKEN }}`. Por ejemplo, lo paso como variable de entorno (`env`) o como parámetro `with` de una acción de inicio de sesión, antes de publicar una imagen.

GitHub intenta ocultar los secretos en los logs, pero nunca se deben imprimir con `echo` ni escribirse en archivos o artefactos. Este taller no requiere crear secretos.

### 5. Reflexión sobre el uso de GitHub Actions en mi flujo de trabajo diario

GitHub Actions me permite delegar tareas repetitivas que hoy hago a mano y en las que es fácil equivocarse. Automatizaría principalmente:

- **Pruebas automáticas en cada push y Pull Request:** ejecutar las pruebas unitarias (por ejemplo con `pytest`) cada vez que alguien sube cambios. Así detecto errores antes de que lleguen a `main`, y el equipo recibe retroalimentación inmediata sin depender de que alguien recuerde probar.
- **Revisión de estilo y calidad del código:** ejecutar un linter o formateador (como `flake8` o `black` en Python) en cada Pull Request. Así el código del equipo sigue un mismo estándar y las revisiones se enfocan en la lógica y no en detalles de formato.
- **Despliegue automático:** después de que las pruebas pasen, construir la aplicación (por ejemplo una imagen de Docker) y desplegarla en un entorno de pruebas en la nube. Esto reduce los errores de los despliegues manuales y permite entregar valor más rápido.
