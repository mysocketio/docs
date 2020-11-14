.. index:: ! Introduction


Introduction
============

Welcome to the documentation for Mysocket.io, a service that provides you with
fast and secure network connectivity whenever you need it, wherever you are.  



About Mysocket
----------------------------------------
Mysocket.io is a service that provides public endpoints for services that are otherwise not publicly reachable. A typical example is a web service running on your laptop, which you’d like to make available to your client. Or ssh access to servers behind NAT or a firewall, like a raspberry pi on your home network. Mysocket.io is a fully managed cloud service, so nothing to run!


About this Documentation
------------------------

The goal is for the documentation to be continuously updated and improved. 

.. note:: You can contribute to the documentation by opening an issue
          or sending patches via pull requests on the `GitHub
          source repository <https://github.com/atoonk/mysocketdocs/>`_.

Quick start start
=========================
More documentation can be found below; but if you're eager to get started, consider this a quick start.
Download the mysocketctl (MacOSx and Linux)
Create an account:
::

    mysocketctl.py account create \
        --name "your_name" \
        --email "your_email_address" \
        --password "a_secure_password" \
        --sshkey "$(cat ~/.ssh/id_rsa.pub)"

After confirming your new account (check your email), login and retrieve an access token:
::

    mysocketctl.py login --email \
        --email "your_email_address" \
        --password "a_secure_password" \

Now you’re ready to use the “quick connect” feature to connect your local service listening on port 8000  to the Internet:
::
    mysocketctl.py connect \
        --port 8000 \
        --name "my test service"

    +--------------------------------------+---------------------------------+-----------------+
    |              socket_id               |             dns_name            |       name      |
    +--------------------------------------+---------------------------------+-----------------+
    | 24cd3fa2-b56a-486f-a554-30924bae54d0 | long-hill-8399.edge.mysocket.io | my test service |
    +--------------------------------------+---------------------------------+-----------------+

    Connecting to Server: ssh.mysocket.io

    Welcome to Mysocket.io!
    my test service - https://long-hill-8399.edge.mysocket.io

    =======================================================
    Logs
    =======================================================
    <live stream of your logs here>

To quickly test if it’s working, you can start a web service on localhost port 8000 like this. 

``python3 -m http.server 8000``


Now visit the URL (dns_name) using your browser, and you’ll see that the localhost service you just started is now globally available!


Features
=========================
Stable public DNS names and port numbers for your private apps. 

Supports various socket types, including:
    1. http

    2. https

    3. TCP

    4. TLS


Options for SSL/TLS Encryption for your sockets

All sockets run on a global anycast network, reducing latency and improving the user experience.

Username and Password protected (http/https) Sockets 

Live Stream of logs. We show you all requests in real-time, including the latency between our anycasted nodes and your origin server.

Support for multiple origins per socket, ie. Load Balancing

Build on a global anycast network
================================
Mysocket.io is built on a global anycasted network of **91 Points of Presence in 80 cities across 42 countries.** This helps you improve the availability and performance of the applications that you offer to your global users.  
Mysocket.io application services connect to use anycast network using various servers in North America, Europe, and Asia.  All this provides us with the best possible low latency user experience and Instant regional failover, which results in an incredible level of high availability.

Example use cases
=========================

Make the local web service on your laptop available to your colleagues or client.
-----------------------------
You may prefer to do web development on your laptop, and, before publishing it to some public server, would like to share it quickly with your teammate or client. Using Mysocket.io you can make the web app running on localhost, publicly available to anyone on the Internet. Just share the mysocket.io generated URL with those with who you’d like to share it. If you’d like, you can even make it password protected.

Access your raspberry pi at home from anywhere on the Internet
--------------------------------
You have a small lab at home, perhaps with a raspberry pi or Intel nuc. Since these are behind your NAT router you can’t normally SSH into them. By using Mysocket.io you can make the SSH services on your home server available by tunneling TCP traffic through the tunnel seamlessly through NAT. Mysocket.io will provide a public DNS name and port number, which can be used to SSH into your server from anywhere.

A global stable public endpoint for your ephemeral resources.
-------------------------------
Your containers come and go, perhaps even distributed over various public clouds as well as your private datacenter. It can be challenging to provide a stable public endpoint for these ephemeral and mobile services. With mysocket.io you can create a public endpoint, either an http/https, or TCP, TLS endpoint. Now each time a new container comes up, it can connect to the mysocket.io service and register as a new origin (backend) server. You can have one, or many of these origin services per public socket.

Interacting with the Mysocket.io service
=============================
The easiest way to get started with the service is by using the mysocketctl cli tool. More details about that can be found here. 
All interaction with our services is done using our RESTful API. You can find the API and the API specifications at https://api.mysocket.io/  The mysocketctl tool uses this API to interact with the service.
