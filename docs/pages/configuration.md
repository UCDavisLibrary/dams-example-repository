---
layout: page
title: Configuration
permalink: /configuration/
---

The first step is to clone the repository, using:

```bash
git clone https://github.com/UCDavisLibrary/fin-example-repository.git
cd fin-example-repository          # change directory to your cloned location
```

This will create a local copy of some example data and configurations for
testing the server. Looking around these files, you'll see some configuration
files, some data and some metadata files mostly located within the collection
directory

The first thing we need to do is setup some environment variables for our first
setup. Look at the `fin-example.env` file. The file will look something like the file
below:

```bash
# server.env
FIN_URL=http://localhost:3000
FIN_ALLOW_ORIGINS=mylibrary.org,localhost
JWT_SECRET=lax
JWT_ISSUER=mylibrary.org
JWT_TTL=36000
JWT_VERBOSE=1
```

- `FIN_URL`  describes where the server will start.  By default we'll start it
   on a localhost with a development port.  If would like to see your server
   from other locations, modify this to a publicly accessible location.  See the
   section below for running on default ports.

- `FIN_ALLOW_ORIGINS` describes what domains can access your system.

- `JWT_SECRET` is a secret used between servers to verify authentication. The
  `JWT_SECRET` is used to sign jwt tokens. Computers with this secret can
  very easily create tokens that would be accepted by the system, so it's **very
  important** to protect this secret in production environments.

- `JWT_ISSUER` describes the service that issued the jwt token. While, it is checked by
  the servers, is not a source of security.

If you are happy with this configuration, let's go ahead and startup you system.
We are using docker-compose to startup the services described in our `fin.yml`
file.

``` bash
docker-compose -f fin-example.yml up -d
```

*Docker pros will notice we are avoiding using the standard location for the
`docker-compose.yml` file, and in addition we explicitly identify the
environment file in our setup. This is just to make it more clear what files are
being used.*

This will take some time the first time, as multiple docker containers are
pulled to your computer from docker hub.  The next time you run this, it will go
much faster.  You can try that now, but turning your setup off and on.  It
should startup much faster.

``` bash
docker-compose -f fin-example.yml down
docker-compose -f fin-example.yml up -d
```

At this point, you should be able to navigate the where you set `FIN_URL`, eg
`http://localhost:3000/` and you should see an empty repository.

Going back to your docker configuration, at this point you should be able to
examine the process that you are running, the logs, and other standard
docker-compose commands.  For example, if you look at all the containers you've
started with `docker-compose -f fin-example.yml ps`, you will see that there are
a number of services started, including a fedora instance, an IIIF server, and a
host of others.

Throughout these examples, we will also show direct access to the underlying LDP
as well. The default base for access to the LDP is /fcrepo/rest, so try
accessing `http://localhost:3000/fcrepo/rest`. This should fail, since by default
the public is not granted access to the data.  Since we want to read and write
data to this repository, let's next create a new user for the system.
