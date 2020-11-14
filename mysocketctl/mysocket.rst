.. index:: ! Introduction


Introduction
============

``mysocketctl`` is a cli tool that allows you to easily manage and and use the Mysocket services. 
``mysocketctl`` is a python tool that uses api.mysocket.io REST api to configure the various objects needed to use the services. 
Using ``mysocketctl`` users can create and manage their account, as well as manage sockets and tunnels and easily connect to the service. 

::

    $ mysocketctl.py
    Usage: mysocketctl.py [OPTIONS] COMMAND [ARGS]...

    Options:
    --help  Show this message and exit.

    Commands:
    account  Create a new account or see account information.
    connect  Quckly connect, Wrapper around sockets and tunnels
    login    Login to mysocket and get a token
    socket   Manage your global sockets
    tunnel   Manage your tunnels



About this Documentation
------------------------

The goal is for the documentation to be continuously updated and improved.

.. note:: You can contribute to this documentation by opening an issue
          or sending patches via pull requests on the `GitHub
          source repository <https://github.com/atoonk/mysocketdocs/>`_.


Installing Mysocketctl
============
The code for mysocketctl can be found here ... 

The quick way, download the executable
-------------------------------
For those who want to get started quickly a binary for Mac OSx and Linux is available. 

::

    curl ...
    chmod +x ./mysocketctl


Using Pip
-------------------------------
Or use pip to install mysocketctl
::
    pip3 install mysocketctl


Account management and login
============

::

    $ mysocketctl.py account --help
    Usage: mysocketctl.py account [OPTIONS] COMMAND [ARGS]...

    Create a new account or see account information.

    Options:
    --help  Show this message and exit.

    Commands:
    create  Create your new mysocket.io account
    show    Show your mysocket.io account information

Creating an account
---------------------------
To use mysocket.io users will need to register and create an account. 
Use the following command to create an account. Make sure to use a valid email address as we’ll use it to send you an email to validate your account.

We need an ssh public key as well, this is what we later use to setup and authenticate tunnels. 
If you don’t know how to create an ssh key pair, please see this link:

https://git-scm.com/book/en/v2/Git-on-the-Server-Generating-Your-SSH-Public-Key

Make sure to upload your public key only.

::

    mysocketctl.py account create \
        --name "your_name" \
        --email "your_email_address" \
        --password "a_secure_password" \
        --sshkey "$(cat ~/.ssh/id_rsa.pub)"

You should receive an email now with a confirmation link. Please click the link to validate your email account. After that, you can login


Logging in and get a token
--------------------------------
In order to use the service please login like below
::
    mysocketctl.py login \
        --email "your_email_address" \
        --password "a_secure_password" \

    Logged in! Token stored in /Users/johndoe/.mysocketio_token

The login process returns a jwt token that is stored in a ``.mysocketio_token`` file located in the users home directory. Going forward, ``mysocketctl`` will use this token to authenticate with the API. Currently, the token is valid for 300 minutes, ie. 5hrs. 
The user will need to re-issue a login request when the token has expired.

Account information
----------------------------
To see information about your account, use the following command.
::
    mysocketctl account show
    +-----------------------------------------------------------------+
    | Name         | Andree Toonk                                     |
    | Email        | blabla@gmail.com                                 |
    | user id      | b2f1b59f-bcba-4286-9818-9f0b6e685e93             |
    | ssh username | b2f1b59fbcba428698189f0b6e685e93                 |
    | ssh key      | ssh-rsa <your public key....SNIP TOO lONG>       |
    +-----------------------------------------------------------------+

Quick connect options
============================
The quick-connect function allows users to quickly, ie. in one command:

1. Create a socket

2. Create a tunnel

3. Make a local service available by connecting the tunnel to mysocket.


This quick connect feature is useful for when you want to make a local service available quickly. Later on we’ll look at how to configure and manage all the individual components.
Every time the connect feature is used, a new socket and, corresponding DNS name is created. If you need more permanent names, please look at creating sockets and tunnels separately. 
::
    mysocketctl.py connect --help
    Usage: mysocketctl.py connect [OPTIONS]

    Quckly connect, Wrapper around sockets and tunnels

    Options:
    --port INTEGER                 Local port to connect  [required]
    --name TEXT
    --protected TEXT
    --protected / --not-protected
    --username TEXT
    --password TEXT
    --type TEXT                    Socket type, http, https, tcp, tls
    --help                         Show this message and exit.

In the example bellow, we’ll connect our local port 8000 to the mysocket service.
Mysocket.io will automatically create a socket with a DNS name for you. It will also create a tunnel, which ``mysocketctl`` will use to connect to automatically. 

