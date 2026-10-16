# Mi aprendizaje

## Conocimientos antes y después de la práctica

Antes de la práctica sabía crear contenedores con `docker run` y exponer puertos, pero no tenía claro cómo conservar los datos cuando un contenedor se elimina. Después de hacerla comprendí que el contenedor es desechable y que los datos importantes deben vivir fuera de él. Aprendí a usar los tres tipos de montaje:

- **Bind mount:** enlaza una carpeta concreta de mi computador con una carpeta del contenedor. Es útil para desarrollo, porque los cambios en los archivos se reflejan al instante (por ejemplo, el template en nginx).
- **Volumen nombrado:** Docker lo administra y se identifica por un nombre. Es el más adecuado para datos que deben conservarse, como bases de datos (PostgreSQL) o los archivos de Drupal.
- **Volumen anónimo:** Docker lo crea sin nombre y es más difícil de gestionar. Sirve para datos temporales y se elimina con `docker rm -fv` o `docker volume prune`.

También entendí que un bind mount *tapa* el contenido original de la carpeta del contenedor (en nginx apareció el error 403 hasta que agregué un `index.html`), y que al eliminar y recrear WordPress el sitio seguía intacto porque sus datos estaban en `ejercicio3/www` y `ejercicio3/db`. Además, aprendí a comunicar contenedores por nombre dentro de una red de Docker (`net-wp`, `net-drupal`) en lugar de usar direcciones IP.

## Problemas presentados y cómo los resolví

### 1. El comando `mkdir -p` no funcionó en PowerShell

Al crear las carpetas del ejercicio 3 con `mkdir -p ejercicio3/db ejercicio3/www` apareció el error `No se encuentra ningún parámetro de posición que acepte el argumento 'ejercicio3/www'`.

**Causa:** ese comando es de Linux. En PowerShell, `mkdir` no tiene la opción `-p` y las carpetas múltiples se separan con comas.

**Solución:**
```powershell
mkdir ejercicio3\db, ejercicio3\www
```
También adapté los comandos de Docker a PowerShell: usar el acento grave `` ` `` para continuar líneas, `${PWD}` para la ruta actual y comillas en la ruta de `-v`, porque mi ruta contiene un espacio (`Practica 1`).

### 2. Drupal no se conectaba a la base de datos (ejercicio 4)

En el paso de configuración de la base de datos de Drupal apareció el error:
`SQLSTATE[08006] could not translate host name "server-postgres" to address: No address associated with hostname`.

**Diagnóstico:** con `docker ps -a` vi que el contenedor `server-postgres` estaba en estado `Exited (1)`, es decir, se había detenido al arrancar. Como no estaba en ejecución, Drupal no podía resolver su nombre dentro de la red `net-drupal`. Con `docker logs server-postgres` revisé el motivo.

**Causa:** las versiones recientes de la imagen `postgres` cambiaron la ruta donde guardan los datos. La ruta `/var/lib/postgresql/data` del volumen ya no coincide con la que espera la imagen más reciente, por lo que el contenedor falla al iniciar.

**Solución:** eliminé el contenedor y el volumen y los volví a crear usando una versión compatible con esa ruta (`postgres:16`):
```powershell
docker rm -f server-postgres
docker volume rm vol-postgres
docker volume create vol-postgres
docker run -d --name server-postgres `
  -e POSTGRES_DB=db_drupal -e POSTGRES_PASSWORD=12345 -e POSTGRES_USER=user_drupal `
  -v vol-postgres:/var/lib/postgresql/data `
  --network net-drupal postgres:16
```
Luego comprobé que estuviera en estado `Up` con `docker ps` y que dijera "ready to accept connections" en `docker logs`. Después reintenté la instalación de Drupal usando **`server-postgres`** como host en las opciones avanzadas (el valor por defecto `localhost` no funciona, porque cada contenedor tiene su propia red interna).

**Lección:** leer la documentación de la imagen en Docker Hub antes de elegir la ruta del volumen, y revisar siempre el estado de los contenedores y sus logs cuando algo falla.

## Otros comandos que utilicé

- `docker ps` y `docker ps -a`: ver contenedores en ejecución y todos los contenedores, incluidos los detenidos.
- `docker logs <contenedor>`: ver por qué un contenedor falló o si ya está listo.
- `docker network create` y `docker network inspect`: crear redes y comprobar qué contenedores pertenecen a cada una.
- `docker volume create`, `docker volume ls` y `docker volume inspect`: crear y consultar volúmenes y su punto de montaje.
- `docker rm -f` y `docker rm -fv`: eliminar contenedores (y sus volúmenes anónimos).
- `docker volume prune`: eliminar volúmenes que no están en uso.

## Conclusión

Esta práctica me ayudó a entender que persistir datos es indispensable para usar Docker en proyectos reales, como un sitio WordPress o una base de datos. Mi principal aprendizaje es que, ante un error, hay que diagnosticar con `docker ps -a` y `docker logs` en lugar de repetir el mismo comando, y que la documentación oficial de cada imagen es clave para configurar bien los volúmenes.