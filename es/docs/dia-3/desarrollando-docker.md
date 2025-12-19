# Desarrollando con contenedores

Ya sabemos cómo lanzar contenedores. Pero si queremos usarlo para desarrollar y hacer nuestro día a día más cómodo, todavía debemos aprender, al menos, como exponer puertos y montar volúmenes.

Para eso vamos usar como base otro tutorial que escribí hace un tiempo

> [https://gdi3d.github.io/quit-virtualenv-and-use-docker-2020-03-22/](https://gdi3d.github.io/quit-virtualenv-and-use-docker-2020-03-22/)

# Port Forwarding

Lo primero que debemos hacer es clonar el repositorio

```sh
git clone https://github.com/gdi3d/quit-virtualenv-use-docker.git

```

Este repositorio contiene un proyecto de python que expone un simple webserver con un endpoint que devuelve un `Hello!`.

En este caso tenemos dos formas desarrollar con nuestra app *contenerizada*:

1. Usar una imagen de python, montar el repositorio clonado como volumen, instalar las librerías y exponer el puerto y correr el webserver (como explica el tutorial)
2. Crear un Dockerfile (ya existe) que instale todo en la imagen y luego solo montar el volumen y exponer el puerto.

Usaremos la opción número dos. Pero antes examinemos el Dockerfile que se encuentra en el repositorio.

```sh
# Accede al directorio donde has clonado el repo y ejecuta:

cat Dockerfile
```

Ahora que ya tenemos más claro lo que se incluirá en el contenedor podemos crear su imagen

```sh

docker build -t abcdocker_webserver .

# Verás algo similar a:

STEP 1/5: FROM python:3.6
Resolved "python" as an alias (/etc/containers/registries.conf.d/000-shortnames.conf)
Trying to pull docker.io/library/python:3.6...
Getting image source signatures
Copying blob sha256:5e3b1213efc56598e78bd602983945c164de2a37205e06a62dada823124dc743
Copying blob sha256:0e29546d541cdbd309281d21a73a9d1db78665c1b95b74f32b009e0b77a6e1e3
Copying blob sha256:9b829c73b52b92b97d5c07a54fb0f3e921995a296c714b53a32ae67d19231fcd
Copying blob sha256:cb5b7ae361722f070eca53f35823ed21baa85d61d5d95cd5a95ab53d740cdd56
Copying blob sha256:6494e4811622b31c027ccac322ca463937fd805f569a93e6f15c01aade718793
Copying blob sha256:6f9f74896dfa93fe0172f594faba85e0b4e8a0481a0fefd9112efc7e4d3c78f7
Copying blob sha256:9fddfdc56334f2e6efad7e241bf5e7459c40ed105c5478676f41c1244bd96752
Copying blob sha256:404f02044bac0432ca522cbb9f254b1c91fcea6806bfeef0be0b243b2f31bab7
Copying blob sha256:c4f42be2be53b900ebffc040c1df13de538434ccc5f5d954a56848a6169a3a3f
Copying config sha256:54260638d07c5e3ad24c6e21fc889abbc8486a27634c0892086ff71f3f44b104
Writing manifest to image destination
Storing signatures
STEP 2/5: WORKDIR /code
--> 9caf590ce01d
STEP 3/5: COPY . /code
--> 7dc7397fdc87
STEP 4/5: RUN pip install -r requirements.txt
Collecting flask
  Downloading Flask-2.0.3-py3-none-any.whl (95 kB)
Collecting Jinja2>=3.0
  Downloading Jinja2-3.0.3-py3-none-any.whl (133 kB)
Collecting Werkzeug>=2.0
  Downloading Werkzeug-2.0.3-py3-none-any.whl (289 kB)
Collecting click>=7.1.2
  Downloading click-8.0.4-py3-none-any.whl (97 kB)
Collecting itsdangerous>=2.0
  Downloading itsdangerous-2.0.1-py3-none-any.whl (18 kB)
Collecting importlib-metadata
  Downloading importlib_metadata-4.8.3-py3-none-any.whl (17 kB)
Collecting MarkupSafe>=2.0
  Downloading MarkupSafe-2.0.1-cp36-cp36m-manylinux_2_5_x86_64.manylinux1_x86_64.manylinux_2_12_x86_64.manylinux2010_x86_64.whl (30 kB)
Collecting dataclasses
  Downloading dataclasses-0.8-py3-none-any.whl (19 kB)
Collecting typing-extensions>=3.6.4
  Downloading typing_extensions-4.1.1-py3-none-any.whl (26 kB)
Collecting zipp>=0.5
  Downloading zipp-3.6.0-py3-none-any.whl (5.3 kB)
Installing collected packages: zipp, typing-extensions, MarkupSafe, importlib-metadata, dataclasses, Werkzeug, Jinja2, itsdangerous, click, flask
Successfully installed Jinja2-3.0.3 MarkupSafe-2.0.1 Werkzeug-2.0.3 click-8.0.4 dataclasses-0.8 flask-2.0.3 importlib-metadata-4.8.3 itsdangerous-2.0.1 typing-extensions-4.1.1 zipp-3.6.0
WARNING: Running pip as the 'root' user can result in broken permissions and conflicting behaviour with the system package manager. It is recommended to use a virtual environment instead: https://pip.pypa.io/warnings/venv
WARNING: You are using pip version 21.2.4; however, version 21.3.1 is available.
You should consider upgrading via the '/usr/local/bin/python -m pip install --upgrade pip' command.
--> d2c6a3379c64
STEP 5/5: ENTRYPOINT ["python", "/code/main.py"]
COMMIT abcdocker_webserver
--> 83f5c049a7ba
Successfully tagged localhost/abcdocker_webserver:latest
83f5c049a7baf24c8831a810e7f6fd99231495a0e49626df01207632530dc80e
```

Ahora que nuestra imagen está lista. Intentemos lanzar el contenedor sin configurar nada mas y ver que sucede

```
docker run --name webserver abcdocker_webserver

# Luego de ejecutar el comando verás algo similar a lo siguiente:

* Serving Flask app 'main' (lazy loading)
* Environment: production
WARNING: This is a development server. Do not use it in a production deployment.
Use a production WSGI server instead.
* Debug mode: off
* Running on all addresses.
WARNING: This is a development server. Do not use it in a production deployment.
* Running on http://10.88.0.2:5000/ (Press CTRL+C to quit)
```

Nuestro contenedor se lanzo correctamente y el webserver esta funcionando. Pero a qué URL deberíamos llamar para obtener una respuesta?

La IP que indica el log `http://10.88.0.2:5000/` es la IP interna del contenedor. Esto sucede ya que el motor de los contenedores tiene su propia red y asigna direcciones IP a cada uno de ellos.

Por lo tanto, con esta configuración no podremos ser capaces de hacer una petición a este webserver a menos que estemos dentro de esa red.

Abre otra ventana en tu terminal y utilicemos `docker ps` para comprobar

```
CONTAINER ID  IMAGE                                 COMMAND     CREATED        STATUS        PORTS       NAMES
4322ac1c806e  localhost/abcdocker_webserver:latest              7 seconds ago  Up 7 seconds              webserver
```

En la columna PORTS, podemos ver que no hay ningún *forwarding*, lo que significa que no hay conectividad entre nuestra red local y la creada por el motor de contenedores.

Detengamos y eliminemos el contenedor

```
# Primero CTRL+C, luego
docker stop webserver
docker rm webserver

# Comprobemos que ha sido eliminado. El siguiente comando no debería devolver nada
docker ps -a
```

Ahora vamos a volver a lanzar el contenedor, pero esta vez expondremos el puerto 8080 y haremos que el contenedor tenga conectividad con nuestra red local.

```
# Utilizamos el argumento -p para indicar PUERTO_RED_LOCAL:PUERTO_INTERNO_CONTENEDOR

docker run --name webserver -p 8080:5000 abcdocker_webserver
```

Ahora comprobemos con `docker ps`

```
CONTAINER ID  IMAGE                                 COMMAND     CREATED             STATUS             PORTS                   NAMES
44a8084ebe42  localhost/abcdocker_webserver:latest              About a minute ago  Up About a minute  0.0.0.0:8080->5000/tcp  webserver
```

!> También puedes utilizar `docker port webserver` y solamente verás la configuración de puertos

El mensaje de `0.0.0.0:8080->5000/tcp` esta indicando que cualquier petición que venga desde la red local (eso representa 0.0.0.0), sea forwardeada al puerto 5000 de nuestro contenedor utilizando el protocolo tcp.

```sh
# En otra terminal

curl http://127.0.0.1:8080/

# Respuesta

Hello!
```

Y si mirás los logs, también verás la llamada.

```sh
* Serving Flask app 'main' (lazy loading)
* Environment: production
WARNING: This is a development server. Do not use it in a production deployment.
Use a production WSGI server instead.
* Debug mode: off
* Running on all addresses.
WARNING: This is a development server. Do not use it in a production deployment.
* Running on http://10.88.0.6:5000/ (Press CTRL+C to quit)
10.88.0.6 - - [27/Jul/2023 17:18:40] "GET / HTTP/1.1" 200 -
```

# Montar volúmenes

Montar un volumen nos permite conectar nuestro directorio de trabajo en el filesystem del contenedor. De esta forma podemos modificar nuestro código de forma local y ese cambio se verá dentro del contenedor.

Si no existiera esta posibilidad, cada cambio en nuestro código debería de ser copiado al contenedor o, deberíamos construir una imagen cada vez que quisiéramos probar un cambio.

Al igual que con el puerto, solo debemos agregar un argumento al comando de `docker run`

Desde una terminal y, ubicado en el directorio donde hemos clonado el repositorio, ejecutamos lo siguiente:

```sh
docker run --name webserver -p 8080:5000 -e FLASK_ENV=development -v $(PWD):/code abcdocker_webserver
```

!> Agregamos la variable de entorno FLASH_ENV para permitir el hot-reload de nuestro código provisto por flask

Ahora, edita el archivo ´main.py´. En la línea 8 reemplaza `Hello!` por `Hello World!` y guarda el archivo.

Verás que nuestro webserver vuelve a iniciarse. Hagamos una nueva llamada al endpoint para comprobarlo.

```sh
curl http://127.0.0.1:8080

# Responderá

Hello World!
```
