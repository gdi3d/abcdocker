# Instalación

Si tienes Windows, antes de poder instalar Docker necesitarás instalar [WSL 2](https://learn.microsoft.com/es-es/windows/wsl/install)

Una vez instalado **WSL 2**, podrás instalar docker siguiendo [la guia de instalación](https://docs.docker.com/desktop/install/windows-install/) 

Recuerda que Docker es solo una opción para correr contenedores. En mi caso despues de varios años y algunos recientes problemas he decidido optar por [Podman](https://podman.io/docs/installation).

Tanto *Docker* como *Podman* tienen los mismos comandos. Por lo que no tendrás conflictos si quieres usar uno u el otro.

Aquí los links para las dos opciones

1. [Instalación de Docker](https://docs.docker.com/engine/install/)
2. [Instalación de Podman](https://podman.io/docs/installation)

En ambos casos se instalará una maquina virtual para el funcionamiento del engine de contenedores.

!> En mi caso desde hace algún tiempo opte por utilizar Podman para desarrollo. Tuve algunos problemas de rendimiento y estabilidad con las ultimas versiones de Docker. Podman me resulto más ligero y estable.

# Comprobar la instalación

!> Si has instalado Podman, en vez de utilizar el comando `docker`, reemplazalo por `podman` o crea una alias.  
Ej.: `docker build -t` .... se reemplaza por: `podman build -t ...`

```sh
# Clona este repositorio
git clone git@github.com:gdi3d/abcdocker.git

# Ve al directorio docs/resources/dia-1/check
docker build -t abcdocker_check .
```

Verás algo similar a:

```sh
STEP 1/4: FROM alpine:latest
STEP 2/4: WORKDIR /home
--> Using cache 8b65b764c83140e17b7b6edd4e535c7ea5999c94fc62304ba3b84b3f4a6d2e2e
--> 8b65b764c831
STEP 3/4: COPY helloworld.txt .
--> Using cache ff5e373e995af967843b824341b614b7f847cc186225dfcc70601b04d100f91c
--> ff5e373e995a
STEP 4/4: ENTRYPOINT ["cat", "helloworld.txt"]
--> Using cache 66c64bd08b65a19726d9ae52e80116418e515402852a42f18b81515def75b147
COMMIT abcdocer_check
--> 66c64bd08b65
Successfully tagged localhost/abcdocer_check:latest
Successfully tagged localhost/check:latest
66c64bd08b65a19726d9ae52e80116418e515402852a42f18b81515def75b147
```

# Hola Mundo

Comprobemos que el contenedor y docker funcionan correctamente:

```sh
docker run --rm abcdocker_check
```

Verás el siguiente mensaje


<pre style="line-height:1.2rem">

  _    _      _ _                            _     _ _
 | |  | |    | | |                          | |   | | |
 | |__| | ___| | | ___   __      _____  _ __| | __| | |
 |  __  |/ _ \ | |/ _ \  \ \ /\ / / _ \| '__| |/ _` | |
 | |  | |  __/ | | (_) |  \ V  V / (_) | |  | | (_| |_|
 |_|  |_|\___|_|_|\___/    \_/\_/ \___/|_|  |_|\__,_(_)

 Ya estas listo para continuar aprendiendo sobre contenedores!

</pre>

