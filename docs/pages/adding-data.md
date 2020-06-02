---
layout: page
title: Adding Data to the Repository
permalink: /adding-data/
---

Now that we have our repository and we have administrative access, let's make
ouradd first entry into the repository.

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
