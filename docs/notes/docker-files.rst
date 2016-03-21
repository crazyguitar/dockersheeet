Dockerfile collections
======================

SSH daemon service 
------------------

Dockerfile

.. code-block:: console

    FROM ubuntu:14.04

    RUN apt-get update && apt-get install -y openssh-server
    RUN mkdir /var/run/sshd
    RUN echo 'root:pwd' | chpasswd
    RUN sed -i 's/PermitRootLogin without-password/PermitRootLogin yes/' /etc/ssh/sshd_config

    ENV NOTVISIBLE "in users profile"
    RUN echo "export VISIBLE=now" >> /etc/profile

    EXPOSE 22
    CMD ["/usr/sbin/sshd", "-D"]

output:

.. code-block:: console

    # build an image
    $ docker build -t docker-ssh .
    
    # login docker conatiner via ssh
    $ docker run -d -P --name sshd docker-ssh
    $ docker port sshd 
    22/tcp -> 0.0.0.0:32770
    $ ssh -p 32770 root@192.168.99.100
    root@7c6593d39e28:~#
