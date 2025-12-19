# Developing with Containers

We already know how to launch containers. But if we want to use them for development and make our day-to-day work more comfortable, we still need to learn, at the very least, how to expose ports and mount volumes.

For this, we are going to base ourselves on another tutorial I wrote a while ago.

> [https://gdi3d.github.io/quit-virtualenv-and-use-docker-2020-03-22/](https://gdi3d.github.io/quit-virtualenv-and-use-docker-2020-03-22/)

# Port Forwarding

The first thing we need to do is clone the repository.

```sh
git clone https://github.com/gdi3d/quit-virtualenv-use-docker.git
```

This repository contains a Python project that exposes a simple web server with an endpoint that returns a `Hello!`.

In this case, we have two ways to develop with our *containerized* app:

1.  Use a Python image, mount the cloned repository as a volume, install the libraries, expose the port, and run the web server (as explained in the tutorial).
2.  Create a Dockerfile (it already exists) that installs everything into the image and then only mount the volume and expose the port.

We will use option number two. But first, let's examine the Dockerfile located in the repository.

```sh
# Go to the directory where you cloned the repo and run:

cat Dockerfile
```

Now that we have a clearer idea of what will be included in the container, we can create its image.

```sh
docker build -t abcdocker_webserver .

# You will see something similar to:

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

Now that our image is ready. Let's try to launch the container without configuring anything else and see what happens.

```
docker run --name webserver abcdocker_webserver

# After running the command, you will see something similar to the following:

* Serving Flask app 'main' (lazy loading)
* Environment: production
WARNING: This is a development server. Do not use it in a production deployment.
Use a production WSGI server instead.
* Debug mode: off
* Running on all addresses.
WARNING: This is a development server. Do not use it in a production deployment.
* Running on http://10.88.0.2:5000/ (Press CTRL+C to quit)
```

Our container launched correctly and the web server is running. But what URL should we call to get a response?

The IP indicated in the log, `http://10.88.0.2:5000/`, is the container's internal IP. This happens because the container engine has its own network and assigns IP addresses to each of them.

Therefore, with this configuration, we will not be able to make a request to this web server unless we are inside that network.

Open another terminal window and let's use `docker ps` to check.

```
CONTAINER ID  IMAGE                                 COMMAND     CREATED        STATUS        PORTS       NAMES
4322ac1c806e  localhost/abcdocker_webserver:latest              7 seconds ago  Up 7 seconds              webserver
```

In the PORTS column, we can see there is no *forwarding*, which means there is no connectivity between our local network and the one created by the container engine.

Let's stop and remove the container.

```
# First CTRL+C, then
docker stop webserver
docker rm webserver

# Let's check that it has been removed. The following command should not return anything.
docker ps -a
```

Now we are going to launch the container again, but this time we will expose port 8080 and make the container have connectivity with our local network.

```
# We use the -p argument to indicate LOCAL_NETWORK_PORT:INTERNAL_CONTAINER_PORT

docker run --name webserver -p 8080:5000 abcdocker_webserver
```

Now let's check with `docker ps`.

```
CONTAINER ID  IMAGE                                 COMMAND     CREATED             STATUS             PORTS                   NAMES
44a8084ebe42  localhost/abcdocker_webserver:latest              About a minute ago  Up About a minute  0.0.0.0:8080->5000/tcp  webserver
```

!> You can also use `docker port webserver` and you will only see the port configuration.

The message `0.0.0.0:8080->5000/tcp` is indicating that any request coming from the local network (that's what 0.0.0.0 represents) is forwarded to port 5000 of our container using the TCP protocol.

```sh
# In another terminal

curl http://127.0.0.1:8080/

# Response

Hello!
```

And if you look at the logs, you will also see the call.

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

# Mounting Volumes

Mounting a volume allows us to connect our working directory to the container's filesystem. This way we can modify our code locally and that change will be reflected inside the container.

If this possibility did not exist, every change to our code would have to be copied to the container, or we would have to build an image every time we wanted to test a change.

Just like with the port, we only need to add an argument to the `docker run` command.

From a terminal, located in the directory where we cloned the repository, run the following:

```sh
docker run --name webserver -p 8080:5000 -e FLASK_ENV=development -v $(PWD):/code abcdocker_webserver
```

!> We added the FLASK_ENV environment variable to enable the hot-reload of our code provided by Flask.

Now, edit the `main.py` file. On line 8, replace `Hello!` with `Hello World!` and save the file.

You will see that our web server restarts. Let's make a new call to the endpoint to check.

```sh
curl http://127.0.0.1:8080

# It will respond with

Hello World!
```