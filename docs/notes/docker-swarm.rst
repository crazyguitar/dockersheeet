Docker Swarm Cheatsheet
=======================

Create mulitple nginx service
------------------------------

.. code-block:: bash

    # step1: create a service discovery container(consul)
    $ docker-machine create --driver virtualbox consul1 
    $ eval $(docker-machine env consul1)
    $ docker pull progrium/consul
    $ docker run -d --name consul              \
                 -p 8400:8400                  \
                 -p 8500:8500                  \
                 -p 8600:53/udp                \
                 -h node1 progrium/consul      \
                 -server -bootstrap -ui-dir /ui
    $ docker-machine ip consul1
    192.168.99.101

    # step2: create swarm master 
    $ docker-machine create                                 \
            --driver virtualbox                             \
            --swarm --swarm-master                          \
            --swarm-discovery consul://192.168.99.101:8500/ \
            swarm-master

    # step3: create swarm nodes
    $ docker-machine create                                 \ 
            --driver virtualbox                             \
            --swarm                                         \
            --swarm-discovery consul://192.168.99.101:8500/ \
            swarm1
    $ docker-machine create                                 \ 
            --driver virtualbox                             \
            --swarm                                         \
            --swarm-discovery consul://192.168.99.101:8500/ \
            swarm2

    # step4: assign nginx services
    $ eval "$(docker-machine env --swarm swarm-master)"
    $ docker info
    $ docker ps -a
    $ for i in 1 2 3; do docker run -d nginx; done
    $ docker ps --format "{{.ID}}: {{.Image}}: {{.Names}}"
    d9d07a457bc9: nginx: swarm2/big_pare
    6eb04332a55c: nginx: swarm2/romantic_visvesvaraya
    a36781752bbb: nginx: swarm1/adoring_boyd


