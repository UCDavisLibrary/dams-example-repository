---
layout: page
title: Adding Your First User
permalink: /adding-your-first-user/
---

## Basic-Auth

Our container setup separates the authentication step from the Fedora and
other services.  The services rely on valid JWT tokens being sent along with
requests to the system.  The Authentication services are what creates these
tokens.  In production, you will most certainly want to use a centralized
authentication mechanism, see [CAS Authentication](#CAS-Authentication) in the [Advanced Configuration](../advanced-configuration) section to see an example of this type of setup.  However, for
testing, we have included a Basic Authentication service that can be used
without any external setup.  You should really only use this service for
testing.

Earlier, we looked at some of the container processes we were running with
`docker-compose -f fin-example.yml ps`. One process there, is there is the
basic-auth service.  In addition, by default we allow anyone to [create a new
user account](https://itcatalog.ucdavis.edu/service/central-authentication-service-cas). Navigate to that
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

<a id="CAS-Authentication"></a>

## CAS Authentication

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
