# Intertwine

This collection example shows how to import trello2moment items into a
collection.

In this example, we are making a collection completely of moments.  This is not
the only possible example but what we are trying.  The skeleton of this is in
the intertwine and intertwine.ttl files.  It basically includes a thumbnail, and
a placeholder directory, `moments` that will hold the individual moment objects.

After logging in, we can import the collection as below.

``` bash
fin io import intertwine .
```

This creates the collection, and adds the thumbnail, and moments directory.  You
can already verify this is working by looking at the dams collection.

## Moments

We can not import the moments one at a time.  Since these are coming from
trello, we will create them, and add them in at that point.  Currently, the
trello2moment command doesn't properly make the files, so we need to run an
external command.

``` bash
key=[secret]
token=[secret]
trello2moment --thumbnails --key=${key} --token=${tok} --moment=cats --board=pY20Yz5x
```

This makes a `cats_moment.ttl` file in the root, which we convert with:

``` bash
riot --formatted=jsonld --base=z: cats_moment.ttl | sed 's/z:#//' | tee cats/cats.json
```

Now we have a `cats.ttl` file and a `cats` directory.  To add those in, use the
`fin io import command`

``` bash
fin io import -p moments -s cats intertwine moments/
```
