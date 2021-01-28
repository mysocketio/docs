.. index::
  ! FAQ
  see: Frequently Asked Questions; FAQ


FAQ
+++

.. note::  The
           contents are available on `Github <https://github.com/mysocketio/docs/blob/master/about/faq.rst>`_,
           allowing you to send a pull request with edits or additions, or fork the
           contents for usage elsewhere.


What is Mysocket?
====================
Mysocket.io, a service that provides you with fast and secure network connectivity whenever you need it, wherever you are. It provides secure and stable public anycasted TCP endpoints for dynamic services or services that are otherwise not publicly reachable! 

What can I do with Mysocket?
====================
There are many examples, but in short you can extend reachability to TCP sockets that run within your network or just your laptop, to a global audiance. 

What are identity aware sockets?
====================
These are sockets that are aware of the visitor's identity. If your socket is enabled for "cloud authentication," all visitors will be prompted to authenticate first. Authentication can be completed using the various social Identity providers (Google, Github, Facebook), as well as local accounts. As the owner of the socket, you can then specify authorization rules, allowing only authenticated users with certain email domains or specific email addresses.
For more information see this article `identity-aware sockets <https://www.mysocket.io/post/introducing-identity-aware-sockets-enabling-zero-trust-access-for-your-private-services>`_

what happened to the Python client?
====================
We changed out the python3 mysocketctl client in favor of the new Golang mysocketctl client. 
The python code is still available and can be installed using ``pip3 install mysocketctl``
The python code can be found here:
https://github.com/mysocketio/mysocketctl

It's recommended, however, to use the Go client, which can be downloaded here: https://download.edge.mysocket.io/



What performance improvement does Mysocket provide?
====================
Because mysocket is an anycasted service, both the end-user and the tunnel connection is automatically terminated at the nearest mysocket server. 
We also use TCP BBR, further improving the perfomance characteristics of the TCP connection

Where is Mysocket deployed today?
====================
Mysocket.io is built on a global anycast network of 91 Points of Presence in 80 cities across 42 countries. 
The actual tunnel and api servers are deployed throughout North America, Europe and Asia.


What kind of support is provided?
====================
Today support is best effort. 


Q: How do I get started with Mysocket?
====================
The best way to get started is to follow the details in this blog: https://www.mysocket.io/post/introducing-mysocket
or see: https://mysocket.readthedocs.io/en/latest/about/about.html#quick-start

Q: What kind of transport security is used between the mysocket.io and the origin.
====================
We currently support SSHv2 as the transport and tunneling protocol. It encrypts all traffic to eliminate eavesdropping, connection hijacking, and other attacks.

Q: If I only have one origin server, how do I benefit from the anycast features.
====================
Using anycast your users will be routed to our closest proxy service (located in Asia, Europe and North America). From there on we make sure traffic is sent to the tunnel server. So we ingest your users traffic as close to the user as possible. This lower Round Trip time helps improve the user experience.



