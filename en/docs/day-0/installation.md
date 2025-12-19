# Installation

If you are using Windows, before installing Docker you will need to install [WSL 2](https://learn.microsoft.com/en-us/windows/wsl/install).

Once **WSL 2** is installed, you can install Docker by following [the installation guide](https://docs.docker.com/desktop/install/windows-install/).

Remember that Docker is just one option for running containers. In my case, after several years of use and some recent issues, I decided to switch to [Podman](https://podman.io/docs/installation).

Both *Docker* and *Podman* use the same commands, so you won’t run into any issues if you choose one or the other.

Here are the links for both options:

2. [Podman installation](https://podman-desktop.io/downloads) *(recommended)*
1. [Docker installation](https://docs.docker.com/desktop/)

In both cases, a virtual machine will be installed to run the container engine.

!> For some time now, I have chosen to use Podman for development. I experienced some performance and stability issues with the latest versions of Docker. Podman turned out to be lighter and more stable for me.

# Verify the installation

!> If you installed Podman, instead of using the `docker` command, replace it with `podman` or create an alias.  
Example: `docker build -t ...` becomes `podman build -t ...`

```sh
# Clone this repository
git clone https://github.com/gdi3d/abcdocker.git

# Go to the abcdocker/en/docs/resources/day-0/check directory and create your first image
cd abcdocker/en/docs/resources/day-0/check
docker build -t abcdocker_check .
```

You will see something similar to:

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

# Hello World

Let’s verify that the container and Docker are working correctly:


```sh
docker run --rm abcdocker_check
```

You will see the following message:

<pre style="line-height:1.2rem">

  _    _      _ _                            _     _ _
 | |  | |    | | |                          | |   | | |
 | |__| | ___| | | ___   __      _____  _ __| | __| | |
 |  __  |/ _ \ | |/ _ \  \ \ /\ / / _ \| '__| |/ _` | |
 | |  | |  __/ | | (_) |  \ V  V / (_) | |  | | (_| |_|
 |_|  |_|\___|_|_|\___/    \_/\_/ \___/|_|  |_|\__,_(_)

 You're now ready to continue learning about containers!

</pre>
