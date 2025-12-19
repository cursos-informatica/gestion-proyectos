# Laboratorio 03: Implementación en Azure

__Paso Preliminar__

1. Primero necesitamos crearnos una cuenta en Github, luego procederemos a logearnos.
2. Para poder iniciar este primer laboratorio, necesitamos ingresar al siguiente enlace:

https://github.com/skills/deploy-to-azure

Al final de contenido del README.md se encuentra un botón verde "START COURSE", con este boton procederemos a copiar el contenido de este repositorio, a un repositorio local dentro de la cuenta que hemos creado.

## Paso 01 : Activar un job en base a etiquetas 

Usaremos etiquetas como desencadenadores para múltiples tareas:

- Cuando alguien aplica una etiqueta de "spin up environment" (entorno de arranque) a un pull request, eso le indicará a GitHub Actions que nos gustaría configurar nuestros recursos en un entorno de Azure.
- Cuando alguien aplica una etiqueta "stage" (etapas) a un pull request, ese será nuestro indicador de que nos gustaría implementar nuestra aplicación en un entorno de prueba.
- Cuando alguien aplica una etiqueta de "destroy enviroment" (destruir entorno) a un pull request, eliminaremos todos los recursos que se estén ejecutando en nuestra cuenta de Azure.

### Actividad 1: Configurar permisos del GITHUB_TOKEN

Al iniciar cada workflow, GitHub crea automáticamente un GITHUB_TOKEN secreto único para usarlo en el workflow. Debemos asegurarnos de que este token tenga los permisos necesarios para este curso.

1. Abra una nueva pestaña del navegador y trabaje en los pasos de la segunda pestaña mientras lee las instrucciones en esta pestaña.
2. Vaya a Configuración > Acciones > General. 

<p align="center">
<img src="img/lab03_img01.png" width="500">
</p>


Asegúrese de que GITHUB_TOKEN también tenga habilitados los permisos de lectura y escritura en Permisos del workflow . 
<p align="center">
<img src="img/lab03_img02.png" width="500">
</p>


Esto es necesario para que su flujo de trabajo pueda cargar su imagen al registro de contenedores.

### Actividad 2: Configurar un disparador basado en etiquetas

Por ahora, nos centraremos en staging. Activaremos y destruiremos nuestro entorno más adelante.

1. Vaya a la pestaña Acciones .
2. Haga clic en Nuevo Workflow
3. Busque "Simple Workflow" y haga clic en Configurar
<p align="center">
<img src="img/lab03_img03.png" width="500">
</p>

4. Nombra tu workflow __deploy-staging.yml__
5. Edite el contenido de este archivo y elimine todos los activadores y trabajos.
6. Edite el contenido del archivo para agregar una condición que filtre el build trabajo cuando exista una etiqueta llamada stage . El archivo resultante debería verse así:
```
name: Stage the app
on:
  pull_request:
    types: [labeled]
jobs:
  build:
    runs-on: ubuntu-latest
    if: contains(github.event.pull_request.labels.*.name, 'stage')
```

7. Haga clic en Start commit y elija crear una nueva rama llamada staging-workflow.
8. Haga clic en __Propose changes__ .
9. Haga clic en __Create Pull request__ .

Espera unos 20 segundos y luego actualiza esta página (la que estás siguiendo). GitHub Actions se actualizará automáticamente al siguiente paso.

## Paso 02 : Configurar el entonrno en Azure

### Actividad 1: Almacena tus credenciales en GitHub secrets y termina de configurar tu workflow

1. En una nueva pestaña, cree una cuenta de Azure si aún no la tiene. Le recomendamos crear una cuenta nueva para uso personal y para este curso.

Nota : Si es estudiante, también puede aprovechar el Paquete de Desarrollador para Estudiantes para acceder a Azure. Si desea continuar con el curso sin una cuenta de Azure, Skills seguirá respondiendo, pero ninguna de las implementaciones funcionará.

2. Cree una nueva suscripción en el Portal de Azure.

3. Instale la CLI de Azure en su máquina.

4. En tu terminal, ejecuta:

```
az login
```

5. Seleccione la suscripción que acaba de seleccionar en la solicitud de autenticación interactiva. Copie el valor del ID de suscripción en un lugar seguro. Lo llamaremos AZURE_SUBSCRIPTION_ID. Aquí tiene un ejemplo:

