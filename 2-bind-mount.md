# BIND MOUNT
En un bind mount mapeamos (montar) un directorio o archivo específico del sistema de archivos del host con una parte del sistema de ficheros del contenedor.

```
docker run -d --name <nombre contenedor> -v <ruta carpeta host>:<ruta carpeta contenedor> <imagen> 
```
ó
```
docker run -d --name <nombre contenedor> --mount type=bind,source=<ruta carpeta host>,target=<ruta carpeta contenedor> <imagen>
```
- destination, dst, target: La ruta donde se monta el archivo o directorio en el contenedor.
- source, src: El origen del montaje.
  
### En tu computador crear una carpeta llamada nginx y dentro de esta carpeta crea otra llamada html. Como se aprecia en la figura.
![Volúmenes](directorio.PNG)

### Crear un contenedor con la imagen nginx:alpine, mapear todos por puertos, para la ruta carpeta host colocar el directorio en donde se encuentra la carpeta html en tu computador y para la ruta carpeta contenedor: /usr/share/nginx/html (esta ruta se obtiene al revisar la documentación de la imagen)
![Volúmenes](volumen-host.PNG)
# docker run -d --name server-nginx -P -v "${PWD}\nginx\html:/usr/share/nginx/html" nginx:alpine

### ¿Qué sucede al ingresar al servidor de nginx?
# Abrir http://localhost:32768. Verás un error 403 Forbidden. La carpeta html del host está vacía y se montó encima de la carpeta del contenedor, así que nginx no encuentra ningún index.html.

### ¿Qué pasa con el archivo index.html del contenedor?
# Queda oculto (tapado) por el bind mount, porque lo que se ve en esa ruta es el contenido de la carpeta del host. No se borra: sigue en la imagen y volvería a verse si el contenedor se crea sin el montaje.

### Ir a https://html5up.net/ y descargar un template gratuito, descomprirlo dentro de tu computador en la carpeta html
### ¿Qué sucede al ingresar al servidor de nginx?
# Ahora muestra el template descargado. Los archivos del host se reflejan de inmediato en el contenedor.

### Eliminar el contenedor
# docker rm -f server-nginx

### ¿Qué sucede al crear nuevamente un contenedor montado al directorio definidos anteriormente?
# La página con el template sigue funcionando, porque los datos están en la carpeta del host y no dependen del contenedor. Eliminar el contenedor no borró nada.


