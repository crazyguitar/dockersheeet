``docker-compose`` cheatsheet
=============================

Common compose options
----------------------

+-----------------+------------------------------------------------------------+
| compose options |  description                                               |
+=================+============================================================+
| build           | Build Dockerfile                                           |
+-----------------+------------------------------------------------------------+
| depends_on      + Express dependency between services                        |
+-----------------+------------------------------------------------------------+
| env_file        | Add environment variables from a file                      |
+-----------------+------------------------------------------------------------+
| environment     | Add environment variables                                  | 
+-----------------+------------------------------------------------------------+
| image           | Specify the image to start the container from              | 
+-----------------+------------------------------------------------------------+
| ports           | Expose ports                                               |
+-----------------+------------------------------------------------------------+
| volumes         | Mount paths or named volumes                               |
+-----------------+------------------------------------------------------------+
| volumes_from    | Mount all of the volumes from another service or container | 
+-----------------+------------------------------------------------------------+
| version 2       | Version 2 files are supported by Compose 1.6.0+            |
+-----------------+------------------------------------------------------------+

Solve "``ERROR``: No such image:"
----------------------------------

.. code-block:: console

    $ docker rm $(docker ps -a -q)

flask
-----

compose working directory

.. code-block:: console

    $ tree .
    .
    ├── Dockerfile
    ├── app.py
    ├── docker-compose.yml
    └── requirements.txt

    0 directories, 4 files

app.py

.. code-block:: python

    from flask import Flask

    app = Flask(__name__)

    @app.route('/')
    def hello():
        return "Hello Docker Flask"

    if __name__ == "__main__":
        app.run(host="0.0.0.0", debug=True)

requirements.txt

.. code-block:: console

    flask

Dockerfile

.. code-block:: console

    FROM python
    ADD . /code
    WORKDIR /code
    RUN pip install -U pip
    RUN pip install -r requirements.txt
    CMD python app.py

docker-compose.yml

.. code-block:: yaml

    web:
      build: . 
      ports:
        - "5000:5000"
      command: python app.py

flask + redis
-------------

compose working directory

.. code-block:: console

    $ tree .
    .
    ├── Dockerfile
    ├── app.py
    ├── docker-compose.yml
    └── requirements.txt

    0 directories, 4 files

app.py

.. code-block:: python

    from flask import Flask
    from redis import Redis

    app = Flask(__name__)
    redis = Redis(host='redis', port=6379)

    @app.route('/')
    def hello():
        redis.incr('hits')
        return 'Hello World! I have been seen %s times.' % redis.get('hits')

    if __name__ == "__main__":
        app.run(host="0.0.0.0", debug=True)

requirements.txt

.. code-block:: console

    flask
    redis

Dockerfile

.. code-block:: console

    FROM python:2.7
    ADD . /code
    WORKDIR /code
    RUN pip install -r requirements.txt
    CMD python app.py

docker-compose.yml

.. code-block:: yaml

    version: '2'
    services:
      web:
        build: .
        ports:
         - "5000:5000"
        volumes:
         - .:/code
        depends_on:
         - redis
      redis:
        image: redis

run app

.. code-block:: console

    $ docker build -t web
    $ docker-compose up

