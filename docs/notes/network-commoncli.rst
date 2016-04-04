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

``tcpdump`` capture packets
----------------------------

===========  ===============================
  option      description
===========  ===============================
  -w          Write the raw packets to file
  -i          Listen on interface
===========  ===============================

.. code-block:: bash

    $ tcpdump -w test.pcap -i eth0
    $ tcpdump -w dns.pcap -i eth0 dst port 53
    $ tcpdump -w local.pcap -i eth0 host 192.168.1.43


DNS related commands
----------------------

nslookup
~~~~~~~~~

.. code-block:: bash

    $ nslookup google.com
    Server:     192.168.1.1
    Address:    192.168.1.1#53

    Non-authoritative answer:
    Name:   google.com
    Address: 173.194.72.102
    Name:   google.com
    Address: 173.194.72.113
    Name:   google.com
    Address: 173.194.72.101
    Name:   google.com
    Address: 173.194.72.139
    Name:   google.com
    Address: 173.194.72.100
    Name:   google.com
    Address: 173.194.72.138

dig
~~~~

.. code-block:: bash

    $ dig google.com

    ; <<>> DiG 9.8.3-P1 <<>> google.com
    ;; global options: +cmd
    ;; Got answer:
    ;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 34606
    ;; flags: qr rd ra; QUERY: 1, ANSWER: 6, AUTHORITY: 0, ADDITIONAL: 0

    ;; QUESTION SECTION:
    ;google.com.            IN  A

    ;; ANSWER SECTION:
    google.com.     47  IN  A   173.194.72.100
    google.com.     47  IN  A   173.194.72.138
    google.com.     47  IN  A   173.194.72.102
    google.com.     47  IN  A   173.194.72.113
    google.com.     47  IN  A   173.194.72.101
    google.com.     47  IN  A   173.194.72.139

    ;; Query time: 7 msec
    ;; SERVER: 192.168.1.1#53(192.168.1.1)
    ;; WHEN: Mon Apr  4 20:05:37 2016
    ;; MSG SIZE  rcvd: 124

whois
~~~~~

.. code-block:: bash

    $ whois google.com

avahi-browse
~~~~~~~~~~~~~

.. code-block:: bash

    # browse local service(service discovery)
    $ avahi-browse -a

    # resolve local DNS
    $ avai-browse -ar

    $ browse local domain service
    $ avahi-browse -d local _workstation._tcp

avahi-resolve
~~~~~~~~~~~~~~

.. code-block:: bash

    # resolve local machines
    $ avahi-resolve -n machine.local

dns-sd
~~~~~~~

only on Mac

===========  =============================================
  option      description
===========  =============================================
  -B          browse for instances of service
  -q          look up any DNS name, resource record type
===========  =============================================

.. code-block:: bash

    # Format: dns-sd -B type domain
    $ dns-sd -B _smb._tcp

    # Format: dns-sd -q name rrtype rrclass
    $ dns-sd -q machine.local
    $ dns-sd -q www.google.com