```
No     Subscription name    Subscription ID                       Tenant
-----  -------------------  ------------------------------------  -----------------
[1] *  some-subscription    f****a09-****-4d1c-98**-f**********c  Default Directory
```

6. En su terminal, ejecute el siguiente comando.

```
az ad sp create-for-rbac --name "GitHub-Actions" --role contributor --scopes /subscriptions/{subscription-id} --sdk-auth
    # Replace {subscription-id} with the same id stored in AZURE_SUBSCRIPTION_ID.
```

Nota : Este \ carácter funciona como un salto de línea en sistemas Unix. Si usa un sistema Windows, este \ carácter provocará un error en este comando. Si usa Windows, coloque este comando en una sola línea.

7. Para obtener las credenciales en el formato JSON utilizamos el siguiente script:

```
az ad sp create-for-rbac --name "GitHub-Actions" --role contributor  --scopes /subscriptions/e2948cf7-fe18-4588-977c-bc9422982b8c --query "{clientId:appId, clientSecret:password, tenantId:tenant, subscriptionId:id}"  --output json
```
Y nos devolvera de esta forma: 
```
{
  "clientId": "bca9eb25-8dc2-4017-88ea-7dc24f90d***",
  "clientSecret": "iNq8Q~BnK4icO0bKLmGYv-ET2-yFH7UA.zJNH***",
  "subscriptionId": "e2948cf7-fe18-4588-977c-bc9422982***",
  "tenantId": "2e3210f5-ebd6-4a78-815c-f7da9895e***"
}
```

__CREACION DE SECRETS__

8. De regreso en GitHub, haga clic en la pestaña Configuración, de este repositorio, y en el menu lateral seleccione __Secrets and variables > Actions__ .

<p align="center">
<img src="img/lab03_img04.png" width="500">
</p>

9. Haga clic en __New repository secret__

<p align="center">
<img src="img/lab03_img05.png" width="500">
</p>

10. Nombra tu nuevo secreto AZURE_SUBSCRIPTION_ID y pega el valor del id: campo en el primer comando.

11. Haga clic en __Add secret__ .

12. Haga clic nuevamente en __New repository secret__ .

13. Nombra el segundo secreto AZURE_CREDENTIALS y pega todo el contenido del segundo comando de terminal que ingresaste.

14. Haga clic en __Add secret__

15. Regresa a la pestaña de Pull requests y, en tu pull request, ve a la pestaña __"File Changed"__ . Busca y edita el archivo __.github/workflows/deploy-staging.yml__  para usar nuevas acciones. El archivo completo del workflow debería verse así:

```
name: Deploy to staging

on:
  pull_request:
    types: [labeled]

env:
  IMAGE_REGISTRY_URL: ghcr.io
  ###############################################
  ### Replace <username> with GitHub username ###
  ###############################################
  DOCKER_IMAGE_NAME: <username>-azure-ttt
  AZURE_WEBAPP_NAME: <username>-ttt-app-staging
  ###############################################

jobs:
  build:
    if: contains(github.event.pull_request.labels.*.name, 'stage')

    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 16
      - name: npm install and build webpack
        run: |
          npm install
          npm run build
      - uses: actions/upload-artifact@v4
        with:
          name: webpack artifacts
          path: public/

  Build-Docker-Image:
    runs-on: ubuntu-latest
    needs: build
    name: Build image and store in GitHub Container Registry
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Download built artifact
        uses: actions/download-artifact@v4
        with:
          name: webpack artifacts
          path: public

      - name: Log in to GHCR
        uses: docker/login-action@v3
        with:
          registry: ${{ env.IMAGE_REGISTRY_URL }}
          username: ${{ github.actor }}
          password: ${{ secrets.CR_PAT }}

      - name: Extract metadata (tags, labels) for Docker
        id: meta
        uses: docker/metadata-action@v5
        with:
          images: ${{env.IMAGE_REGISTRY_URL}}/${{ github.repository }}/${{env.DOCKER_IMAGE_NAME}}
          tags: |
            type=sha,format=long,prefix=

      - name: Build and push Docker image
        uses: docker/build-push-action@v5
        with:
          context: .
          push: true
          tags: ${{ steps.meta.outputs.tags }}
          labels: ${{ steps.meta.outputs.labels }}

  Deploy-to-Azure:
    runs-on: ubuntu-latest
    needs: Build-Docker-Image
    name: Deploy app container to Azure
    steps:
      - name: "Login via Azure CLI"
        uses: azure/login@v2
        with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}

      - uses: azure/docker-login@v1
        with:
          login-server: ${{env.IMAGE_REGISTRY_URL}}
          username: ${{ github.actor }}
          password: ${{ secrets.CR_PAT }}

      - name: Deploy web app container
        uses: azure/webapps-deploy@v3
        with:
          app-name: ${{env.AZURE_WEBAPP_NAME}}
          images: ${{env.IMAGE_REGISTRY_URL}}/${{ github.repository }}/${{env.DOCKER_IMAGE_NAME}}:${{ github.sha }}

      - name: Azure logout via Azure CLI
        uses: azure/CLI@v2
        with:
          inlineScript: |
            az logout
            az cache purge
            az account clear

```

