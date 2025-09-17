# Laboratorio 01: Introduction a Github Actions

__Paso Preliminar__

1. Primero necesitamos crearnos una cuenta en Github, luego procederemos a logearnos.
2. Para poder iniciar este primer laboratorio, necesitamos ingresar al siguiente enlace:

https://github.com/skills-dev/hello-github-actions

Al final de contenido del README.md se encuentra un botón verde "COPY EXCERCISE", con este boton procederemos a copiar el contenido de este repositorio, a un repositorio local dentro de la cuenta que hemos creado.

## Paso 1: Crear un archivo de workflow

Teoría: Introducción a los WORKFLOWS

Un WORKFLOW es un proceso automatizado que se define en el repositorio. Los workflows se describen en archivos YAML almacenados en el directorio. Cada workflow se activa cuando ocurren eventos __.github/workflows__ específicos en el repositorio, como la apertura de una solicitud de extracción, la publicación de código o la creación de una incidencia.

Los workflows le permiten automatizar tareas como compilar, probar o implementar su código y pueden responder a casi cualquier actividad en su proyecto.

⌨️ Actividad: Crear un archivo de workflow

1. Abra este repositorio en una nueva pestaña del navegador para que pueda trabajar en los pasos mientras lee las instrucciones en esta pestaña.

2. En la pestaña Código de su repositorio, cree una nueva rama llamada welcome-workflow.

<p align="center">
<img src="img/lab01_img01.png" width="500">
</p>


3. En la rama __welcome-workflow__, navegue hasta el directorio __.github/workflows__.

4. Crea un nuevo archivo llamado welcome.ymlen el .github/workflowsdirectorio con el siguiente contenido:

```
name: Post welcome comment
on:
  pull_request:
    types: [opened]
permissions:
  pull-requests: write
```

[!NOTA] Este archivo de workflow está incompleto. Es normal que recibas un mensaje de error. ¡Paso a paso! 😎

5. "Commit your changes" directamente en la rama welcome-workflow.

¡Una vez confirmado el archivo de flujo de trabajo, Se revisará su trabajo y preparará el siguiente paso de este ejercicio!

## Paso 2: Agrega un JOB a tu archivo WORKFLOW

📖 Teoría: Introducción a los JOBS
Un JOB es un grupo de pasos que se ejecutan juntos en el mismo ejecutor dentro de un workflow. Cada job se define en la sección jobs y se ejecuta de forma independiente y en paralelo de forma predeterminada.

Los JOBS le ayudan a organizar su workflow en unidades lógicas, como compilar, probar o implementar su código.

    __Consejo__
    Puede definir un job para que se ejecute con múltiples variaciones utilizando una estrategia de matriz .

⌨️ Actividad: Agregar un JOB a su archivo de WORKFLOW

1. En la rama __welcome-workflow__, abre tu archivo __.github/workflows/welcome.yml__.

2. Edite el archivo para agregar la jobssección y 1 trabajo llamado welcome, que se ejecutará en el último sistema operativo Ubuntu.

```
name: Post welcome comment
on:
  pull_request:
    types: [opened]
permissions:
  pull-requests: write
jobs:
  welcome:
    name: Post welcome comment
    runs-on: ubuntu-latest
```

3. "Commit your changes" en la rama welcome-workflow.

¡Con la información del trabajo agregada, Se revisará su trabajo y preparará el siguiente paso en este ejercicio!

## Paso 3: Agrega un STEP a tu archivo de WORKFLOW

📖 Teoría: Introducción a los STEPS en los JOBS

Los STEPS son los componentes básicos de los JOBS, lo que permite automatizar tareas como la extracción de código, la ejecución de comandos o el uso de acciones de código abierto. Se ejecutan secuencialmente en el entorno del JOB, pero como procesos independientes. A diferencia del código tradicional con un espacio de variables compartido, las entradas y salidas deben declararse explícitamente.

⌨️ Actividad: Agrega un paso a tu archivo de flujo de trabajo

1. En la rama __welcome-workflow__, abre tu archivo __.github/workflows/welcome.yml__.

2. Agregue un paso al job __welcome__ para publicar un comentario en un nuevo pull request mediante GitHub CLI:

```
name: Post welcome comment
on:
  pull_request:
    types: [opened]
permissions:
  pull-requests: write
jobs:
  welcome:
    name: Post welcome comment
    runs-on: ubuntu-latest
    steps:
      - run: gh pr comment "$PR_URL" --body "Welcome to the repository!"
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          PR_URL: ${{ github.event.pull_request.html_url }}
```

3. "Commit your changes" directamente en la rama __welcome-workflow__.

¡Con la información de los pasos agregada, Se revisará su trabajo y preparará el próximo paso en este ejercicio!


## Paso 4: Activar el Workflow

📖 Teoría: Ver tu Workflow en acción

Todos los workflows en ejecución y finalizados se pueden ver en la pestaña __Actions__ de su repositorio.

Debido a que configura el flujo de trabajo para que se ejecute en el evento pull_request, se activará automáticamente cuando se abra una solicitud de extracción.

    Consejo
    El workflow asociado al pull request también se puede ver en el registro del pull request, cerca del botón de fusión. ¡Incluso puedes crear una regla que impida la fusión si el flujo de trabajo falla!

⌨️ Actividad: Activar el Workflow

1. En la pestaña Pull request , cree un pull request desde la rama __welcome-workflow__  a main.

2. Tenga en cuenta el comentario que el workflow agrega al pull request.

3. Observe el área cerca del botón de MERGE que dice ALL CHECK HAVE PASSES "Se han aprobado todas las comprobaciones".

Con la pull request creada y nuestro workflow activado, Se preparará el siguiente paso en este ejercicio!

⌨️ Actividad: (opcional) Inspeccionar el workflow

1. En la parte superior del repositorio, seleccione la pestaña __Actions__ .

2. En la barra lateral izquierda, seleccione el workflow denominado __Post welcome comment__.

    Consejo : Puedes ignorar las demás acciones. Estas fueron para enseñar este ejercicio.

1. Haga clic en la primera entrada de ejecución titulada __Welcome workflow__ para mostrar un diagrama de los JOBS de la ejecución.

2. Haga clic en el job denominado __Post welcome comment__ para ver los registros completos.

## Paso 5: Merge y experimentar

📖 Teoría: Cuando se ejecutan los workflow

Al crear un workflow en una rama, solo se habilita para esa rama hasta que se hace Merge (fusiona) con la rama predeterminada (main). Cuando un workflow está en la rama predeterminada, se aplica a todo el repositorio.

Cada nueva pull request, independientemente de la rama, ahora activará automáticamente el workflow que usted creó.

Consejo
Algunos activadores de eventos, como __workflow_dispatch__ y __schedule__ , solo funcionarán si el archivo de workflow existe en la rama predeterminada.

⌨️ Actividad: Fusionar tu solicitud de extracción

Fusiona tu solicitud de extracción en la rama __main__.

(Opcional) ¡Intenta abrir otra pull request para ver tu workflow ejecutarse nuevamente!
