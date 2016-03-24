Common network tool cheatsheet
==============================

``route`` show / manipulate the IP routing table
------------------------------------------------

show IP routing table with host name

.. code-block:: console

    $ route
    Kernel IP routing table
    Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
    default         140.112.91.254  0.0.0.0         UG    100    0        0 eth0
    140.112.91.0    *               255.255.255.0   U     0      0        0 eth0
    link-local      *               255.255.0.0     U     1000   0        0 eth0
    169.254.95.0    *               255.255.255.0   U     1      0        0 usb0

show IP routing table with ip address

==============   =====================
    option        description
==============   =====================
-n, --numeric    don't resolve names     
==============   =====================

.. code-block:: console

    $ route -n -v
    Kernel IP routing table
    Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
    0.0.0.0         140.112.91.254  0.0.0.0         UG    100    0        0 eth0
    140.112.91.0    0.0.0.0         255.255.255.0   U     0      0        0 eth0
    169.254.0.0     0.0.0.0         255.255.0.0     U     1000   0        0 eth0
    169.254.95.0    0.0.0.0         255.255.255.0   U     1      0        0 usb0


``netstat``: display network state
----------------------------------

display all sockets state
~~~~~~~~~~~~~~~~~~~~~~~~~

=======================   =========================================
  option                   description
=======================   =========================================
 -a, --all, --listening    display all sockets (default: connected)
=======================   =========================================

.. code-block:: console

    $ netstat -a
    Active Internet connections (servers and established)
    Proto Recv-Q Send-Q Local Address           Foreign Address         State   
    tcp        0      0 localhost:ipp           *:*                     LISTEN  
    tcp        0      0 localhost:smtp          *:*                     LISTEN  
    tcp        0      0 *:3389                  *:*                     LISTEN  
    tcp        0      0 *:1022                  *:*                     LISTEN 
    tcp        0      0 *:2016                  *:*                     LISTEN 
    tcp        0      0 *:33824                 *:*                     LISTEN
    tcp        0      0 localhost:27017         *:*                     LISTEN 
    tcp        0      0 localhost:mysql         *:*                     LISTEN 
    tcp        0      0 *:sunrpc                *:*                     LISTEN 
    tcp        0      0 *:http                  *:*                     LISTEN 
    tcp        0      0 localhost:domain        *:*                     LISTEN 
    tcp        0      0 localhost:3350          *:*                     LISTEN 
    tcp        1      0 mvnl.csie.ntu.edu:45006 economy.canonical.:http CLOSE_WAIT
    tcp        1      0 mvnl.csie.ntu.edu:59399 economy.canonical.:http CLOSE_WAIT

display tcp/udp sockets 
~~~~~~~~~~~~~~~~~~~~~~~

==========   ==========================
  option      description
==========   ==========================
 -t           display all tcp sockets
 -u           display all udp sockets
==========   ==========================

tcp

.. code-block:: console

    $ netstat -at
    Active Internet connections (servers and established)
    Proto Recv-Q Send-Q Local Address           Foreign Address         State      
    tcp        0      0 localhost:ipp           *:*                     LISTEN     
    tcp        0      0 localhost:smtp          *:*                     LISTEN     
    tcp        0      0 *:3389                  *:*                     LISTEN     
    tcp        0      0 *:1022                  *:*                     LISTEN     
    tcp        0      0 *:2016                  *:*                     LISTEN     
    tcp        0      0 *:33824                 *:*                     LISTEN     
    tcp        0      0 localhost:27017         *:*                     LISTEN     
    tcp        0      0 localhost:mysql         *:*                     LISTEN     
    tcp        0      0 *:sunrpc                *:*                     LISTEN     
    tcp        0      0 *:http                  *:*                     LISTEN     
    tcp        0      0 localhost:domain        *:*                     LISTEN     
    tcp        0      0 localhost:3350          *:*                     LISTEN     
    tcp        1      0 mvnl.csie.ntu.edu:45006 economy.canonical.:http CLOSE_WAIT 
    tcp        1      0 mvnl.csie.ntu.edu:59399 economy.canonical.:http CLOSE_WAIT


udp

.. code-block:: console

    $ netstat -au
    Active Internet connections (servers and established)
    Proto Recv-Q Send-Q Local Address           Foreign Address         State      
    udp        0      0 *:52526                 *:*                                
    udp        0      0 localhost:domain        *:*                                
    udp        0      0 *:bootpc                *:*                                
    udp        0      0 *:sunrpc                *:*                                
    udp        0      0 *:677                   *:*                                
    udp        0      0 localhost:726           *:*                                
    udp        0      0 *:mdns                  *:*                                
    udp        0      0 *:34003                 *:*                                
    udp        0      0 *:40458                 *:*                                
    udp6       0      0 [::]:sunrpc             [::]:*                             
    udp6       0      0 [::]:677                [::]:*

show statistics for each protocol
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

=================   ==========================================
 option              description
=================   ==========================================
-s, --statistic      display networking statistics (like SNMP)
=================   ==========================================

.. code-block:: console

    $ netstat -s
    Ip:
        308571830 total packets received
        0 forwarded
        1 with unknown protocol
        0 incoming packets discarded
        300220010 incoming packets delivered
        261997230 requests sent out
        12 dropped because of missing route
        67 fragments received ok
        134 fragments created
    Icmp:
        8693034 ICMP messages received
        ...

Display PID and network state
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

=================  ========================================
 option             description
=================  ========================================
 -p, --programs     display PID/Program name for sockets
=================  ========================================

.. code-block:: console

    $ netstat -pt
    Active Internet connections (w/o servers)
    Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
    tcp        1      0 mvnl.csie.ntu.edu:45006 economy.canonical.:http CLOSE_WAIT  11988/python    
    tcp        1      0 mvnl.csie.ntu.edu:59399 economy.canonical.:http CLOSE_WAIT  8635/python

Display routing information
~~~~~~~~~~~~~~~~~~~~~~~~~~~

===============  ==========================
 option           description
===============  ==========================
-r, --route       display routing table
===============  ==========================

.. code-block:: console

    $ netstat -r    # same as ``route`` command
    Kernel IP routing table
    Destination     Gateway         Genmask         Flags   MSS Window  irtt Iface
    default         140.112.91.254  0.0.0.0         UG        0 0          0 eth0
    140.112.91.0    *               255.255.255.0   U         0 0          0 eth0
    link-local      *               255.255.0.0     U         0 0          0 eth0
    169.254.95.0    *               255.255.255.0   U         0 0          0 usb0

``arping`` Send ARP request to a neighbour host
-----------------------------------------------

==============  ====================================
 option          description
==============  ====================================
 -I device       which ethernet device to use (eth0)
==============  ====================================

.. code-block:: console

    $ arping -I eth0 140.112.91.210
    ARPING 140.112.91.210 from 140.112.91.208 eth0
    Unicast reply from 140.112.91.210 [5C:F3:FC:2A:80:3F]  0.754ms
    Unicast reply from 140.112.91.210 [5C:F3:FC:2A:80:3F]  0.763ms
    ...

``dhclient`` DHCP Client
------------------------

Request a IP address from DHCP server

=============  ======================
 option         description 
=============  ======================
 -v --verbose   Enable verbose
=============  ======================

.. code-block:: console

    $ dhclient -v eth0

``ethtool`` Query or control network driver and hardware
--------------------------------------------------------

==============  ========================================
  option         description
==============  ========================================
 -i, --driver     Display interface driver information
==============  ========================================

.. code-block:: console

    $ ethtool eth0
    $ ethtool -i eth0
