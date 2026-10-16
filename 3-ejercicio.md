## Esquema para el ejercicio
![Imagen](esquema-ejercicio3.PNG)

### Crear red net-wp
# docker network create net-wp

### Para que persista la información es necesario conocer en dónde mysql almacena la información.
# COMPLETAR LA SIGUIENTE ORACIÓN. REVISAR LA DOCUMENTACIÓN DE LA IMAGEN EN https://hub.docker.com/
En el esquema del ejercicio carpeta del contenedor (a) es (/var/lib/mysql)

Ruta carpeta host: .../ejercicio3/db --> C:\Users\Jhonny\Documents\Octavo_Semestre\Construccion\Practicas\Practica 1\ejercicio3\db

### ¿Qué contiene la carpeta db del host?
# Al inicio está vacía, porque todavía no se ha creado ningún contenedor.

### Crear un contenedor con la imagen mysql:8  en la red net-wp, configurar las variables de entorno: MYSQL_ROOT_PASSWORD, MYSQL_DATABASE, MYSQL_USER y MYSQL_PASSWORD
#  docker run -d --name server-mysql --network net-wp \
# -e MYSQL_ROOT_PASSWORD=root123 \
# -e MYSQL_DATABASE=db_wordpress \
# -e MYSQL_USER=user_wp \
# -e MYSQL_PASSWORD=pass_wp \
# -v "$(pwd)/ejercicio3/db":/var/lib/mysql \
# mysql:8

### ¿Qué observa en la carpeta db que se encontraba inicialmente vacía?
# Ya no está vacía: aparecen los archivos de datos de MySQL (como ibdata1, carpetas mysql, performance_schema y la carpeta de la base db_wordpress, archivos de redo log, etc.). Esos son los datos reales del servidor, ahora guardados en tu equipo.

### Para que persista la información es necesario conocer en dónde wordpress almacena la información.
# COMPLETAR LA SIGUIENTE ORACIÓN. REVISAR LA DOCUMENTACIÓN DE LA IMAGEN EN https://hub.docker.com/
En el esquema del ejercicio la carpeta del contenedor (b) es (/var/www/html)

Ruta carpeta host: .../ejercicio3/www

### Crear un contenedor con la imagen wordpress en la red net-wp, configurar las variables de entorno WORDPRESS_DB_HOST, WORDPRESS_DB_USER, WORDPRESS_DB_PASSWORD y WORDPRESS_DB_NAME (los valores de estas variables corresponden a los del contenedor creado previamente)
# docker run -d --name server-wordpress --network net-wp -p 8081:80 \
# -e WORDPRESS_DB_HOST=server-mysql \
# -e WORDPRESS_DB_USER=user_wp \
# -e WORDPRESS_DB_PASSWORD=pass_wp \
# -e WORDPRESS_DB_NAME=db_wordpress \
# -v "$(pwd)/ejercicio3/www":/var/www/html \
# wordpress

### Personalizar la apariencia de wordpress y agregar una entrada

### Eliminar el contenedor y crearlo nuevamente, ¿qué ha sucedido?

# Al entrar, el sitio sigue igual, con el tema, la entrada y la configuración, porque todo se guardó en ejercicio3/www (archivos) y ejercicio3/db (base de datos). No hay que reinstalar nada. 

