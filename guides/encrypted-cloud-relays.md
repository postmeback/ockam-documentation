---
description: End-to-end encrypted, secure and private cloud relays – for any application.
---

# Encrypted Cloud Relays



```
Let's walk through a simple example to create an end-to-end encrypted,
mutually authenticated, secure and private cloud relay – for any application.

First let's enroll with Ockam Orchestrator where we'll create a managed cloud
based relay that will move end-to-end encrypted  data between distributed parts
of our application.

# Create a cryptographic identity and enroll with Ockam Orchestrator.
# This will sign you up for an account with Ockam Orchestrator and setup a
# hobby space and project for you.
$ ockam enroll

You can also create encrypted relays outside the orchestrator.
See `ockam forwarder --help`.

Application Service
------

Next let's prepare the service side of our application.

# Start our application service, listening on a local ip and port, that clients
# would access through the cloud relay. We'll use a simple http server for our
# first example but this could be some other application service.
$ python3 -m http.server --bind 127.0.0.1 5000

# Setup an ockam node, called blue, as a sidecar next to our application service.
$ ockam node create blue

# Create a tcp outlet on the blue node to send raw tcp traffic to the application service.
$ ockam tcp-outlet create --at /node/blue --from /service/outlet --to 127.0.0.1:5000

# Then create a forwarding relay at your default orchestrator project to blue.
$ ockam forwarder create blue --at /project/default --to /node/blue

Application Client
------

Now on the client side:

# Setup an ockam node, called green, as a sidecar next to our application service.
$ ockam node create green

# Then create an end-to-end encrypted secure channel with blue, through the cloud relay.
# Then tunnel traffic from a local tcp inlet through this end-to-end secure channel.
$ ockam secure-channel create --from /node/green \
    --to /project/default/service/forward_to_blue/service/api \
        | ockam tcp-inlet create --at /node/green --from 127.0.0.1:7000 --to -/service/outlet

# Access the application service though the end-to-end encrypted, secure relay.
$ curl 127.0.0.1:7000

We just created end-to-end encrypted, mutually authenticated, and authorized
secure communication between a tcp client and server. This client and server
can be running in separate private networks / NATs. We didn't have to expose
our server by opening a port on the Internet or punching a hole in our firewall.

The two sides authenticated and authorized each other's known, cryptographically
provable identifiers. In later examples we'll see how we can build granular,
attribute-based access control with authorization policies.
```











```bash
# Install Ockam
$ brew install build-trust/ockam/ockam

# Create an ockam node, called cloud-relay, that will relay
# end-to-end encrypted messages. Later we'll see how we can get such a relay
# as a managed cloud service with Ockam Orchestrator.
$ ockam node create cloud-relay

# --- APPLICATION SERVICE ----

# An application service, listening on a local ip and port, that clients would
# access through the cloud relay. We'll use a simple http server for our example.
$ python3 -m http.server --bind 127.0.0.1 5000

# Setup an ockam node, called blue, next to our application service. Create a
# tcp outlet on the blue node to send raw tcp traffic to the application service.
# Then create a forwading relay on the cloud-relay node for blue.
$ ockam node create blue
$ ockam tcp-outlet create --at /node/blue --from /service/outlet --to 127.0.0.1:5000
$ ockam forwarder create --at /node/cloud-relay --from /service/forwarder-for-blue --for /node/blue

# --- APPLICATION CLIENT ----

# Setup an ockam node, called green, for use by an application client.
# Create an end-to-end encrypted secure channel with blue, through the cloud relay.
# Then tunnel traffic from a local tcp inlet through this end-to-end secure channel.
$ ockam node create green
$ ockam secure-channel create --from /node/green --to /node/cloud-relay/service/forwarder-for-blue/service/api \
    | ockam tcp-inlet create --at /node/green --from 127.0.0.1:7000 --to -/service/outlet

# Access the application service though the end-to-end encrypted, secure relay.
$ curl 127.0.0.1:7000
```