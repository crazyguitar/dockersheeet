Docker Basic cheatsheet
=======================

Show docker info
----------------

.. code-block:: console

    $ docker info

Download a pre build image
--------------------------

.. code-block:: console

    $ docker pull ubuntu

``docker inspect``
------------------

.. code-block:: console

    $ docker images
    REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
    docker_web          latest              68262071e31a        11 hours ago        683.8 MB
    web                 latest              558bed20dcb5        11 hours ago        683.8 MB
    python              2.7                 1c32174fd534        9 days ago          676.8 MB
    redis               latest              4f5f397d4b7c        2 weeks ago         177.6 MB

    $ docker inspect 1c32174fd534

Run an interactive shell
------------------------

.. code-block:: console

    # basic run an interactive shell
    $ docker run -i -t ubuntu /bin/bash

    # mont a data volume
    $ docker -it -v `pwd`:/code ubuntu /bin/bash

    # mount a nfs volume in docker container shell
    $ docker --privileged -it ubuntu /bin/bash
    root@eef2a9813cfb:/# apt-get update
    root@eef2a9813cfb:/# apt-get install -y \
    > nfs-common \
    > nfs-client \
    > inotify-tools
    root@eef2a9813cfb:/# mount -t nfs -o nolock \
    > 192.168.1.44:/nfs /mnt

Exec a command in container directly
------------------------------------

.. code-block:: console

    docker run ubuntu /bin/echo "Hello"


Run a background task
---------------------

.. code-block:: console

    # using -d option
    $ JOB=$(docker run -d python \
    > /bin/bash -c "while true; echo hi; do sleep 3; done")
    $ docker logs $JOB  # show container log
    $ docker kill $JOB  # kill container job

``docker run`` port forwarding
------------------------------

.. code-block:: bash

    # -P  Publish all exposed ports to random ports
    $ docker run -d -P ${image} ${cmd}

    # -p  Publish a container's port(s) to the host
    $ docker run -d -p ${host_port}:${docker_port} ${image} ${cmd}


``docker port`` check port map
------------------------------

.. code-block:: console

    $ container_name="sshd"
    $ docker port ${container_name}
    22/tcp -> 0.0.0.0:32770

``docker ps`` check running container
-------------------------------------

.. code-block:: console

    $ docker ps # Show running containers
    $ docker -a # Show all containers


``docker commit`` updating an image
-----------------------------------

.. code-block:: console 

    $ docker run -it python /bin/bash
    root@6bf742506ff2:/# pip install flask
    $ docker ps      # check container which you want to commit
    $ docker commit -m "Inst flask" -a "fly" 6bf742506ff2 flask/python:v1
    $ docker images  # check images

================   ==============
option             description
================   ==============
 -m                commit message
 -a                author
 6bf742506ff2      container ID
 flask/python:v1   image name
================   ==============

Using Dockerfile create an image
--------------------------------

+--------------+-------------------------------------+-----------------------+
|KEYWORD       |  DESCRIPTION                        |  NOTATION             |  
+==============+=====================================+=======================+
|``FROM``      |  To specify the Base Image          |``From <image>:<tag>`` |
+--------------+-------------------------------------+-----------------------+
|``MAINTAINER``|  Set the Author who create the image|``MAINTAINER <name>``  |
+--------------+-------------------------------------+-----------------------+
|``RUN``       |  Execute any commands in a new layer|``RUN <command>``      |
|              |  on top of the current image and    |                       |
|              |  commit the results                 |                       |
+--------------+-------------------------------------+-----------------------+
|``CMD``       |  The main purpose of a CMD is to    |``CMD command param1`` |
|              |  provide defaults for an executing  |                       |
|              |  container                          |                       |
+--------------+-------------------------------------+-----------------------+
|``LABEL``     |  Adds metadata to an image          |``LABEL <key>=<value>``|
+--------------+-------------------------------------+-----------------------+
|``ADD``       |  Copies new files, directories or   |``ADD <src> <dst>``    | 
|              |  remote file URLs to the filesystem |                       |
|              |  of the container at the path       |                       |
+--------------+-------------------------------------+-----------------------+
|``VOLUME``    |  Creates a mount point              |``VOLUME ["/data"]``   |
+--------------+-------------------------------------+-----------------------+
|``WORKDIR``   |  Sets the working directory for any |``WORKDIR /path/dir``  |
|              |  ``RUN``, ``CMD``, ``ENTRYPOINT``,  |                       |
|              |  ``COPY`` and ADD instructions      |                       |
+--------------+-------------------------------------+-----------------------+
|``ARG``       |  defines a variable that users can  |``ARG <name>[=value>]``|
|              |  pass at build-time                 |                       |
+--------------+-------------------------------------+-----------------------+

Example of Dockerfile

.. code-block:: console

    FROM python:2.7
    ADD . /code
    WORKDIR /code
    RUN pip install -r requirements.txt
    CMD python -c 'print "Hello Docker"'

``docker build``

.. code-block:: console

    docker build -t docker/pyhello .


Remove an image
---------------

.. code-block:: console

    # docker rmi <image name>
    $ docker images
    REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
    docker/pyhello      latest              348eee814b08        16 minutes ago      683.8 MB
    python              2.7                 1c32174fd534        10 days ago         676.8 MB
    $ docker rmi -f docker/pyhello
    ocker rmi -f docker/pyhello
    Untagged: docker/pyhello:latest
    Deleted: sha256:348eee814b081fe2651c50a3c301b8143345e330c4f57c5018ff2965308d1796
    Deleted: sha256:3e1333f9861f93f254056ecba7336689d146a55a0dd6c9a7542fa4118f72ca2e
    Deleted: sha256:80aee556402dde95191df9925f0faa3273444f8826c8281b8ec6b2902a05a406


``docker save`` tar a repository
--------------------------------

.. code-block:: console

    $ docker save -o pyimg.tar.gz  python:latest

``docker load`` untar a repository
----------------------------------

.. code-block:: console

    $ docker load -i pyimg.tar.gz