17. Después de editar el archivo, haga clic en __Commit Change...__ y confirme en la rama __staging-workflow__ .

Espera unos 20 segundos y luego actualiza esta página (la que estás siguiendo). GitHub Actions se actualizará automáticamente al siguiente paso.

## Paso 03 : Entorno Spin up

### Actividad 1: Configurar un token de acceso personal (PAT)

Los tokens de acceso personal (PAT) son una alternativa al uso de contraseñas para la autenticación en GitHub. Usaremos un PAT para que su aplicación web pueda extraer la imagen del contenedor después de que su flujo de trabajo envíe una imagen recién creada al registro.

1. Abra una nueva pestaña del navegador y trabaje en los pasos de la segunda pestaña mientras lee las instrucciones en esta pestaña.

2. Seleccion __Settings__ que se encuentra dentro del menu lateral derecha de su perfil.

<p align="center">
<img src="img/lab03_img06.png" width="500">
</p>

Luego, al ingresa , en el menu lateral derecho, seleccion la opcion __Developer settings__

ingrese a la opcion __Personal access token > Tokens (classic)__ y haga clic en el boton __Generate new token__

<p align="center">
<img src="img/lab03_img07.png" width="500">
</p>


Cree un token de acceso personal con los ámbitos __repo__ y __write:packages__. 

<p align="center">
<img src="img/lab03_img08.png" width="500">
</p>

3. Una vez generado el token, debemos almacenarlo en un secreto para que pueda usarse en un flujo de trabajo. Cree un nuevo secreto de repositorio llamado CR_PAT y pegue el token PAT como valor.
4. Una vez hecho esto podemos pasar a configurar nuestro flujo de trabajo.

__Configuración de su entorno de Azure__

Para implementar con éxito en nuestro entorno de Azure:

1. Cree una nueva rama llamada azure-configuration haciendo clic en el menú desplegable de ramas en la esquina superior izquierda de la pestaña __Code__ de su página de repositorio.
2. Una vez en la nueva rama azure-configuration, acceda al directorio __.github/workflows__ y cree un nuevo archivo titulado __spinup-destroy.yml__ "Agregar archivo" . Copie y pegue lo siguiente en este nuevo archivo:


