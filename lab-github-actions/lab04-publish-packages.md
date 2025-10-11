# Laboratorio 04: Publicar en paquetes de GitHub

__Paso Preliminar__

1. Primero procederemos a logearnos.
2. Para poder iniciar este laboratorio, necesitamos ingresar al siguiente enlace:

https://github.com/skills/publish-packages

Al final de contenido del README.md se encuentra un botón verde "START COURSE", con este boton procederemos a copiar el contenido de este repositorio, a un repositorio local dentro de la cuenta que hemos creado.


## Paso 1: Crear el archivo de flujo de trabajo

_¡Bienvenido a "Publicar paquetes"! :wave:_

Primero, tómese un momento para examinar la imagen a continuación. Muestra la relación entre la _integración continua_, la _entrega continua_ y la _implementación continua_.

![](https://i.imgur.com/xZCkjmU.png)

La **integración continua** (CI) es una práctica en la que los desarrolladores integran código probado en una rama compartida varias veces al día. La **entrega continua** (CD) es la siguiente fase de la **integración continua** (CI), donde también nos aseguramos de empaquetar el código en una _versión_ y almacenarlo en algún lugar, preferiblemente en un repositorio de artefactos. Por último, la **implementación continua** (CD) lleva la **entrega continua** (CD) al siguiente nivel al implementar directamente nuestras versiones al mundo.

[**Docker**](https://www.docker.com/why-docker) es un motor que le permite ejecutar contenedores.
Los contenedores son paquetes de software que pueden ejecutarse de forma fiable en diferentes entornos. Incluyen todo lo necesario para ejecutar la aplicación. Son ligeros en comparación con las máquinas virtuales. Un **Dockerfile** es un documento de texto que contiene todos los comandos e instrucciones necesarios para crear una imagen de Docker. Una **imagen de Docker** es un paquete ejecutable compuesto por código, dependencias, bibliotecas, un entorno de ejecución, variables de entorno y archivos de configuración. Un **contenedor Docker** es una instancia de entorno de ejecución de una imagen de Docker.

Comenzaremos creando el archivo de flujo de trabajo para publicar una imagen de Docker en paquetes de GitHub.

### :keyboard: Actividad: Crear el archivo de flujo de trabajo

1. Abra una nueva pestaña del navegador y siga los pasos de la segunda pestaña mientras lee las instrucciones de esta.
1. Vaya a la pestaña **Código**.
1. Desde el menú desplegable de la rama **principal**, haga clic en la rama **cd**.
1. Vaya a la carpeta `.github/workflows/`, luego seleccione **Agregar archivo** y haga clic en **Crear nuevo archivo**.
1. En el campo **Nombre de su archivo...**, ingrese `publish.yml`.
1. Agregue lo siguiente al archivo `publish.yml`:

```
name: Publish to Docker
on:
  push:
    branches:
      - main
permissions:
  packages: write
  contents: read
jobs:
  publish:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4
      # Add your test steps here if needed...
      - name: Docker meta
        id: meta
        uses: docker/metadata-action@v5
        with:
          images: ghcr.io/${{ github.repository }}/game
          tags: type=sha
      - name: Login to GHCR
        uses: docker/login-action@v3
        with:
          registry: ghcr.io
          username: ${{ github.repository_owner }}
          password: ${{ secrets.GITHUB_TOKEN }}
      - name: Build container
        uses: docker/build-push-action@v5
        with:
          context: .
          push: true
          tags: ${{ steps.meta.outputs.tags }}
```

1. Reemplace `YOURNAME` con su nombre de usuario.
1. Asegúrese de que el nombre de la imagen sea único.
1. Confirme sus cambios.
1. (opcional) Crea una solicitud de extracción para ver todos los cambios que realizarás a lo largo del curso. Haz clic en la pestaña **Solicitudes de extracción**, haz clic en **Nueva solicitud de extracción**, configura `base: main` y `compare:cd`.
1. Espera unos 20 segundos y luego actualiza esta página (la que estás siguiendo). [GitHub Actions](https://docs.github.com/en/actions) actualizará automáticamente al siguiente paso.


## Paso 2: Agregar un Dockerfile

_¡Creaste un flujo de trabajo de publicación! :tada:_

Agregaremos un archivo Dockerfile a la rama cd. Este archivo contiene un conjunto de instrucciones que se almacenan en una imagen Docker. Si lo desea, puede obtener más información sobre los archivos Dockerfile (https://docs.docker.com/engine/reference/builder/).

### :keyboard: Actividad: Agregar un Dockerfile

1. En la rama `cd`, cree `Dockerfile` en la raíz del proyecto e incluya:
```archivo docker
FROM nginx:1.24-alpine
COPY . /usr/share/nginx/html
```
1. Confirme sus cambios.
1. Espera unos 20 segundos y luego actualiza esta página (la que estás siguiendo). [GitHub Actions](https://docs.github.com/en/actions) actualizará automáticamente al siguiente paso.


## Paso 3: Fusiona tus cambios (Merge)

_¡Empecemos a publicar! :heart:_

¡Ahora puedes [fusionar](https://docs.github.com/es/get-started/quickstart/github-glossary#merge) tus cambios!

### :keyboard: Actividad: Fusionar tus cambios

1. Fusiona los cambios de `cd` en `main`. Si creaste la solicitud de extracción en el paso 1, simplemente abre la solicitud y haz clic en **Fusionar solicitud de extracción**. Si no creaste la solicitud de extracción antes, puedes hacerlo ahora siguiendo las instrucciones del paso 1.
1. (opcional) Eliminar la rama `cd`.
1. Espera unos 20 segundos y luego actualiza esta página (la que estás siguiendo). [GitHub Actions](https://docs.github.com/en/actions) actualizará automáticamente al siguiente paso.


## Paso 4: Extrae tu imagen

¡Ahora todo marcha bien! :sparkles:_

¡Vaya, ya está todo funcionando! Puede que tarde unos minutos. Puede que tarde un ratito, así que coge tus palomitas :popcorn: y espera a que termine la compilación antes de continuar.

:cook: Mientras esperamos que finalice la compilación, ocupémonos de algunos requisitos previos.

Para facilitar su uso y garantizar la compatibilidad entre plataformas (Windows, Mac y Linux), nos centraremos en Docker Desktop. Cabe destacar que Docker Engine es la base para ejecutar contenedores, mientras que **[Docker Desktop](https://www.docker.com/blog/how-to-check-docker-version/)** integra Docker Engine, una interfaz gráfica de usuario y una máquina virtual en una sola instalación.

1. Instale [Docker Desktop para Windows](https://docs.docker.com/desktop/install/windows-install/#install-docker-desktop-on-windows).
   * Si está utilizando Mac o Linux, busque los pasos de instalación correctos en el enlace anterior a través del menú del árbol de la izquierda.
1. Abra Docker Desktop y [explore brevemente](https://docs.docker.com/desktop/use-desktop/).
1. Para ejecutar comandos `docker`, acceda a la terminal de línea de comandos a través de Bash, Git Bash, Símbolo del sistema de Windows o PowerShell.

:inbox_tray: Para extraer la imagen de Docker, primero debemos iniciar sesión en Docker.

Antes de que podamos usar esta imagen de Docker, deberá generar un [token de acceso personal (clásico)](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token) que contenga los siguientes permisos:

**Ámbitos del token de acceso personal (clásico)** :coin:
- repositorio (todos)
- escribir:paquetes
- leer:paquetes

![captura de pantalla de la página de creación del token de acceso personal con casillas para repositorio (todos), escritura: paquetes y lectura: paquetes marcadas](https://user-images.githubusercontent.com/3250463/219254714-82bb1da5-33b1-491b-97c0-b25f51494f6a.png)

Usaremos este token para iniciar sesión en Docker y autenticarnos con el paquete.

1. Abra su terminal (se recomienda Bash o Git Bash).
1. Utilice el siguiente comando para iniciar sesión:

```
docker login ghcr.io -u USERNAME
```

1. Reemplace `USERNAME` con su nombre de usuario de GitHub.
1. Ingrese su nuevo token de acceso personal como contraseña.
1. Presione **Enter**.

Si todo salió bien, :crossed_fingers: deberías ver `Inicio de sesión exitoso` en tu terminal.

### :keyboard: Actividad: Extrae tu imagen

1. Copie el comando `pull` de las instrucciones del paquete.
   Consejo: Para acceder a esta página, haz clic en la pestaña **Código** en la parte superior de tu repositorio. Luego, busca la barra de navegación debajo de la descripción del repositorio y haz clic en el enlace **Paquetes**.
     ![captura de pantalla del comando pull en la página del paquete de GitHub](https://user-images.githubusercontent.com/3250463/219254981-9ff949fa-4d01-46e3-9e3d-b8ce3710c2a9.png)
   - O alternativamente, en la siguiente URL reemplace `YOURNAME`, `REPONAME`, y navegue a `https://github.com/users/YOURNAME/packages?repo_name=REPONAME` y haga clic en el nombre del paquete
1. Reemplace `YOURNAME` con su nombre de usuario de GitHub.
1. Reemplace `TAG` con la etiqueta de la imagen.
1. Pegue el comando `pull` en su terminal. Debería verse así:
   - `docker pull ghcr.io/TUNOMBRE/paquetes-de-publicación/juego:ETIQUETA`
1. Presione **Enter**.
1. Deberías ver un resultado que indique que la extracción fue exitosa, como `Estado: Se descargó una imagen más nueva para ghcr.io/YOURNAME/publish-packages/game:TAG`.
   ![captura de pantalla de la salida exitosa de la imagen de Docker](https://user-images.githubusercontent.com/3250463/219255178-3c943a6f-6c15-4f59-9002-228249b1c469.png)
1. _No podemos verificar este paso automáticamente, así que continúe con el siguiente paso a continuación._


## Paso 5: Ejecuta tu imagen

_¡Bien hecho al capturar tu imagen de Docker! :relajado:_

Vamos a intentar ejecutarlo.

### :keyboard: Actividad: Ejecuta tu imagen

1. Busque la información de su imagen escribiendo "docker image ls".
   ![captura de pantalla de la salida del comando ls de imágenes de Docker: enumera las imágenes de Docker, la ETIQUETA DEL REPOSITORIO y la URL de Docker](https://i.imgur.com/UAwRXiq.png)<!-- Esta captura de pantalla debería cambiarse. -->
2. Utilice el siguiente comando para ejecutar un contenedor desde su imagen:
```
docker run -dp 8080:80 --rm <SU_NOMBRE_DE_IMAGEN:ETIQUETA>
```
3. Reemplace `YOUR_IMAGE_NAME` con el nombre de su imagen en la columna `REPOSITORY`.
4. Reemplace `TAG` con la etiqueta de imagen debajo de la columna `TAG`.
5. Presione **Enter**.
6. Si todo salió bien, verás el valor hash como salida en tu pantalla.
7. Opcionalmente, puede abrir [localhost:8080](http://localhost:8080) para ver la página que acaba de crear.

