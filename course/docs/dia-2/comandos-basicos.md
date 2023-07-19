# Comandos Básicos

Como otras herramientas, Docker permite hacer muchas cosas y por lo tanto tiene muchos comandos. Pero en este curso nos vamos a enfocar en los más comunes.

!> Te recomiendo consultar el manual ya que siempre es bueno descubrir comandos nuevos https://docs.docker.com/engine/reference/commandline/cli/  
Te recomiendo examinar: run, exec, pull, logs, inspect, start/stop/restart, cp, images, ps, rm, stats, build

# Creando imagenes

Para poder distribuir tu contenedor es necesario crear una *imagen* del mismo. La imagen contiene todo lo necesario para que tu código pueda funcionar.

Esta imagen se compone de capas que son reutilizadas por otros contenedores y que terminan conformando el file system que existe dentro de tu contenedor

!> Más info: https://opensource.com/article/21/8/container-image

Para comenzar, crearemos nuestra primera imagen. Abre tu terminal y ve al directorio donde clonamos el repositorio y accede a `course/resources/dia-2/timestamp` (más adelante podrás crear una desde 0)

```sh
# Examinemos su contenido
ls
cat Dockerfile
```

```sh
# Creemos una imagen llamada abcdocker_timestamp

docker build -t abcdocker_timestamp .
```

El resultado será algo similar a esto

```sh
STEP 1/2: FROM alpine:latest
Resolved "alpine" as an alias (/etc/containers/registries.conf.d/000-shortnames.conf)
Trying to pull docker.io/library/alpine:latest...
Getting image source signatures
Copying blob sha256:31e352740f534f9ad170f75378a84fe453d6156e40700b882d737a8f4a6988a3
Copying config sha256:c1aabb73d2339c5ebaa3681de2e9d9c18d57485045a4e311d9f8004bec208d67
Writing manifest to image destination
Storing signatures
STEP 2/2: ENTRYPOINT ["sh", "-c", "while true; do    date;    sleep 1; done"]
--> Using cache 591586f9f590429e4137b255f12b8cce020d5fc11bc44c8bc56b6327e9b8ce78
COMMIT abcdocker_timestamp
--> 591586f9f590
Successfully tagged localhost/abcdocker_timestamp:latest
Successfully tagged localhost/dia2:latest
591586f9f590429e4137b255f12b8cce020d5fc11bc44c8bc56b6327e9b8ce78
```

Nuestra imagen está lista y guardada en nuestra pc. Podemos verificarlo de la siguiente manera:

```sh
docker images
```

Verás una lista de imágenes entre las cuales se encuentra *abcdocker_timestamp*

```sh
REPOSITORY                     TAG         IMAGE ID      CREATED       SIZE
localhost/abcdocker_timestamp  latest      591586f9f590  23 hours ago  7.62 MB
...
```

!> Explora que otras cosas puedes hacer con `docker image -h` y `docker images -h`

# Lanzando e interactuando con contenedores

Para los siguientes ejercicios usaremos la *imagen* que hemos creado anteriormente y lanzaremos diferentes instancias de esa imagen como contenedores.

!> Un contenedor está basado en una imagen y puedes lanzar tantos contenedores de la misma imagen como quieras.

```sh
# Lanza un contenedor y eliminalo cuando haya finalizado
# Presiona CTRL+C para abortar

docker run --rm abcdocker_timestamp
```

```sh
# Lanza un contenedor y asígnale un nombre
# Presiona CTRL+C para abortar

docker run --name mi-contenedor abcdocker_timestamp
```

```sh
# Lanza un contenedor y ejecutalo en background

docker run -d --name mi-contenedor-background abcdocker_timestamp

# Comprueba que está funcionando

docker logs mi-contenedor-background

# Mantiene el log activo para ver actualizaciones
docker logs -f mi-contenedor-background
```

Pero como saber si hay más contenedores corriendo?

```sh
docker ps
```

```sh
CONTAINER ID  IMAGE                                 COMMAND     CREATED        STATUS        PORTS       NAMES
487da75b5424  localhost/abcdocker_timestamp:latest              2 minutes ago  Up 2 minutes              mi-contenedor-background
```

Vamos a detener este contenedor

```sh
# Podemos detenerlo utilizando su nombre

docker stop mi-contenedor-background

# Su ID (tu ID será distinto y lo veras despues haber lanzado docker ps)

docker stop 487da75b5424

# O los primeros 3 o 4 caracteres de su ID

docker stop 487
```

Un comando que utilizarás muchísimo es `docker exec` que permite ejecutar comandos dentro del contenedor, lo que también nos permite acceder a él.

Para probar este comando volvamos a lanzar el contenedor `abcdocker_timestamp`

```sh
docker run -d --name mi-contenedor-background abcdocker_timestamp

```

Verás el siguiente error:

```sh
Error: creating container storage: the container name "mi-contenedor-background" is already in use by 94af5447d11a393b1ecd2b9a2964a1c43635749a6c61f328a9bc14406242c54f. You have to remove that container to be able to reuse that name: that name is already in use
```

Pero qué sucede, si ya habiamos parado el contenedor en un paso anterior?. El problema es que el contenedor todavía existe, pero en estado de *stop* y no puedes tener otro contenedor con el mismo nombre.

Esto lo podemos comprobar utilizando una de las opciones de `docker ps`

```sh
# Muestra todos los contenedores, incluyendo los que esten en stop

docker ps -a
```

!> Recuerda que siempre puedes ver todas las opciones de un comando agregando un -h al final. Ej.: `docker ps -h`

Para poder lanzar nuestro contenedor tenemos dos opciones:

1. Utilizar el comando `docker start mi-contenedor-background`
2. Eliminar el contenedor y lanzar uno nuevo. `docker rm mi-contenedor-background` y luego `docker run -d --name mi-contenedor-background abcdocker_timestamp`

Elige la opción que más te guste.

Una vez que nuestro contenedor ya está funcionando nuevamente, comprobandolo con `docker ps` y viendo que en la columna **STATUS** aparece como *Up*, ya podemos acceder a él.

```sh
# Accede al contendor por su nombre o ID

docker exec -it mi-contenedor-background /bin/sh

# Escribe exit para salir del contenedor
```


