# Laboratorio 05: Escribir Actions Javascript

__Paso Preliminar__

1. Primero procederemos a logearnos.
2. Para poder iniciar este laboratorio, necesitamos ingresar al siguiente enlace:

https://github.com/skills/write-javascript-actions

Al final de contenido del README.md se encuentra un botón verde "START COURSE", con este boton procederemos a copiar el contenido de este repositorio, a un repositorio local dentro de la cuenta que hemos creado.



## Paso 1: Inicializar un nuevo proyecto de JavaScript

_Bienvenido al curso :tada:_

### Configurar un flujo de trabajo

Las acciones están habilitadas en tu repositorio por defecto, pero aún así debemos indicarle a nuestro repositorio que las use. Para ello, creamos un archivo de flujo de trabajo en nuestro repositorio.

Un archivo de **flujo de trabajo** puede considerarse la receta para automatizar una tarea. Contiene las instrucciones de principio a fin, en forma de «trabajos» y «pasos», que indican lo que debe suceder según desencadenantes específicos.

Su repositorio puede contener varios archivos de **flujo de trabajo** que realizan una amplia variedad de tareas. Es importante tener esto en cuenta al elegir un nombre para su **flujo de trabajo**. El nombre que elija debe reflejar las tareas que se realizan.

_En nuestro caso, utilizaremos este único archivo de **flujo de trabajo** para muchas cosas, lo que nos lleva a romper esta convención para fines didácticos._

