
# Fin Example Repositories

This project contains a set of example repositories for the UC Davis digital
archive management system [DAMS](https://digital.ucdavis.edu).  These examples
are often, but not always found on the [DAMS
Sandbox](https://sandbox.dams.library.ucdavis.edu), a test DAMS for UC Davis.

This example repository is meant to fill two roles. First, there are a number of
different example repositories that illustrate various aspects of how we
organize data in the DAMS and methods we use to add data into the DAMS.  In
addition to the organization, we also use these examples to illustrate various
ervices that are available through our DAMS.  These example repositories then
attempt to standardize how data should be added to the system.  Each individual
collection has it's own README that documents some of the features of our DAMS.
There are a few examples that should probably be done in order, [Pets] and
[Photos]. After that, the examples can be looked at in pretty much any order.


There are also a number of
[FAQS](https://github.com/UCDavisLibrary/fin-example-repository/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3AFAQ)
in the Issues. You may find help there as well.

- [Fin Example Repositories](#fin-example-repositories)
- [Installation](#installation)
  - [Prerequisites](#prerequisites)
  - [Purging the System](#purging-the-system)
  - [Configuration](#configuration)
  - [Adding Your First User](#adding-your-first-user)
    - [Basic-Auth](#basic-auth)
    - [CAS Authentication](#cas-authentication)
  - [Accessing the LDP Server](#accessing-the-ldp-server)
  - [Adding Data to the Repository](#adding-data-to-the-repository)
- [Advanced Configuration](#advanced-configuration)
  - [Run Server on Default Ports](#run-server-on-default-ports)
- [Example Collections](#example-collections)

# Installation

Follow the steps below you should be able to get your own server up and
running relatively quickly.  From there, you can investigate some of the example
repository data.

Currently, we assume some knowledge of what an LDP is, what linked data means,
and how linked metadata is created and maintained. We also assume you have some
familiarity with linked data turtle and json-ld formats.  We are looking to
expand the tutorial material in those areas.

[fin-server](https://github.com/UCDavisLibrary/fin-server), extended Fedora
Linked Data platform [Fedora-LDP](https://fedora.info/spec/).

The first step is to get your example server up and running. This setup uses
docker and a suite of docker containers to run the system.  After installation
you will have an test server up and running, and ready to add collections.

## Prerequisites

First, clone the [fin-ucd-lib-deployment](https://github.com/UCDavisLibrary/fin-ucd-lib-deployment).
This is the engine your own instance of the DAMS will run on. Please follow the steps documented in
[Running a Deployment](https://github.com/UCDavisLibrary/fin-ucd-lib-deployment#running-a-deployment)
to launch your own local DAMs.

Now, you can clone this repository. This is a good way to get your
example data downloaded, and it is also a good starting point to fork this
repository, later when you want to save site specific preferences. So, we'll
need have have `git` on your development machine. If you're new to git, you my
follow the installation instructions in this [GIT Book](https://git-scm.com/),
or perhaps try github's [desktop](https://desktop.github.com/) application if
you are using windows. The examples below, will use `git`'s command line
features.

The server is implemented as suite of docker containers, so in order to run this
example, you need to have `docker` and `docker-compose` installed on our system.
Follow the [installation notes](https://docs.docker.com/compose/install/) from
docker, if you are new to docker.  **Note**, if you are new to docker,
understand that you will need to learn about this system.  Some of the example
configuration below, will be unclear, and it will be difficult to modify the
examples below for your setup unless  you understand this environment. Also, as of right now,
Docker Desktop will only work on Windows 10 Pro, Enterprise, and Education.
Support for Windows Home is dependent on having Windows 10 2004 installed
[Install Docker Desktop on Windows Home](https://docs.docker.com/docker-for-windows/install-windows-home/).

Finally, we will be running a nodejs based tool `fin-cli` to work with our
repository. After installing [nodejs](https://nodejs.org/en/download/), you can
install this tool with `npm install -g @ucd-lib/fin-cli` [installation notes](https://github.com/UCDavisLibrary/fin-cli).
After installing, make sure your fin-cli is configured properly.

1.Run the command `fin config`.
2.Run `fin config set host [hostname]`.
3.Run `fin config` again and you should be able to see that the host/base path has been updated.

## Purging the System

In order to purge your DAMS instance, you need to use docker-compose commands directly:

```bash
# This sets up an alias to docker-compose, dams-dc
eval $(damsctrl alias)
# You can erase your current setup, while maintaining your docker configuration with:
dams-dc down -v
# Now you can bring this back up
dams-dc up -d
```

At this point you have no admins for the site. The biggest change is that the admins are now managed in the repository's ACL.
This allows the rest of the configuration, even adding admins, to be done on any remote machine.

In order to add admins without any users you need to login in as the super-user. This uses the JWT_SECRET in the server
configuration file.

```bash
fin login --super-user USERNAME@ucdavis.edu
```

Once logged in, you can add some admins using the `fin acl` command:

```bash
for i in quinn jmerz enebeker ladragoo; do
  fin acl add-admin ${i}@ucdavis.edu
done
```

With these admins now in place you may now add new collections:

```bash
# Log-in using your normal (in our case CAS) ids:
fin login --headless
# Create a new collection
cd ~/fin-example-repo/collection
fin io import ex1-pets .
```

## Purging the System

In order to purge your DAMS instance, you need to use docker-compose commands directly:

```bash
# This sets up an alias to docker-compose, dams-dc
eval $(damsctrl alias)
# You can erase your current setup, while maintaining your docker configuration with:
dams-dc down -v
# Now you can bring this back up
dams-dc up -d
```

At this point you have no admins for the site. The biggest change is that the admins are now managed in the repository's ACL.
This allows the rest of the configuration, even adding admins, to be done on any remote machine.

In order to add admins without any users you need to login in as the super-user. This uses the JWT_SECRET in the server
configuration file.

```bash
fin login --super-user USERNAME@ucdavis.edu
```

Once logged in, you can add some admins using the `fin acl` command:

```bash
for i in quinn jmerz enebeker ladragoo; do
  fin acl add-admin ${i}@ucdavis.edu
done
```

With these admins now in place you may now add new collections:

```bash
# Log-in using your normal (in our case CAS) ids:
fin login --headless
# Create a new collection
cd ~/fin-example-repo/collection
fin io import ex1-pets .
```

## Configuration

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

## Adding Your First User

### Basic-Auth

Our container setup separates the authentication step from the Fedora and
other services.  The services rely on valid JWT tokens being sent along with
requests to the system.  The Authentication services are what creates these
tokens.  In production, you will most certainly want to use a centralized
authentication mechanism, see [CAS Authentication](#CAS-Authentication) in the [Advanced Configuration](#Advanced-Configuration) section to see an example of this type of setup.  However, for
testing, we have included a Basic Authentication service that can be used
without any external setup.  You should really only use this service for
testing.

Earlier, we looked at some of the container processes we were running with
`docker-compose -f fin-example.yml ps`. One process there, is there is the
basic-auth service.  In addition, by default we allow anyone to [create a new
user account](http://localhost:3000/auth/basic/create.html). Navigate to that
location and create a new user.  The email address allows for password resets.
Once you've created your account, you can login to the server, and then from the
home page, you can verify that you are logged in via the lower right hand side
of the page.

``` bash
docker-compose -f fin-example.yml exec basic-auth node service/cli create-user -u quinn -p laxlax -e quinn@example.org
```

Now, we are going to give the user we've just created special administrative
privileges for our repository.  This cannot be done via the website, but can be
done with a command in your docker setup.  Assuming the user you've added, and
who will be your admin is `superman`, run the command below:

``` bash
docker-compose -f fin-example.yml exec server node app/cli admin add-admin -u quinn@local
```

This command adds the user `superman@local` into the group of administrators for
the repository.   Every user in our Basic-Auth setup is given the `@local`
suffix to differentiate them from other authentication systems.  Now in your
browser, logout and back in (using the links in the lower right), to give
yourself these new privileges.

***Pro Tip** if you understand the development setup of your browser, you will
see that this process has added a Cookie `fin-jwt` that encapsulates this
information.  For example, you could copy that jwt token and decode it, for
example by visiting [jwt.io](https://jwt.io/). You will see the values encoded
in the token.  You can even verify the signature if you include the `JWT_SECRET`
you've set in your configuration.  This should show you how easy it is to create
tokens if you know that `JWT_SECRET`, keep it hidden keep it safe!*

### CAS Authentication

The examples above includes a Basic Authentication service, but even in
development, we often don't use this service. Instead, we have an authentication
service using the CAS service used on our UC Davis campus. When using this
service, the authentication of the users is sent to the central CAS
authentication server, and if the user authenticates, the service will mint a
token for this users.  There aren't too many differences in the overall setup.
First, open the fin-example.yml file, remove the Basic-Authentication Service, and add the CAS service.

``` diff
--- fin-example.yml	2018-03-01 17:05:14.964623572 -0800
+++ fin-example-cas.yml	2018-03-01 17:05:26.192623341 -0800
@@ -103,10 +103,10 @@
       - server

   ###
-  # Basic Username/Password AuthenticationService
+  # CAS AuthenticationService
   ###
-  basic-auth:
-    image: ucdlib/fin-basic-auth:master
+  cas:
+    image: ucdlib/fin-cas-service:master
     env_file:
       - fin-example.env
     depends_on:
```

Then, when we create admins we make sure to use the `@ucdavis.edu` suffix for
these users, as in:

``` bash
docker-compose -f fin-example.yml exec server node app/cli admin add-admin -u quinn@ucdavis.edu
```

More information is available in the fin-server
[authentication-service](https://github.com/UCDavisLibrary/fin-server/blob/master/docs/authentication-service/README.md).
Implementors of alternative authentication schemes can look at the [CAS
Service](https://github.com/UCDavisLibrary/fin-server/tree/master/services/cas)
for a good example of how this can be implemented for new authentication
services.

## Accessing the LDP Server

Now that we have elevated privileges, let's revisit the root to the LDP services,
`http://localhost:3000/fcrepo/rest`. Now we should have access to this location.
There isn't anything here, but at least we can see that now.  Users familiar
with Fedora will note that this is the standard fedora interface when accessed
via the browser.

In many of the examples following, we will also be using the command-line tool
`fin`.  If you haven't already, you can install this tool with `npm install -g @ucd-lib/fin-cli`. If you've
already installed fin make sure you upgrade to the newest version `npm install -g @ucd-lib/fin-cli@0.10.0`.

The first time you use `fin`, you need to point to the server that you want to
interact with.  Run the command `fin shell`.  This will put you into an
interactive mode.  It will also prompt for a fedora endpoint.  Use the value to
match your FIN_URL, in our example `http://localhost:3000`.

Next, just as for the browser, we need to get a valid token for our command-line
server.  Within the fin shell try `login`.  This should launch a service to
verify your login credentials, and use those to add a valid token to your `fin`
cli.  If you have more complicated setup, you can use `login --headless` and
simply add in a jwt token, by cutting and pasting from your browser's
development environment for example.  Or if you know your JWT_SECRET, you can
mint a new token directly from the client, for example from this directory

``` bash
source fin-example.env; fin jwt encode --admin --save=true $JWT_SECRET $JWT_ISSUER quinn
```

Now that our `fin` client is set, we can use it to interrogate and modify our
server. In other examples, we will have more detail about the fin client, but
for now, this command will show the metadata related to the root of this
repository.

``` bash
fin http get -P b /
```

## Adding Data to the Repository

Now that we have our repository and we have administrative access, let's make
our first entry into the repository.

``` bash
fin http put -H prefer:return=minimal -H "Content-Type:text/turtle" -@ server.ttl -P h /
```

This adds the `server.ttl` to the metadata of our root repository.  We can
verify that in two ways, first using the command-line tool.

``` bash
fin http get -P b /
```

We can also verify in the browser, `http://localhost:3000/fcrepo/rest` open the
properties bar and verify we've updated the metadata.  The root metadata also
controls the information on the server.  Revisit, `http://localhost:3000/` you can
see that the description of the repository has changed.

***Pro tip** The `fin` cli has lots if specialized tools for accessing a fedora
server, but there is nothing special in the calls that are sent to fedora, they
are all standard HTTP requests. You can use other tools to interact with the
server. For example, you could use the popular [HTTPie](https://httpie.org/)
command line tool. Let's say you've followed the steps above, and your fin-cli
has a valid token. This is stored in the ~/.fccli file in your home directory.
If you wanted to use use httpie to interogate your system, you'd need to pass
that token along on your httpie requests as well. Httpie has a mechanism for
saving header information for certain locations, and you could set that up with
the following command `http --print=h --session=admin http://localhost:3000
"Authorization:Bearer $(jq -r .jwt < ~/.fccli)"`. This saves the Authorization
token to the `admin` session. Later on, we can access fedora with httpie like
this `http --session=admin http://localhost:3000/fcrepo/rest`.*

# Advanced Configuration

## Run Server on Default Ports

If you want to run this on the default port, the user running this example will
need permission to connect to that protected port. In our setup, we often have
multiple domains running on the same machine, and we use apache to map to this
setup. This allows you to run the server as a normal user.  Additionally, our
service does not directly support SSL, so you will need to do something similar
to this setup when you move to a production setup.

``` xml
<VirtualHost *:80>
  ServerName dams.mylibrary.org
  ServerAdmin admin@mylibrary.org
  RewriteEngine on
  RewriteRule ^ https://%{SERVER_NAME}%{REQUEST_URI} [END,QSA,R=permanent]
</VirtualHost>

<IfModule mod_ssl.c>
 <VirtualHost *:443>
        ServerName dams.mylibrary.org
        SSLEngine on

        Header set Access-Control-Allow-Origin "*"
#        SSLCertificateFile      /etc/ssl/certs/__name__.cer
#       SSLCertificateKeyFile /etc/ssl/private/__name__.key

       # Required for RIIIF pathnames
       AllowEncodedSlashes On
       ProxyPreserveHost On
       ProxyPass / http://localhost:3000/ nocanon
       ProxyPassReverse / http://localhost:3000/ nocanon
</VirtualHost>
</IfModule>
```

# Example Collections

From here, we can look at some of the example collections in this project and
see how data can be added and maintained in our repository.

- [Example 1](collection/ex1-pets) This is the most
  simple example of adding data into the repository.  The data is born digital,
  and the organization of the data and the metadata is very basic.

- [Example 2](collection/ex2-photos) This example explains how
  to document physical objects, in this case historical photos, and their
  associated digital representations.

- [Example 3](collection/ex_sherry_lehmann) Here, we show how you
might organize objects that have multiple digital encodings, in this case where
each catalog has a complete PDF file, but we also have each page as an image.
