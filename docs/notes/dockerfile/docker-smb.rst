Samba File Protocol Service
============================

Dockerfile

.. code-block:: bash

    FROM base/archlinux:latest
    MAINTAINER fly <cntsai@synology.com>

    RUN pacman -Sy --noconfirm  archlinux-keyring
    RUN pacman-db-upgrade
    RUN pacman --noconfirm -S samba
    RUN systemctl enable smbd nmbd

    ENV SMB_GROUP samba
    ENV SMB_USR dockersmb
    ENV SMB_PWD dockersmb
    ENV SMB_SHARE synosrc
    ENV SMB_EXPOSE_PATH /synosrc
    ENV SMB_CONF_PATH /etc/samba/smb.conf
    COPY smb.conf.temp $SMB_CONF_PATH
    RUN sed "s/#SMB_USR/$SMB_USR/" -i $SMB_CONF_PATH
    RUN sed "s/#SMB_SHARE/$SMB_SHARE/" -i $SMB_CONF_PATH
    RUN sed "s@#SMB_EXPOSE_PATH@$SMB_EXPOSE_PATH@" -i $SMB_CONF_PATH

    RUN mkdir $SMB_SHARE
    VOLUME ["$SMB_SHARE"]

    RUN groupadd $SMB_GROUP 
    RUN useradd -m -g $SMB_GROUP -s /sbin/nologin $SMB_USR
    RUN printf "%s\n%s\n" $SMB_PWD $SMB_PWD | pdbedit -a -u $SMB_USR

    CMD ["/usr/bin/smbd", "-F"]

smb.conf

.. code-block:: bash

    [global]
        workgroup = WORKGROUP
        server string = POGOPLUG
        netbios name = POGOPLUG
        printcap name = /dev/null
        load printers = no
        disable spoolss = yes
        printing = bsd
        show add printer wizard = no
        print notify backchannel = no
        log file = /var/log/samba/log.%m
        max log size = 50
        security = user
        dns proxy = no
        map to guest = Bad User
        unix extensions = false
        # oplocks = no
        # level2 oplocks = no
        socket options = TCP_NODELAY IPTOS_LOWDELAY
        write cache size = 262144
        use sendfile = true
        getwd cache = yes
        min receivefile size = 16384
        max xmit = 65536
        public = yes

    [#SMB_SHARE]
        comment = Expose Share
        public = no
        valid users = #SMB_USR
        read only = no
        writeable = yes
        path = #SMB_EXPOSE_PATH

``docker build`` and run samba service

.. code-block:: bash

    $ docker build -t samba .
    $ docker run -d --rm -p 445:445 \
                 --privileged       \
                 -v /share:/mnt     \
                 --name samba samba
