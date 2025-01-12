# Documentación de la Pipeline de Jenkins

## Índice

1. [Introducción](#introducción)
2. [Objetivos del Proyecto](#objetivos-del-proyecto)
3. [Pasos para la Configuración de la Pipeline en Jenkins](#pasos-para-la-configuración-de-la-pipeline-en-jenkins)
   1. [Crear el Jenkinsfile](#crear-el-jenkinsfile)
   2. [Instalar Jenkins y Plugins Necesarios](#instalar-jenkins-y-plugins-necesarios)
   3. [Configurar el Proyecto en Jenkins](#configurar-el-proyecto-en-jenkins)
   4. [Configurar las Credenciales en Jenkins](#configurar-las-credenciales-en-jenkins)
   5. [Configurar la Vista de Build Monitor (Opcional)](#configurar-la-vista-de-build-monitor-opcional)
4. [Ejecutar la Pipeline](#ejecutar-la-pipeline)
5. [Scripts Utilizados en la Pipeline](#scripts-utilizados-en-la-pipeline)
6. [Resultados y Notificaciones](#resultados-y-notificaciones)
7. [Conclusión](#conclusión)

## Introducción

Jenkins es una herramienta de integración continua (CI) y entrega continua (CD) muy utilizada en el desarrollo de software para automatizar el proceso de construcción, pruebas y despliegue de aplicaciones. Con Jenkins, podemos definir pipelines para orquestar todas las fases de este proceso, desde la compilación del código hasta el despliegue en un entorno de producción.

### Conceptos Clave de Jenkins

- **Pipeline:** Es un conjunto de pasos definidos que automatizan tareas específicas como compilación, prueba y despliegue. En Jenkins, los pipelines pueden ser definidos de forma declarativa o programática a través de un archivo `Jenkinsfile`.
- **Stage:** Una etapa dentro de un pipeline que representa una parte específica del proceso de CI/CD, como la ejecución de pruebas o el despliegue del proyecto.
- **Agentes:** Son los entornos donde se ejecutan las tareas dentro de un pipeline. Puede ser un nodo de Jenkins o una máquina externa.
- **Parámetros:** Son valores que podemos definir al inicio de la ejecución de un pipeline y que pueden ser utilizados en las diferentes etapas del pipeline.

## Objetivos del Proyecto

Este proyecto consiste en configurar una pipeline de Jenkins para un proyecto React. La pipeline realiza las siguientes tareas:

1. **Instalar dependencias** necesarias (Node.js, Vercel CLI).
2. **Pedir datos al usuario** (nombre del executor, motivo y Chat ID de Telegram).
3. **Ejecutar el linter** para verificar el estilo de código utilizando ESLint.
4. **Ejecutar pruebas** con Jest.
5. **Compilar el proyecto** generando una versión empaquetada.
6. **Actualizar el archivo README.md** con un badge que refleje el estado de los tests.
7. **Hacer commit y push** de los cambios en el repositorio.
8. **Desplegar el proyecto a Vercel**.
9. **Enviar notificaciones a Telegram** con los resultados de la pipeline.

## Pasos para la Configuración de la Pipeline en Jenkins

### Crear el Jenkinsfile

El `Jenkinsfile` es un archivo donde se define la pipeline. Escribe este archivo en el directorio raíz de tu proyecto React. En este archivo, especificamos las siguientes etapas:

1. **Dependencias**: Instalamos Node.js, las dependencias del proyecto y la CLI de Vercel.
2. **Petición de datos**: Solicitamos parámetros como el nombre del executor, el motivo de la ejecución y el Chat ID de Telegram.
3. **Linter**: Ejecutamos ESLint para verificar el código.
4. **Test**: Ejecutamos las pruebas con Jest.
5. **Build**: Realizamos la compilación del proyecto.
6. **Update Readme**: Actualizamos el README.md con el estado de los tests.
7. **Push Changes**: Realizamos el commit y push de los cambios al repositorio.
8. **Deploy to Vercel**: Desplegamos el proyecto en Vercel si todo ha pasado correctamente.
9. **Notificación**: Enviamos un mensaje a Telegram con el estado de la pipeline.

### Instalar Jenkins y Plugins Necesarios

Para usar Jenkins en tu proyecto, primero debes instalarlo. Puedes instalar Jenkins en tu máquina local o en un servidor. Una vez instalado Jenkins, debes instalar algunos plugins necesarios:

- **Plugin de NodeJS:** Para gestionar las versiones de Node.js.
- **Plugin de Build Monitor View:** Para visualizar el estado de las ejecuciones de Jenkins.

### Configurar el Proyecto en Jenkins

1. **Crear un nuevo proyecto** en Jenkins:
   - En la UI de Jenkins, selecciona "Nuevo Item".
   - Elige "Pipeline" como tipo de proyecto y asigna un nombre adecuado.

2. **Configurar los parámetros**:
   - Define los parámetros que utilizarás en tu `Jenkinsfile`. En nuestro caso, estos parámetros son `EXECUTOR`, `MOTIVO` y `CHAT_ID`.

3. **Configurar la fuente de código**:
   - Conecta el proyecto a tu repositorio de GitHub para que Jenkins pueda acceder al código y ejecutar el `Jenkinsfile`.

4. **Configurar el archivo `Jenkinsfile`**:
   - En la configuración del proyecto de Jenkins, en la sección de "Pipeline", selecciona "Pipeline script from SCM".
   - Especifica el repositorio GitHub y la rama donde se encuentra el archivo `Jenkinsfile`.

### Configurar las Credenciales en Jenkins

Las credenciales, como los tokens de Vercel y Telegram, deben ser configuradas de forma segura en Jenkins:

1. Ve a "Administrar Jenkins" > "Administrar Credenciales".
2. Agrega nuevas credenciales para Vercel y Telegram, asignándoles un ID (por ejemplo, `VERCEL_TOKEN` y `TELEGRAM_TOKEN`).
3. En el `Jenkinsfile`, utiliza las credenciales con el bloque `withCredentials`.

### Configurar la Vista de Build Monitor (Opcional)

El plugin **Build Monitor View** te permite visualizar el estado de las ejecuciones de la pipeline. Puedes configurarlo de la siguiente manera:

1. Ve a "Administrar Jenkins" > "Configurar Jenkins".
2. En la sección de vistas, agrega una vista de tipo "Build Monitor View".
3. Configura la vista para que muestre las tareas ejecutadas en Jenkins y su estado (Éxito, Fallo, etc.).

## Ejecutar la Pipeline

Una vez configurado todo, puedes ejecutar la pipeline de la siguiente forma:

1. En la página principal del proyecto en Jenkins, haz clic en "Construir ahora".
2. La pipeline se ejecutará en el orden de las etapas definidas en el `Jenkinsfile`.
3. Asegúrate de que todas las etapas se completen correctamente y que se envíen las notificaciones correspondientes.

## Scripts Utilizados en la Pipeline

La pipeline de Jenkins utiliza varios scripts para automatizar diferentes tareas en el proceso de integración y entrega continua. A continuación, se describen los scripts utilizados:

### `deployToVercel.bat`

Este script se encarga de desplegar el proyecto en **Vercel**. Al ejecutar este script, se verifica que el token de Vercel esté configurado correctamente, luego se establece como una variable de entorno y finalmente se realiza el despliegue del proyecto utilizando la CLI de Vercel. Si el despliegue se realiza correctamente, el script lo confirmará con un mensaje; si no, se mostrará un error.

### `sendTelegramMessage.bat`

Este script permite enviar notificaciones a **Telegram** con los resultados de las distintas etapas de la pipeline. Utiliza la API de Telegram y requiere el token del bot, el ID del chat y el mensaje que se desea enviar. Verifica que se proporcionen estos parámetros antes de intentar enviar el mensaje. Si el mensaje se envía correctamente, el script confirmará el envío; en caso contrario, se mostrará un error.

### `updateReadme.js`

Este script está diseñado para actualizar el archivo `README.md` con el estado de los **tests** que se ejecutan durante la pipeline. Dependiendo del resultado de los tests (si son exitosos o fallan), se actualizará el archivo `README.md` con un badge que refleja el estado de los tests. Este badge puede ser de color verde (para tests exitosos) o rojo (para tests fallidos). El script lee el contenido del `README.md`, busca la sección de "RESULTADO DE LOS ÚLTIMOS TESTS" y actualiza el badge correspondiente.

## Resultados y Notificaciones

Al finalizar la ejecución de la pipeline, se enviará una notificación a Telegram con los resultados de cada etapa. El mensaje incluirá los siguientes estados:

- **Linter**: Si el código pasó la verificación de ESLint.
- **Test**: Si los tests de Jest pasaron o fallaron.
- **Deploy to Vercel**: El estado del despliegue en Vercel.

## Conclusión

Este proyecto configura una pipeline de Jenkins para un proyecto React, integrando varias etapas clave como la ejecución de tests, el análisis de código con ESLint, el despliegue en Vercel y las notificaciones a Telegram. Usando Jenkins, podemos automatizar estos procesos, garantizando que el código se construya, se pruebe y se despliegue de manera eficiente y consistente.
