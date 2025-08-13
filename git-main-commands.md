| Comando | Descripción | Importancia |
| --- | --- | --- |
| `git commit --amend -m "[TASK-01] #comment Agregar bartool" -m "Este commit agrega la funcionalidad de agregar un toll en la barra."` | Permite modificar el mensaje del commit., lo que esta entre comillas es tu mensaje de commit, debes cambiarlo a tu descripción | Útil para corregir errores en el mensaje del commit. |
| `git branch -M main` | Cambia el nombre de la rama principal a main (la renombra). | Útil para estandarizar el nombre de la rama principal. |
| `git branch develop main` | Crea una nueva rama llamada `develop` basada en la rama `main`. | Útil para trabajar en nuevas características sin afectar la rama principal. |
|`git branch -d develop` | Elimina la rama `develop`.  Antes de usar este comando, debes hacer un checkout a una rama diferente de la que queires elimn¡inar. Puedes usar -D mayuscula para forzar la eliminación. | Útil para eliminar ramas que ya no se utilizan. |
| `git checkout -b develop main` | Crea y cambia (hace checkout) a la rama `develop` basada en la rama `main`. | Útil para trabajar en nuevas características sin afectar la rama principal. |
| `git branch` | Muestra una lista de todas las ramas en el repositorio. | Útil para verificar en qué rama se encuentra actualmente. |
| `git tag -a START_PROJECT -m "Stat repository with source code no versioned previusly"` | Crea una nueva etiqueta llamada `START_PROJECT` con un mensaje. | Útil para etiquetar un punto específico en el historial del repositorio. |
| `git push origin --all` | Envía todas las ramas al repositorio remoto. | Útil para sincronizar todas las ramas en el repositorio remoto. |
| `git push --tags` | Envía todas las etiquetas al repositorio remoto. | Útil para sincronizar todas las etiquetas en el repositorio remoto. |
| `git checkout  idcommit` | Cambia (hace checkout) a un commit anterior basado en su ID. | Útil para revisar cambios anteriores en el historial del repositorio. |
| `git fetch origin master` | Descarga los ultimos cambios que hay en la rama master del repositorio remoto (puedes cambiar master por la rama que deses actaulizar o usar --all pra descargar todo). | Útil para sincronizar tu repositorio local con el remoto. |
| `git merge origin/master` | Integra los cambios de la rama master en tu rama actual (puedes cambiar master por la rama que deses integrar en tu rama actual). | Útil integrar evitar conflictos antes del un pull request. |
| `git push -f origin HEAD:main` | Envía la rama actual al repositorio remoto, sobrescribiendo cualquier cambio existente en la rama principal. | Útil para actualizar la rama principal con cambios locales. |
| `git reset --hard origin/main` | Descarta todos los cambios locales y restaura la rama `main` a su estado actual (como esta en el repositorio remoto). | Útil para restaurar la rama local a su estado original en el repositorio remoto. |
| `git reset --hard TAG_ID` | Descarta todos los cambios locales y restaura los cambios del hasID al caul apunta el `TAG_ID` indicado (como estaba el repostirorio remoto en ese momento especifico). | Útil para restaurar la rama locala un momento especifico del tiempo en el repositorio remoto. |
| ` git  push --force-with-lease` | Resetea tu repositorio a un momento o instantanea especifica. (Convierte el hasID actual en el HEAD de tu repositorio remoto, descartando todos los cambios que fueron creados a partir de ese momento en el tiempo). | Útil cuando estas seguro de que quieres volver a una version especifica del pasado y descartar todos los cambios a partir de ese momento.|
| `git stash pop` | Restaura los cambios del último stash y los elimina del stash. | Útil para aplicar los cambios que se han guardado en un stash. |
| `git stash apply` | Restaura los cambios del último stash y los conserva en el stash. | Útil para aplicar los cambios que se han guardado en un stash. |
| `git stash list` | Muestra una lista de todos los stashes en el repositorio. | Útil para verificar los cambios que se han guardado en un stash. |
| `git stash clear` | Elimina todos los stashes en el repositorio. | Útil para limpiar el stash y liberar espacio. |
| `git stash drop stash@{1}` | Elimina el stash en la posición `1`. | Útil para eliminar un stash específico.