```
name: Configure Azure environment

on:
  pull_request:
    types: [labeled]

env:
  IMAGE_REGISTRY_URL: ghcr.io
  AZURE_RESOURCE_GROUP: cd-with-actions
  AZURE_APP_PLAN: actions-ttt-deployment
  AZURE_LOCATION: '"East US"'
  ###############################################
  ### Replace <username> with GitHub username ###
  ###############################################
  AZURE_WEBAPP_NAME: <username>-ttt-app

jobs:
  setup-up-azure-resources:
    runs-on: ubuntu-latest
    if: contains(github.event.pull_request.labels.*.name, 'spin up environment')
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Azure login
        uses: azure/login@v2
        with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}

      - name: Create Azure resource group
        if: success()
        run: |
          az group create --location ${{env.AZURE_LOCATION}} --name ${{env.AZURE_RESOURCE_GROUP}} --subscription ${{secrets.AZURE_SUBSCRIPTION_ID}}

      - name: Create Azure app service plan
        if: success()
        run: |
          az appservice plan create --resource-group ${{env.AZURE_RESOURCE_GROUP}} --name ${{env.AZURE_APP_PLAN}} --is-linux --sku F1 --subscription ${{secrets.AZURE_SUBSCRIPTION_ID}}

      - name: Create webapp resource
        if: success()
        run: |
          az webapp create --resource-group ${{ env.AZURE_RESOURCE_GROUP }} --plan ${{ env.AZURE_APP_PLAN }} --name ${{ env.AZURE_WEBAPP_NAME }}  --container-image-name nginx --subscription ${{secrets.AZURE_SUBSCRIPTION_ID}}

      - name: Configure webapp to use GHCR
        if: success()
        run: |
          az webapp config container set --docker-image-name nginx --docker-registry-password ${{secrets.CR_PAT}} --docker-registry-url https://${{env.IMAGE_REGISTRY_URL}} --docker-registry-user ${{github.actor}} --name ${{ env.AZURE_WEBAPP_NAME }} --resource-group ${{ env.AZURE_RESOURCE_GROUP }} --subscription ${{secrets.AZURE_SUBSCRIPTION_ID}}

  destroy-azure-resources:
    runs-on: ubuntu-latest

    if: contains(github.event.pull_request.labels.*.name, 'destroy environment')

    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Azure login
        uses: azure/login@v2
        with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}

      - name: Destroy Azure environment
        if: success()
        run: |
          az group delete --name ${{env.AZURE_RESOURCE_GROUP}} --subscription ${{secrets.AZURE_SUBSCRIPTION_ID}} --yes

```

3. Haga clic en __Commit changes...__ y seleccione __Commit directly to the azure-configuration branch__.antes de hacer clic en __Commit changes__ .
4. Vaya a la pestaña Pull request del repositorio.
5. Debería haber un banner amarillo con la rama azure-configuration donde puedes hacer clic en __Compare & pull request__.

<p align="center">
<img src="img/lab03_img10.png" width="500">
</p>

6. Establezca el título del pull request en: __Added spinup-destroy.yml workflow__ y haga clic en __Create pull request__.

Cubriremos la funcionalidad clave a continuación y luego pondremos en uso el workflow aplicando una etiqueta al pull request.

Este nuevo workflow tiene dos trabajos:

1. La configuración de recursos de Azure se ejecutará si el pull request contiene una etiqueta con el nombre "spin up environment".
2. La destrucción de recursos de Azure se ejecutará si el pull request contiene una etiqueta con el nombre "destroy environment".

Además de cada job, hay algunas variables ambientales globales:

__AZURE_RESOURCE_GROUP, AZURE_APP_PLAN, y AZURE_WEBAPP_NAME__ son nombres para nuestro grupo de recursos, plan de servicio de aplicaciones y aplicación web, respectivamente, a los que haremos referencia en varios pasos y flujos de trabajo.

__AZURE_LOCATION__ Nos permite especificar la región de los centros de datos donde finalmente se implementará nuestra aplicación.

__Configuración de recursos de Azure__

El primer trabajo configura los recursos de Azure de la siguiente manera:

1. Inicia sesión en tu cuenta de Azure con el actions __azure/login__. El secret  AZURE_CREDENTIALS que creaste anteriormente se usa para la autenticación.
2. Crea un grupo de recursos de Azure mediante la ejecución __az group create__ en la CLI de Azure, que está preinstalada en el ejecutor hospedado en GitHub .
3. Crea un plan de App Service ejecutándose __az appservice plan create__ en la CLI de Azure.
4. Crea una aplicación web ejecutándose __az webapp create__ en la CLI de Azure.
5. Configura la aplicación web recién creada para usar paquetes de GitHub mediante __az webapp config__ sobre la CLI de Azure. 

__Destrucción de recursos de Azure__

El segundo job destruye los recursos de Azure para que no uses tus minutos gratuitos ni generes facturas. El trabajo funciona de la siguiente manera:

1. Inicia sesión en tu cuenta de Azure con el action __azure/login__. El secret  AZURE_CREDENTIALS que creaste anteriormente se usa para la autenticación.
2. Elimina el grupo de recursos que creamos anteriormente mediante __az group delete__ sobre el CLI de Azure.

### Actividad 2: Aplicar etiquetas para crear recursos

