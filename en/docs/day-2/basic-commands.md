# Basic Commands

Like other tools, Docker allows you to do many things, and therefore it has many commands. However, in this course we will focus on the most common ones.

!> I recommend checking the manual, as it is always useful to discover new commands https://docs.docker.com/engine/reference/commandline/cli/  
I recommend reviewing: run, exec, pull, logs, inspect, start/stop/restart, cp, images, ps, rm, stats, build

# Creating an image

To distribute your container, it is necessary to create an *image*. The image contains everything your code needs in order to run.

This image is composed of layers that are reused by other containers and that together form the file system inside your container.

!> More info: https://opensource.com/article/21/8/container-image

To get started, we will create our first image. Open your terminal and go to the directory where we cloned the repository, then navigate to `en/docs/resources/day-2/timestamp` (later you will be able to create one from scratch).

```sh
# Examine its contents
ls
cat Dockerfile
```

```sh
# Create an image called abcdocker_timestamp
docker build -t abcdocker_timestamp .
```

The result will look something like this:

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
Successfully tagged localhost/day2:latest
591586f9f590429e4137b255f12b8cce020d5fc11bc44c8bc56b6327e9b8ce78
```

Our image is ready and stored on our computer. We can verify this as follows:

```sh
docker images
```

You will see a list of images that includes *abcdocker_timestamp*.

```sh
REPOSITORY                     TAG         IMAGE ID      CREATED       SIZE
localhost/abcdocker_timestamp  latest      591586f9f590  23 hours ago  7.62 MB
...
```

!> Explore what else you can do with `docker image -h` and `docker images -h`

# Running and interacting with containers

For the following exercises, we will use the *image* we created earlier and launch different instances of that image as containers.

!> A container is based on an image, and you can run as many containers from the same image as you want.

```sh
# Run a container and remove it when it finishes
# Press CTRL+C to stop
docker run --rm abcdocker_timestamp
```

```sh
# Run a container and assign it a name
# Press CTRL+C to stop
docker run --name my-container abcdocker_timestamp
```

```sh
# Run a container in the background
docker run -d --name my-container-background abcdocker_timestamp

# Check that it is running
docker logs my-container-background

# Keep logs attached to see updates
docker logs -f my-container-background
```

How do we know if there are other containers running?

```sh
docker ps
```

```sh
CONTAINER ID  IMAGE                                 COMMAND     CREATED        STATUS        PORTS       NAMES
487da75b5424  localhost/abcdocker_timestamp:latest              2 minutes ago  Up 2 minutes              my-container-background
```

Let’s stop this container:

```sh
# Stop it using its name
docker stop my-container-background

# Or using its ID (your ID will be different)
docker stop 487da75b5424

# Or using the first 3 or 4 characters of the ID
docker stop 487
```

One command you will use very frequently is `docker exec`, which allows you to run commands inside a container and access it interactively.

To test this command, let’s run the `abcdocker_timestamp` container again:

```sh
docker run -d --name my-container-background abcdocker_timestamp
```

You will see the following error:

```sh
Error: creating container storage: the container name "my-container-background" is already in use by 94af5447d11a393b1ecd2b9a2964a1c43635749a6c61f328a9bc14406242c54f. You have to remove that container to be able to reuse that name: that name is already in use
```

What happened? Even though we stopped the container earlier, it still exists in a *stopped* state, and Docker does not allow two containers with the same name.

We can confirm this by using one of the options of `docker ps`:

```sh
# Show all containers, including stopped ones
docker ps -a
```

!> Remember you can always see all options of a command by adding `-h` at the end. Example: `docker ps -h`

To run our container again, we have two options:

1. Use `docker start my-container-background`
2. Remove the container and run a new one:  
   `docker rm my-container-background`  
   then  
   `docker run -d --name my-container-background abcdocker_timestamp`

Choose the option you prefer.

Once the container is running again (check with `docker ps` and ensure the **STATUS** column shows *Up*), you can access it.

```sh
# Access the container by name or ID
docker exec -it my-container-background /bin/sh

# Type exit to leave the container
```

Finally, we can stop and remove the container:

```sh
# Stop the container
docker stop my-container-background

# Verify it is stopped
docker ps -a

CONTAINER ID  IMAGE                                 COMMAND     CREATED             STATUS                       PORTS       NAMES
e7f998e61261  localhost/abcdocker_timestamp:latest              About a minute ago  Exited (137) 22 seconds ago              my-container-background

# Remove the container
docker rm my-container-background

# Verify it was removed
docker ps -a
```

It is important to note that removing a container does not remove its image. You can always run a new container based on the same image whenever you want.

!> When working with more than one container, you can use the following command to stop and remove all containers  
`docker stop $(docker ps -qa) && docker rm $(docker ps -qa)`
