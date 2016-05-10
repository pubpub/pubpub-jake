# jake.pubpub.org

This repo is the simple heroku deployment of jake.pubpub.org.

Jake is our server which runs [Thumbor](https://github.com/thumbor/thumbor) for resizing and editing images on the fly.

![Jake](https://media.giphy.com/media/ZnCgWmoAqMII8/giphy.gif)

# PubPub Asset Architecture

## Filename structure
PubPub assets are named with the following convention

/hash/timestamp.extension

The hash is a 8 character hex string. This hash is simply a pseudo-random string that allows certain performance and scalability optimizations for S3 (and likely other storage infrastructures). 

The timestamp is a unix timestamp with milliseconds (i.e. `new Date().getTime()`).

## Infrastructure
Our assets infrastucture has two main components: an 'originals' fileserver, and a resizing service.

The originals fileserver:
```
    CDN (Cloudflare)
-----------------------
assets.pubpub.org CNAME
-----------------------
    fileserver (S3)
``` 

The resizing service sv
```
    CDN (Cloudflare)
-----------------------
    jake.pubpub.org
-----------------------
      Thumbor
```