---
layout: page
title: Accessing the LDP Server
permalink: /accessing-the-ldp-server/
---

Now that we have elevated privileges, let's revisit the root to the LDP services,
`http://localhost:3000/fcrepo/rest`.  Now we should have access to this location.
There isn't anything here, but at least we can see that now.  Users familiar
with Fedora will note that this is the standard fedora interface when accessed
via the browser.

In many of the examples following, we will also be using the command-line tool
`fin`.  If you haven't already, you can install this tool with `npm install -g @ucd-lib/fin-cli`

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
