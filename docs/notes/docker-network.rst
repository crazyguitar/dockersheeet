Docker network cheatsheet
=========================

Macvlan setting
---------------

.. code-block:: bash

    #!/bin/bash

    if [ $# -lt 1 ]; then
            echo 'Usage: macvlan.sh $CONTAINER $CONTAINER_IFACE'
            exit 1
    fi

    CONTAINER_PID=$(/usr/bin/docker inspect --format="{{.State.Pid}}" $1)
    IF_NAME="$(ifconfig | grep ^eno | head -n 1 | cut -d':' -f 1)"
    MACVLAN=macvlan${IF_NAME}
    MAC_ADDR=`printf '00:11:32:%02X:%02X:%02X\n' $((RANDOM%256)) $((RANDOM%256)) $((RANDOM%256))`

    # default container if name is eth1
    CONTAINER_IF_NAME="eth1"
    if [ $2 ]; then
            CONTAINER_IF_NAME="$2"
    fi

    # NIC promisc mode
    if ! ip link set ${IF_NAME} promisc on; then
            echo "Turn NIC promisc mode on fail"
            exit 1
    fi

    # ifconfig  macvlan down
    if [ $(ip link |grep $MACVLAN | cut -d':' -f2) ]; then
            echo "ifconfig down $MACVLAN"
            ip link delete $MACVLAN
    fi

    # create macvlan interface
    if ! ip link add link ${IF_NAME} dev ${MACVLAN} mtu 1500 type macvlan mode bridge; then
            echo "create macvlan $MACVLAN fail"
            exit 1
    fi

    # setup namespace
    [ ! -d /var/run/netns ] && mkdir -p /var/run/netns
    [ -f /var/run/netns/$CONTAINER_PID ] && rm -f /var/run/netns/$CONTAINER_PID
    ip link set dev ${MACVLAN} netns ${CONTAINER_PID} > /dev/null 2>&1
    ln -s /proc/$CONTAINER_PID/ns/net /var/run/netns/$CONTAINER_PID

    ip netns exec ${CONTAINER_PID} ip link set ${MACVLAN} name ${CONTAINER_IF_NAME} > /dev/null 2>&1
    ip netns exec ${CONTAINER_PID} ip link set ${CONTAINER_IF_NAME} address ${MAC_ADDR}
    ip netns exec ${CONTAINER_PID} ip link set ${CONTAINER_IF_NAME} up