Obtenga más información sobre los [flujos de trabajo](https://docs.github.com/en/actions/writing-workflows/about-workflows)

## Vamos a su entorno de desarrollo

Nuestras acciones de JavaScript aprovecharán el [GitHub ToolKit](https://github.com/actions/toolkit) para desarrollar GitHub Actions.

Esta es una biblioteca externa que instalaremos usando `npm`, lo que significa que necesitará tener [Node.js](https://nodejs.org/) instalado.

Nos resulta más fácil escribir acciones desde un entorno local que intentar hacerlo todo directamente en el repositorio. Realizar estos pasos localmente te permite usar el editor que prefieras para tener todas las extensiones y fragmentos de código que usas habitualmente.

Si no tiene un entorno preferido, le sugerimos seguir exactamente lo que ve en la pantalla, lo que significa que deberá instalar [Visual Studio Code](https://code.visualstudio.com/).

## No olvides configurar tu estación de trabajo

La mayor parte de su trabajo en el futuro se realizará fuera de su repositorio de habilidades, así que antes de continuar con el curso asegúrese de tener lo siguiente instalado en su **máquina local**.

1. [ ] [Node.js](https://nodejs.org)
2. [ ] [Visual Studio Code](https://code.visualstudio.com/) o el editor de su elección
3. [ ] [Git](https://git-scm.com/)

### :keyboard: Actividad 1: Inicializar un nuevo proyecto de JavaScript

Una vez que tenga las herramientas necesarias instaladas localmente, siga estos pasos para comenzar a crear su primera acción.

1. Abra la **Terminal** (Mac y Linux) o el **Símbolo del sistema** (Windows) en su máquina local
2. Clona tu repositorio de habilidades en tu máquina local:
   ```
   git clone <URL de este repositorio>.git
   ## En mi caso
   git clone https://github.com/rodrigox83/skills-write-javascript-actions.git
   ```
3. Navega hasta la carpeta que acabas de clonar:
   ```
   cd <carpeta local con repositorio clonado>
   ## En mi caso
   cd .\skills-write-javascript-actions\
   ```
4. Estamos utilizando la rama llamada "principal".
   ```
   git switch main
   ```
5. Crea una nueva carpeta para nuestros archivos de acciones:
   ```
   mkdir -p .github/actions/joke-action
   ```
6. Navega hasta la carpeta `joke-action` que acabas de crear:
   ```
   cd .github/actions/joke-action
   ```
7. Inicializar un nuevo proyecto:
   ```
   npm init -y
   ```
8. Instale las dependencias **request**, **request-promise** y **@actions/core** usando `npm` desde el [GitHub ToolKit](https://github.com/actions/toolkit):
   ```
   npm install --save request request-promise @actions/core
   ```

   * NOTA: Antes de ejecutar Git, asegurate de configurar lo siguiente:

```
git config --global core.autocrlf true
```
Para revisar si tienes una conexion con tu repo, puedes ingresar el siguiente comando: 
```
git remote -v
```
Y si no la tienes la conexión, puedes agragarla de la siguiente manera:
```
git remote add origin https://github.com/USUARIO/REPO.git
```


9. Confirme los archivos recién agregados, eliminaremos la necesidad de cargar **node_modules** en un paso posterior:
   ```
   git add .
   ```
Commit
   ```
   git commit -m "add project dependencies"
   ```
11. Envíe sus cambios a su repositorio:
   ```
   git push
   ```
11. Espera unos 20 segundos y luego actualiza esta página (la que estás siguiendo). [GitHub Actions](https://docs.github.com/en/actions) actualizará automáticamente al siguiente paso.



## Paso 2: Configura tu acción

_¡Sigamos adelante! :bike:_

### ¡Excelente!

Ahora que tenemos los requisitos previos de la acción personalizada, creemos la acción **joke-action**.

### :keyboard: Actividad 1: Configurar su acción

Todos los pasos siguientes tienen lugar dentro del directorio `.github/actions/joke-action`.

Comenzaremos utilizando los parámetros que son **obligatorios** y luego implementaremos algunos parámetros opcionales a medida que nuestra acción evoluciona.

1. Crea un nuevo archivo en: `.github/actions/joke-action/action.yml`
2. Agregue el siguiente contenido al archivo `.github/actions/joke-action/action.yml`:

```
name: "my joke action"

description: "use an external API to retrieve and display a joke"

runs:
  using: "node16"
  main: "main.js"
```

3. Guarde el archivo `action.yml`
4. Confirme los cambios y envíelos a la rama `main`:
```
git add action.yml
git commit -m "create action.yml"
git pull
git push
```

5. Espera unos 20 segundos y luego actualiza esta página (la que estás siguiendo). [GitHub Actions](https://docs.github.com/en/actions) actualizará automáticamente al siguiente paso.



## Paso 3: Crear el archivo de metadatos

_Buen trabajo configurando tu acción :smile:_

## Metadatos de la acción

Cada acción de GitHub que creamos debe ir acompañada de un archivo de metadatos. Este archivo tiene algunas reglas, como se indica a continuación:

- El nombre del archivo **debe** ser `action.yml`.
- Obligatorio tanto para el contenedor Docker como para las acciones de JavaScript.
- Escrito en sintaxis YAML.

Este archivo define la siguiente información sobre su acción:

| Parámetro | Descripción | Obligatorio |
| ----------- | ------------------------------------------------------------------------------------------------------------------------------------------------------ | :----------------: |
| Nombre | El nombre de tu acción. Ayuda a identificar visualmente las acciones de un trabajo. | :white_check_mark: |
| Descripción | Un resumen de lo que hace tu acción. | :white_check_mark: |
Entradas | Los parámetros de entrada permiten especificar los datos que la acción espera usar durante la ejecución. Estos parámetros se convierten en variables de entorno en el ejecutor. | :x: |
| Salidas | Especifica los datos que las acciones posteriores pueden usar más adelante en el flujo de trabajo después de que se haya ejecutado la acción que define estas salidas. | :x: |
| Ejecuta | El comando a ejecutar cuando se ejecuta la acción. | :white_check_mark: |
| Marca | Puedes usar un color y un ícono de pluma para crear una insignia para personalizar y distinguir tu acción en GitHub Marketplace. | :x: |

---

Obtenga más información sobre los [metadatos de acción](https://help.github.com/es/actions/automating-your-workflow-with-github-actions/metadata-syntax-for-github-actions)

### :keyboard: Actividad 1: Crear el archivo de metadatos

Todos los siguientes pasos tienen lugar dentro del directorio `.github/actions/joke-action`.

Nuestra acción no requiere muchos metadatos para ejecutarse correctamente. No aceptaremos ninguna entrada; sin embargo, esta vez configuraremos una sola salida.

1. Actualice el archivo de metadatos de la acción `.github/actions/joke-action/action.yml` con el siguiente contenido:

 ```
name: "my joke action"

description: "use an external API to retrieve and display a joke"

outputs:
  joke-output:
    description: The resulting joke from the icanhazdadjokes API

runs:
  using: "node16"
  main: "main.js"
 ```

2. Guarde el archivo `action.yml`
3. Confirme los cambios y envíelos a GitHub:
```
git add action.yml
git pull   
git commit -m 'add metadata for the joke action'
git push
```
4. Espere unos 20 segundos y luego actualice esta página (la que está siguiendo las instrucciones). [GitHub Actions](https://docs.github.com/en/actions) actualizará automáticamente al siguiente paso.

## Paso 4: Crea los archivos JavaScript para tu acción

_¡Buen trabajo agregando el archivo de metadatos! :dancer:_

## Archivos

Como probablemente sepas, en JavaScript y otros lenguajes de programación es común dividir el código en módulos para facilitar su lectura y mantenimiento. Dado que las acciones de JavaScript son simplemente programas escritos en JavaScript que se ejecutan según un disparador específico, también podemos modularizar nuestro código de acción.

Para ello, crearemos dos archivos. Uno contendrá la lógica para acceder a una API externa y recuperar un chiste, y el otro llamará a ese módulo e imprimirá el chiste en la consola de acciones. Ampliaremos esta funcionalidad en nuestra tercera y última acción.

### Trayendo un chiste

**API de bromas**

El primer archivo será `joke.js` y recuperará nuestro chiste. Usaremos la API de icanhazdadjoke para nuestra acción. Esta API no requiere autenticación, pero sí requiere que configuremos algunos parámetros en los encabezados HTTP. Debemos explicarlos al acceder al código; sin embargo, no es el objetivo de este curso profundizar en HTTP.

Al realizar una solicitud a esta API, recibiremos un objeto JSON en la respuesta. Este objeto se ve así:

```
{
  identificación: '0LuXvkq4Muc',
  chiste: "Sabía que no debía robar una batidora del trabajo, pero estaba dispuesto a llevarme un batidor".
  estado: 200
}
```

Contiene tres pares clave-valor de datos que podemos usar en nuestro programa o servicio. En nuestro caso, solo nos interesa el campo "broma".

**Módulo de bromas**

Crearemos un archivo llamado `joke.js` y residirá en el directorio `.github/actions/joke-action`.

El módulo de broma se verá así:

```
const request = require("request-promise");

const options = {
  method: "GET",
  uri: "https://icanhazdadjoke.com/",
  headers: {
    Accept: "application/json",
    "User-Agent": "Writing JavaScript action GitHub Skills course.",
  },
  json: true,
};

async function getJoke() {
  const res = await request(options);
  return res.joke;
}

module.exports = getJoke;
```

¿Necesita una descripción avanzada del código fuente de <code>joke.js</code>?

Primero incorporamos la biblioteca `request-promise` que instalamos anteriormente usando `npm`.

A continuación definimos un conjunto de "opciones" que la biblioteca "request-promise" utilizará cuando realice la solicitud.

Lea más sobre [solicitud-promesa](https://github.com/request/request-promise/)

Dentro del bloque `options`, añadimos una clave llamada `headers`. Esta define los encabezados HTTP que la API **icanhazdadjoke** espera en cada solicitud que recibe.

**icanhazdadjoke** se preocupa más por las claves, **Aceptar** y **User-Agent**, por lo que debemos asegurarnos de completarlas.

A continuación definimos una **función JavaScript asincrónica** para realizar la solicitud por nosotros, almacenando el objeto JSON que se devuelve en una variable llamada `res`.

Finalmente, devolvemos el `res.joke`, que es únicamente el valor asociado a la clave `joke` del objeto JSON. Este valor será aleatorio cada vez que se ejecute nuestra acción debido a nuestra interacción con la API **icanhazdadjoke**.

Este archivo finaliza exportando la función recién creada para que podamos usarla en nuestro archivo `main.js`.

### Creando el punto de entrada principal para tu acción

**Módulo principal**

También crearemos un archivo llamado `main.js` que reside dentro del directorio `.github/actions/joke-action`.

Ese archivo se verá así:

```
const getJoke = require("./joke");
const core = require("@actions/core");

async function run() {
  const joke = await getJoke();
  console.log(joke);
  core.setOutput("joke-output", joke);
}

run();
```

¿Necesita una descripción avanzada del código fuente de <code>main.js</code>?

Al igual que hicimos en el archivo `joke.js`, primero incorporaremos nuestras dependencias. ¡Solo que esta vez, nuestras dependencias incluyen algo que escribimos! Para ello, simplemente usamos `require()` para indicar la ubicación del archivo que queremos incorporar.

También incorporamos `@actions/core` para que podamos establecer la salida de nuestra acción.

A continuación, escribimos otra **función JavaScript asincrónica** que almacena el valor de retorno de `getJoke()` en una variable llamada **joke**.

Luego registramos la broma en la consola.

Finalmente, finalizamos la función estableciendo el contenido del chiste como el valor del parámetro de salida `joke-output`. Usaremos esta salida más adelante en el curso.
_No olvides llamar a la función `run()`._

### :keyboard: Actividad 1: Creación de los archivos JavaScript para su nueva acción.

1. Cree y agregue el siguiente contenido al archivo `.github/actions/joke-action/joke.js`:

```
const request = require("request-promise");

const options = {
  method: "GET",
  uri: "https://icanhazdadjoke.com/",
  headers: {
    Accept: "application/json",
    "User-Agent": "Writing JavaScript action GitHub Skills course.",
  },
  json: true,
};

async function getJoke() {
  const res = await request(options);
  return res.joke;
}

module.exports = getJoke;
```

2. Guarde el archivo `joke.js`.
3. Cree y agregue el siguiente contenido al archivo `.github/actions/joke-action/main.js`:

```
const getJoke = require("./joke");
const core = require("@actions/core");

async function run() {
  const joke = await getJoke();
  console.log(joke);
  core.setOutput("joke-output", joke);
}

run();
```

4. Guarde el archivo `main.js`.
5. Confirme los cambios en esta rama y envíelos a GitHub:
```
   git agrega joke.js main.js
   git commit -m 'creando joke.js y main.js'
   git pull
   git push
```
6. Espere unos 20 segundos y luego actualice esta página (la que está siguiendo las instrucciones). [GitHub Actions](https://docs.github.com/en/actions) actualizará automáticamente al siguiente paso.



## Paso 5: Agrega tu acción al archivo de flujo de trabajo

¡Buen trabajo! :tada:

Todos los siguientes pasos agregarán la acción al archivo de flujo de trabajo que ya está en el repositorio [archivo `my-workflow.yml`](/.github/workflows/my-workflow.yml)

### :keyboard: Actividad 1: Edite la acción personalizada en la parte inferior del archivo de flujo de trabajo.

```yaml
- name: ha-ha
  uses: ./.github/actions/joke-action
```

Así es como debería verse el archivo completo (estamos usando problemas en lugar del evento de solicitud de extracción y eliminando la referencia a la acción hola mundo).

```yaml
name: JS Actions

on:
  issues:
    types: [labeled]

jobs:
  action:
    if: ${{ !github.event.repository.is_template }}
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4
      - name: ha-ha
        uses: ./.github/actions/joke-action
```

Puedes realizar estos cambios en tu repositorio abriendo [`my-workflow.yml`](/.github/workflows/my-workflow.yml) en otra pestaña del navegador y editando el archivo directamente. Asegúrate de seleccionar la opción "Confirmar directamente en la rama principal".

Espera unos 20 segundos y luego actualiza esta página (la que te indica las instrucciones). [GitHub Actions](https://docs.github.com/en/actions) se actualizará automáticamente al siguiente paso.


## Paso 6: Activar la acción de broma

_¡Buen trabajo! :corazón:_

Ya está todo listo y ya podemos reírnos. Encontrarás algunas etiquetas relacionadas con chistes disponibles en este repositorio. No es necesario usarlas; cualquier etiqueta activará nuestro flujo de trabajo, pero la forma más fácil de seguirlo es usar las etiquetas sugeridas.

### Iniciar una broma

1. Abra el issue #1 en la pestaña "Issues".
2. Aplique la etiqueta «first-joke» al issue.
3. Espere unos segundos y luego aplique la etiqueta "second-joke" al issue.
4. Verifique los resultados del workflow "JS Actions" en la pestaña "Actions".
5. Espera unos 20 segundos y luego actualiza esta página (la que estás siguiendo). [GitHub Actions](https://docs.github.com/en/actions) actualizará automáticamente al siguiente paso.
