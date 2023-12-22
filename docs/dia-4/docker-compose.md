# Docker Compose

Docker Compose es una herramienta que permite definir y ejecutar aplicaciones con multiples contenedores. Al utilizar Docker Compose defines en un YAML los *servicios* de tu aplicación y sus configuraciones. Luego solo ejecutas `docker-compose up` y todos los contenedores serán lanzados.

El siguiente ejemplo es el archivo `docker-compose.yml` del tutorial [https://gdi3d.github.io/learning-to-build](https://gdi3d.github.io/learning-to-build).

!> Para conocer mas comandos que puedes utilizar en docker compose puedes consultar la doc [https://docs.docker.com/compose/compose-file/compose-file-v3/](https://docs.docker.com/compose/compose-file/compose-file-v3/)

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

# Conectividad entre contenedores

Un tema importante a saber de docker es el funcionamiento de DNS. 

Una vez que definimos nuestros servicios podemos llamar a los contenedores que ejecutan esos servicios a través de sus nombres.

Esto significa que si, por ejemplo accedemos vía consola al servicio **web** (`docker exec -it ...`) y ejecutamos `ping redis` obtendremos una respuesta del servicio **redis**.

Para entender más sobre como funciona el servicio de DNS en docker vayamos al post [https://gdi3d.github.io/how-to-connect-docker-containers-2021-07-26/](https://gdi3d.github.io/how-to-connect-docker-containers-2021-07-26/)


# Instalando y probando un proyecto real

Ahora que ya tenemos un poco más claro como funciona docker compose, probemos un proyecto real y no solo contenedores sueltos.

Utilizando el siguiente proyecto podrás aprender mas sobre **docker-compose** y algunas cosas más[https://gdi3d.github.io/learning-to-build/#/README?id=getting-started](https://gdi3d.github.io/learning-to-build/#/README?id=getting-started)

!> Una vez lanzados los contenedores con docker-compose up, puedes seguir interactuando con ellos utilizando el comando `docker`, no hace falta aprender nuevos comandos.