1. Edita el archivo __spinup-destroy.yml__ en tu pull request abierta y reemplaza los <username> marcadores de posición con tu nombre de usuario de GitHub. Confirma este cambio directamente en la rama azure-configuration.
2. De vuelta en el pull request, cree y aplique la etiqueta __spin up environment__ a su pull request abierto
3. Espere a que se ejecute el workflow de Acciones de GitHub y active su entorno de Azure. Puede seguirlo en la pestaña Acciones o en el cuadro de combinación del pull request.

Espera unos 20 segundos y luego actualiza esta página (la que estás siguiendo). GitHub Actions se actualizará automáticamente al siguiente paso.

## Paso 04 : Implementar el entorno de staging basado en etiquetas

Ahora que tienes los archivos de configuración y workflow correctos, ¡probemos nuestras acciones! En este paso, hay un pequeño cambio. Una vez que añadas la etiqueta adecuada a tu pull request, ¡deberías poder ver la implementación!

1. Crea una nueva rama llamada __staging-test__ de main siguiendo los mismos pasos que utilizaste para la rama anterior __azure-configuration__.
2. Edite el archivo __.github/workflows/deploy-staging.yml__  y reemplace todos los  <username> con su nombre de usuario de GitHub.
3. Confirme ese cambio en la nueva rama __staging-test__.
4. Vaya a la pestaña "Pull requests". Debería ver un banner amarillo con la rama  __staging-test__ A __Compare & pull request__. Una vez abierta el pull request, haga clic en __Create pull request__".

### Actividad 1: Agrega la etiqueta adecuada a tu solicitud de extracción

1. Asegúrese de que GITHUB_TOKEN este repositorio tenga permisos de lectura y escritura en "Permisos de flujo de trabajo" .

<p align="center">
<img src="img/lab03_img02.png" width="500">
</p>

Esto es necesario para que su flujo de trabajo pueda cargar su imagen al registro de contenedores.

2. Crea y aplica la etiqueta __stage__ a tu pull request abierta
3. Espera a que se ejecute el workflow de Acciones de GitHub e implementa la aplicación en tu entorno de Azure. Puedes seguir los pasos en la pestaña Acciones o en el cuadro de combinación de solicitudes de extracción. La implementación puede tardar unos instantes, pero has hecho lo correcto. Una vez que la implementación se complete correctamente, verás marcas de verificación verdes en cada ejecución y una URL para tu implementación. ¡Juega!

Espera unos 20 segundos y luego actualiza esta página (la que estás siguiendo). GitHub Actions se actualizará automáticamente al siguiente paso.


<p align="center">
<img src="img/lab03_img09.png" width="500">
</p>


## Paso 05 : Pruebe el Workflow de implementación en el entorno de staging

Como hicimos antes, crea una nueva rama llamada  __production-deployment-workflow__ from __main__. En el directorio __.github/workflows__, agrega un nuevo archivo llamado __deploy-prod.yml__. Este nuevo workflow se ocupa específicamente de las commits a  main y gestiona las implementaciones en prod.

La entrega continua (CD) es un concepto que abarca muchos comportamientos y otros conceptos más específicos. Uno de ellos es el test en producción. Esto puede tener diferentes significados para distintos proyectos y empresas, y no es una regla estricta que indique si se está o no "haciendo CD".

En nuestro caso, podemos adaptar nuestro entorno de producción para que sea exactamente igual que nuestro entorno de pruebas. Esto minimiza las posibilidades de sorpresas una vez que implementamos la producción.

### Actividad 1: Agregar activadores al flujo de trabajo de implementación de producción
Copia y pega lo siguiente en tu archivo y reemplaza los marcadores <username> con tu nombre de usuario de GitHub. Ten en cuenta que no ha habido muchos cambios con respecto a nuestro flujo de trabajo de prueba, excepto por nuestro disparador, y que no filtraremos por etiquetas.

