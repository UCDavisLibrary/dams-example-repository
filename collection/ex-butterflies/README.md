# Example Butterflies - Video

The butterflies example is meant to show how we represent
video in the DAMs.  The main component of this is to show how we provide
streaming support for our videos, but at the same time, allow for users to
download a complete video.

There are three main components of this; the item, the video source, and the
streaming version.  What's important is the way these items are coordinated with
the metadata schema.

The item uses the standard `associatedMedia` tag to identify the video.  The
important part of the `monarch1.ttl` file is:

``` ttl
<> a schema:CreativeWork;
   schema:associatedMedia <monarch1/monarch_butterfly.mp4> ;
```

Since the streaming version of the data is wholely created from this file, we
make that an `associatedMedia` component of the mp4 file.  The
`monarch_butterfly.mp4.ttl` file contains:

``` ttl
<> a schema:VideoObject, schema:MediaObject, schema:CreativeWork;
 schema:encodesCreativeWork <..>;
 schema:associatedMedia <../stream> ;
 schema:encodingFormat "video/mp4";
```

Now, looking at the streaming data file, we need to identify where to look for
the manifest. This is retrieved from the `contentUrl` parameter. In addition, we
use the `ucdlib:clientMediaDownload` to help the application know what to offer
as a download. The `stream.ttl` file looks like this:

``` ttl
<> ldp:Container, schema:VideoObject, ucdlib:StreamingVideo;
    schema:contentUrl <stream/playlist.m3u8> ;
    ucdlib:clientMediaDownload <monarch_butterfly.mp4> ;
    schema:encodesCreativeWork <monarch_butterfly.mp4> ;
    schema:encodingFormat "application/x-mpegURL";
    schema:caption <caption-en.vtt>, <caption-fr.vtt> ;
    .
```
