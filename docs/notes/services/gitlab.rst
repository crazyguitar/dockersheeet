GitLab
========

GitLab Basic Setting
---------------------

Step1: set postgres
~~~~~~~~~~~~~~~~~~~~

.. code-block:: bash

    docker run -d --name gitlab-postgres                       \
      --volume /mnt/gitlab-postgres-volume:/var/lib/postgresql \
      --env DB_NAME=gitlabhq_production                        \
      --env DB_USER=gitlab                                     \
      --env DB_PASS=password                                   \
      --env DB_EXTENSION=pg_trgm                               \
      sameersbn/postgresql

Step2: set redis
~~~~~~~~~~~~~~~~~

.. code-block:: bash

    docker run -d --name gitlab-redis                  \
      --volume /mnt/gitlab-redis-volume:/var/lib/redis \
      sameersbn/redis

Set3: start GitLab
~~~~~~~~~~~~~~~~~~~

.. code-block:: bash

    docker run -d --name gitlab                                             \
      --link gitlab-postgres:postgresql                                     \
      --link gitlab-redis:redisio                                           \
      --publish 10022:22                                                    \
      --publish 10080:80                                                    \
      --env GITLAB_HOST=www.example.com                                     \
      --env GITLAB_PORT=10080                                               \
      --env GITLAB_SSH_PORT=10022                                           \
      --env GITLAB_SECRETS_DB_KEY_BASE=long-and-random-alpha-numeric-string \
      --volume /mnt/gitlab-volume:/home/git/data                            \
      sameersbn/gitlab:8.6.4

GitLab CI
----------

Set GitLab Runner
~~~~~~~~~~~~~~~~~~

.. code-block:: bash

    docker run -d --name gitlab-runner                \
      -v /mnt/gitlab-runner-volume:/etc/gitlab-runner \
      gitlab-runner

Register the runner
~~~~~~~~~~~~~~~~~~~~

.. code-block:: bash

    docker exec -it gitlab-runner gitlab-runner register

Check config
~~~~~~~~~~~~~

.. code-block:: bash

    concurrent = 1

    [[runners]]
      name = "a2e70727f624"
      url = "http://www.example.com:10080/ci"
      token = "asde4be6034e63019easec712783hh"
      executor = "shell"
      [runners.ssh]
      [runners.docker]
        tls_verify = false
        image = ""
        privileged = false
        disable_cache = false
      [runners.parallels]
        base_name = ""
        disable_snapshots = false
      [runners.virtualbox]
        base_name = ""
        disable_snapshots = false
      [runners.cache]
        Insecure = false
