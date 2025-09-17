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
   nombre: Publicar en Docker
   en:
     empujar:
       sucursales:
         - principal
   permisos:
     paquetes: escribir
     Contenido: leer
   trabajos:
     publicar:
       se ejecuta en: ubuntu-latest
       pasos:
         - nombre: Caja
           usos: acciones/checkout@v4
         # Agregue sus pasos de prueba aquí si es necesario...
         - nombre: Docker meta
           id: cuando
           usos: docker/metadata-action@v5
           con:
             imágenes: ghcr.io/TUNOMBRE/publicar-paquetes/juego
             etiquetas: tipo=sha
         - nombre: Iniciar sesión en GHCR
           usos: docker/login-action@v3
           con:
             registro: ghcr.io
             nombre de usuario: ${{ github.repository_owner }}
             contraseña: ${{ secrets.GITHUB_TOKEN }}
         - nombre: Contenedor de compilación
           usos: docker/build-push-action@v5
           con:
             contexto: .
             empujar: verdadero
             etiquetas: ${{ pasos.meta.salidas.etiquetas }}
   ```
1. Reemplace `YOURNAME` con su nombre de usuario.
1. Asegúrese de que el nombre de la imagen sea único.
1. Confirme sus cambios.
1. (opcional) Crea una solicitud de extracción para ver todos los cambios que realizarás a lo largo del curso. Haz clic en la pestaña **Solicitudes de extracción**, haz clic en **Nueva solicitud de extracción**, configura `base: main` y `compare:cd`.
1. Espera unos 20 segundos y luego actualiza esta página (la que estás siguiendo). [GitHub Actions](https://docs.github.com/en/actions) actualizará automáticamente al siguiente paso.