::

    mysocketctl.py connect \
        --port 8000 \
        --name "my test service"
    +--------------------------------------+--------------------------------------+-----------------+
    |              socket_id               |               dns_name               |       name      |
    +--------------------------------------+--------------------------------------+-----------------+
    | d84515f7-5c6e-4970-83bb-e25c1ca8cf16 | muddy-darkness-2030.edge.mysocket.io | my test service |
    +--------------------------------------+--------------------------------------+-----------------+

    Connecting to Server: ssh.mysocket.io

    Welcome to Mysocket.io!
    my test service - https://muddy-darkness-2030.edge.mysocket.io

    =======================================================
    Logs
    =======================================================
    ....


In this case, a socket with the name muddy-darkness-2030.edge.mysocket.io was created. Using your browser, you can now visit this socket which is automatically connected to the http service running on your localhost port 8000. 
Note, to test this, you can quickly start a localhost http server on port 8000 like this:

``python3 -m http.server 8000``

All requests are logged and shown in the ``mysocketctl`` terminal.

``Ctrl-c`` will cause the ssh tunnel to disconnect.  Mysocketctl will automatically reconnect the tunnel, this is to recover from possible network issues. 
To end the quick connect session press ``ctrl-c`` twice. 
This will make sure the socket objects are automatically deleted, so you won’t hit any of the account limits.
::
    ^C  (ctr-c)
    Connection to ssh.mysocket.io closed.
    Disconnected... Automatically reconnecting now..
    Press ctrl-c to exit
    ^C (ctr-c)
    Bye
    cleaning up…

Socket Management
========================
Sockets are the public endpoint that mysocket creates on behalf of users. Each socket will come with a unique DNS name.
There are three types of socket supported today:

1. **http/https**. Use this when your local service is a http service. 

2. **TCP**. Use this when your local service is a non-http service. In this case mysocket will proxy a raw tcp session. This is used for example for ssh or https services. Note that in this case mysocket will, in addition to a unique DNS name, also create a TCP port number just for your service.

3. **TLS**. This is a TLS encrypted TCP socket. This is great to, for example, make your local mysql service available over TLS.

::

    mysocketctl socket --help
    Usage: mysocketctl.py socket [OPTIONS] COMMAND [ARGS]...

    Manage your global sockets

    Options:
    --help  Show this message and exit.

    Commands:
    create
    delete
    ls

Creating sockets
--------------------
The command below creates an http socket of type http. It returns the socket_id and dns name. 
::
    mysocketctl.py socket create \
        --name "my local http service" \
        --type http
    +--------------------------------------+-----------------------------------+---------+------+-----------------------+
    |              socket_id               |              dns_name             | port(s) | type |          name         |
    +--------------------------------------+-----------------------------------+---------+------+-----------------------+
    | 506182d3-1109-4d94-96f1-3bd7b0de68a9 | frosty-rain-6381.edge.mysocket.io |  80 443 | http | my local http service |
    +--------------------------------------+-----------------------------------+---------+------+-----------------------+

For http based services, we can add password protection to the socket. This means that the user will see a username password window before visiting your socket service. Below an example of creating a password-protected socket, with username john and password secret.
::
    mysocketctl.py socket create \
        --name "my local http service" \
        --type http \
        --protected \
        --username john \
        --password secret
    +--------------------------------------+---------------------------------+---------+------+-----------------------+
    |              socket_id               |             dns_name            | port(s) | type |          name         |
    +--------------------------------------+---------------------------------+---------+------+-----------------------+
    | 5870a362-65d3-474d-bbf6-3341827eaee0 | dark-darkness-6275.edge.mysocket.io |  80 443 | http | my local http service |
    +--------------------------------------+---------------------------------+---------+------+-----------------------+

    Protected Socket, login details:
    +----------+----------+
    | username | password |
    +----------+----------+
    | john     | secret   |
    +----------+----------+


Listing all sockets
-----------------------
To see all your socket, issue the socket ls command like below:

::

    mysocketctl.py socket ls
    +--------------------------------------+-----------------------------------------+------+---------+-----------------------+
    | socket_id                            | dns_name                                | type | port(s) | name                  |
    +--------------------------------------+-----------------------------------------+------+---------+-----------------------+
    | f441738c-4f77-44d5-bc68-99664f272319 | restless-night-1301.edge.mysocket.io    | http | 80 443  | Local port 44         |
    | 12967b8a-ccca-4a84-87e6-2443daed5fe5 | frosty-wildflower-4938.edge.mysocket.io | http | 80 443  | andree was here       |
    | 05da6711-c2c7-4c53-b213-21ea9a3d1db6 | ancient-voice-2982.edge.mysocket.io     | http | 80 443  | Local port 8000       |
    | 5870a362-65d3-474d-bbf6-3341827eaee0 | wild-pine-1229.edge.mysocket.io         | http | 80 443  | my local http service |
    +--------------------------------------+-----------------------------------------+------+---------+-----------------------+

Delete sockets
----------------------
To delete a socket, issue the socket delete command and provide the socket_id you wish to delete.
::
    mysocketctl.py socket delete \
        --socket_id 5870a362-65d3-474d-bbf6-3341827eaee0

    Socket 5870a362-65d3-474d-bbf6-3341827eaee0 deleted