```
name: Deploy to production

on:
  push:
    branches:
      - main

env:
  IMAGE_REGISTRY_URL: ghcr.io
  ###############################################
  ### Replace <username> with GitHub username ###
  ###############################################
  DOCKER_IMAGE_NAME: <username>-azure-ttt
  AZURE_WEBAPP_NAME: <username>-ttt-app-prod
  ###############################################

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 16
      - name: npm install and build webpack
        run: |
          npm install
          npm run build
      - uses: actions/upload-artifact@v4
        with:
          name: webpack artifacts
          path: public/

  Build-Docker-Image:
    runs-on: ubuntu-latest
    needs: build
    name: Build image and store in GitHub Container Registry
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Download built artifact
        uses: actions/download-artifact@v4
        with:
          name: webpack artifacts
          path: public

      - name: Log in to GHCR
        uses: docker/login-action@v3
        with:
          registry: ${{ env.IMAGE_REGISTRY_URL }}
          username: ${{ github.actor }}
          password: ${{ secrets.CR_PAT }}

      - name: Extract metadata (tags, labels) for Docker
        id: meta
        uses: docker/metadata-action@v5
        with:
          images: ${{env.IMAGE_REGISTRY_URL}}/${{ github.repository }}/${{env.DOCKER_IMAGE_NAME}}
          tags: |
            type=sha,format=long,prefix=

      - name: Build and push Docker image
        uses: docker/build-push-action@v5
        with:
          context: .
          push: true
          tags: ${{ steps.meta.outputs.tags }}
          labels: ${{ steps.meta.outputs.labels }}

  Deploy-to-Azure:
    runs-on: ubuntu-latest
    needs: Build-Docker-Image
    name: Deploy app container to Azure
    steps:
      - name: "Login via Azure CLI"
        uses: azure/login@v2
        with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}

      - uses: azure/docker-login@v1
        with:
          login-server: ${{env.IMAGE_REGISTRY_URL}}
          username: ${{ github.actor }}
          password: ${{ secrets.CR_PAT }}

      - name: Deploy web app container
        uses: azure/webapps-deploy@v3
        with:
          app-name: ${{env.AZURE_WEBAPP_NAME}}
          images: ${{env.IMAGE_REGISTRY_URL}}/${{ github.repository }}/${{env.DOCKER_IMAGE_NAME}}:${{github.sha}}

      - name: Azure logout via Azure CLI
        uses: azure/CLI@v2
        with:
          inlineScript: |
            az logout
            az cache purge
            az account clear
```

1. Actualiza cada uno <username> de tus nombres de usuario de GitHub.
2. Confirme sus cambios en la rama production-deployment-workflow.
3. Vaya a la pestaña pull requests y haga clic en Compare & Pull request para la rama production-deployment-workflow y cree un pull request.

¡Genial! La sintaxis que usaste le indica a GitHub Actions que solo ejecute ese workflow cuando se realiza un commit en la rama main. ¡Ahora podemos implementar este flujo de trabajo para implementarlo en producción!

### Actividad 2: Merge your pull request
1. ¡Ahora puedes fusionar tu pull request!
2. Haga clic Merge pull request y deje esta pestaña abierta, ya que aplicaremos una etiqueta al pull request cerrado en el siguiente paso.
3. Ahora solo tenemos que esperar a que el paquete se publique en GitHub Container Registry y se produzca la implementación.

Espera unos 20 segundos y luego actualiza esta página (la que estás siguiendo). GitHub Actions se actualizará automáticamente al siguiente paso.

## Paso 06 : Implementación en producción

__El entorno de la nube__

A lo largo del curso, has creado recursos que, si no se gestionan, podrían generar facturación o consumir tus minutos gratuitos del proveedor de la nube. Una vez que hayas verificado tu aplicación en producción, desmantelaremos esos entornos para que puedas conservar tus minutos para seguir aprendiendo.

### Actividad 1: Destruye cualquier recurso en ejecución para no incurrir en cargos
1. Crea y aplica la etiqueta __destroy environment__ a tu pull request __production-deployment-workflow__ fusionada. Si ya cerraste la pestaña con tu solicitud de extracción, puedes volver a abrirla haciendo clic en "Pull Requests" y luego en el filtro "Cerrado" para ver las merged pull requests.

Ahora que has aplicado la etiqueta correcta, esperemos a que finalice el flujo de trabajo de GitHub Actions. Una vez finalizado, puedes confirmar que tu entorno se ha destruido visitando la URL de tu aplicación o iniciando sesión en Azure Portal para comprobar que no se está ejecutando.

Espera unos 20 segundos y luego actualiza esta página (la que estás siguiendo). GitHub Actions se actualizará automáticamente al siguiente paso.
