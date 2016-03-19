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


flask + postgresql
------------------

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
    from flask_sqlalchemy import SQLAlchemy

    DB_NAME    = 'postgres'
    DB_USER    = 'postgres'
    DB_PASS    = 'postgres'
    DB_SERVICE = 'postgres'
    DB_PORT    = 5432
    SQLALCHEMY_DATABASE_URI = \
        'postgresql://{0}:{1}@{2}:{3}/{4}'.format(
        DB_USER, DB_PASS, DB_SERVICE, DB_PORT, DB_NAME)

    app = Flask(__name__)
    db = SQLAlchemy(app)

    class DockerTable(db.Model):
        __tablename__ = 'docker'
        id = db.Column(db.Integer, primary_key=True)
        uuid = db.Column(db.String, nullable=False)

    db.create_all()

    @app.route('/')
    def hello():
        import uuid
        docker_uuid = uuid.uuid1()
        r = DockerTable(uuid=str(docker_uuid))
        db.session.add(r)
        db.session.commit()
        items = DockerTable.query.order_by(
                DockerTable.id.desc()).all()
        msg = "<pre>ALL UUID:\n"
        for _ in items:
            msg += _.uuid + '\n'
        msg += "</pre>"
        return msg

    if __name__ == "__main__":
        app.run(host="0.0.0.0", debug=True)

Dockerfile

.. code-block:: console

    FROM python
    ADD . /code
    WORKDIR /code
    RUN pip install -U pip
    RUN pip install -r requirements.txt

docker-compose.yml

.. code-block:: yaml

    version: '2'
    services:
      web:
        build: . 
        ports:
          - "5000:5000"
        command: python app.py
        volumes:
          - .:/code
        depends_on: 
          - postgres
      postgres:
        image: postgres

run app

.. code-block:: console

    $ docker-compose up

output example: (open browser)

.. image:: ../_static/img/flask+postgres.png
