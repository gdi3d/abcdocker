# Docker Compose

Docker Compose is a tool that allows you to define and run applications with multiple containers. Using Docker Compose, you define your application's *services* and their configurations in a YAML file. Then you simply run `docker-compose up`, and all the containers will be launched.

The following example is the `docker-compose.yml` file from the tutorial [https://gdi3d.github.io/learning-to-build](https://gdi3d.github.io/learning-to-build).

!> To learn more commands you can use in Docker Compose, you can consult the documentation at [https://docs.docker.com/compose/compose-file/compose-file-v3/](https://docs.docker.com/compose/compose-file/compose-file-v3/)

```
version: "3.9"
services:
  web:
    image: youtubemp3_web:latest
    restart: on-failure
    ports:
      - "80:5000"
    volumes:
      - converted_mp3_vol:/data:ro 
    depends_on:
      - redis
    environment:
      - PYTHONPATH=/app
      - FLASK_APP=web.main
  celery_workers:
    image: youtubemp3_convert:latest
    restart: on-failure
    deploy:
      replicas: 5
    volumes:
      - converted_mp3_vol:/data
    depends_on:
      - redis
    environment:
      - PYTHONPATH=/app
  cleanup:
    image: youtubemp3_cleanup
    restart: on-failure
    volumes:
      - converted_mp3_vol:/data
  redis:
    image: redis:latest
    volumes:
     - redis_vol:/data

volumes:
  converted_mp3_vol:
    external: true
  redis_vol:
    external: true
```

# Connectivity Between Containers

An important topic to understand about Docker is how DNS works.

Once we define our services, we can call the containers running those services by their names.

This means that if, for example, we access the **web** service via console (`docker exec -it ...`) and run `ping redis`, we will get a response from the **redis** service.

To understand more about how the DNS service works in Docker, let's go to the post [https://gdi3d.github.io/how-to-connect-docker-containers-2021-07-26/](https://gdi3d.github.io/how-to-connect-docker-containers-2021-07-26/)

# Installing and Testing a Real Project

Now that we have a slightly clearer understanding of how Docker Compose works, let's test a real project, not just isolated containers.

Using the following project, you can learn more about **docker-compose** and some other things: [https://gdi3d.github.io/learning-to-build/#/README?id=getting-started](https://gdi3d.github.io/learning-to-build/#/README?id=getting-started)

!> Once the containers are launched with `docker-compose up`, you can continue interacting with them using the `docker` command; there's no need to learn new commands.