Tunnel Management
=========================
In the previous section, we looked at managing sockets. Sockets are created on the mysocket servers and serve as the public endpoint for your local services. In order to connect your local service to the mysocket socket we need tunnels. 
In this section, we’ll explain how to manage tunnels and how to connect the tunnels. Tunnels provide the connection between your local service and the globally anycasted public sockets for you. Currently, we support ssh as a transport protocol for secure connectivity between your local services and mysocket.
Note that a socket can have multiple tunnels. In that case mysocket will load balance over all available tunnels.
::

    mysocketctl.py tunnel --help
    Usage: mysocketctl.py tunnel [OPTIONS] COMMAND [ARGS]...

    Manage your tunnels

    Options:
    --help  Show this message and exit.

    Commands:
    connect
    create
    delete
    ls

Creating a tunnel
---------------------
The command below creates a new tunnel for a socket we create earlier. 
::
    mysocketctl.py tunnel create \
        --socket_id 334c2e48-8324-47c0-9b03-c0a69c2c7833
    +--------------------------------------+--------------------------------------+---------------+------------+
    | socket_id                            | tunnel_id                            | tunnel_server | relay_port |
    +--------------------------------------+--------------------------------------+---------------+------------+
    | 334c2e48-8324-47c0-9b03-c0a69c2c7833 | dc620ec5-76d6-455e-865c-eac238472bee |               | 6054       |
    +--------------------------------------+--------------------------------------+---------------+------------+

Note that the mysocket API returned a tunnel_id and a relay port. The relay port is used when connecting the tunnel, it’s used as the SSH listener port. 

Listing all tunnels for a socket
--------------------------------
To see all tunnels for a socket, issue the ``mysocket tunnel ls`` command like below:
::
    mysocketctl.py tunnel ls \
        --socket_id 334c2e48-8324-47c0-9b03-c0a69c2c7833
    +--------------------------------------+--------------------------------------+---------------+------------+
    | socket_id                            | tunnel_id                            | tunnel_server | relay_port |
    +--------------------------------------+--------------------------------------+---------------+------------+
    | 334c2e48-8324-47c0-9b03-c0a69c2c7833 | 4f1d5c81-1531-4b93-9343-76b5c16194dc | 52.13.204.31  | 6043       |
    | 334c2e48-8324-47c0-9b03-c0a69c2c7833 | dc620ec5-76d6-455e-865c-eac238472bee |               | 6054       |
    +--------------------------------------+--------------------------------------+---------------+------------+

The tunnel server field indicates what server the tunnel was last connected to.

Deleting a tunnel
---------------------
To delete a tunnel, issue the tunnel delete command and provide the socket_id and tunnel_id you wish to delete.
::
    mysocketctl.py tunnel delete \
        --socket_id 334c2e48-8324-47c0-9b03-c0a69c2c7833 \
        --tunnel_id dc620ec5-76d6-455e-865c-eac238472bee

    Tunnel dc620ec5-76d6-455e-865c-eac238472bee deleted


Connecting and using a tunnel
------------------------

In order to spin up your tunnel, the ``mysocketctl tunnel connect`` feature may be used.
::
    mysocketctl.py tunnel connect --help
    Usage: mysocketctl.py tunnel connect [OPTIONS]

    Options:
    --socket_id TEXT  [required]
    --tunnel_id TEXT  [required]
    --port TEXT       [required]
    --help            Show this message and exit.

It requires socket_id and tunnel_id as mandatory arguments. It also needs to know what port number the local service listens on. This can be any local TCP port, as long as you have something listening on it.
For example, if you have a local webservice, you want to make publicly available using this tunnel in port 8000 then provide 8000 as the ``--port`` parameter.
If you wanted to make ssh available and the socket you created is of type TCP, then provide port 22 as the port parameter.
::
    mysocketctl.py tunnel connect \
        --socket_id 334c2e48-8324-47c0-9b03-c0a69c2c7833 \
        --tunnel_id 4f1d5c81-1531-4b93-9343-76b5c16194dc \
        --port 8000

    Connecting to Server: ssh.mysocket.io

    Welcome to Mysocket.io!
    Local port 44 - https://white-dew-2957.edge.mysocket.io

    =======================================================
    Logs
    =======================================================


After issuing the tunnel connect command, ``mysocketctl`` calls ssh and sets up the SSH tunnel to ssh.mysocket.io. This is an anycasted ssh service, so users will always use the closest, lowest latency, mysocket ssh server.  Once connected, the mysocket control plane will signal in real-time all other servers where this tunnel is. As a result, you can re-use the tunnel from multiple endpoints, but only the latest login will be used for traffic. If you would like to load balance over multiple ssh sessions, simply create multiple tunnel connections first.

The stop the tunnel session, press ``ctr-c``.


















