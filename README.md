# jake.pubpub.org

This repo is our simple heroku deployment of jake.pubpub.org.

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
  DNS + CDN (Cloudflare)
-------------------------
    CDN (CloudFront)
-------------------------
assets.pubpub.org (CNAME)
-------------------------
     fileserver (S3)
``` 

The resizing service:
```
    CDN (Cloudflare)
-------------------------
    jake.pubpub.org 
-------------------------
       Thumbor
```

The originals fileserver is behind two layers of CDN because S3 alone does not provide what's needed to have a custom domain with SSL. That is, we can't make https://assets.pubpub.org work without using the SSL provided by Cloudfront. Cloudflare in this case behaves more or less as a DNS provider allowing us to CNAME assets.pubpub.org to our Cloudflare URL. The added layer of CDN doesn't hurt anything.

# Setup

```
heroku create
heroku buildpacks:set heroku/python
heroku buildpacks:add https://github.com/jc4p/heroku-buildpack-gifsicle.git
git push heroku master
heroku scale web=1

```

Herokus cache seems to fail with gifsicle on each new build (the original build works fine).

To clear the Heroku cache:
```
heroku repo:purge_cache -a <app-name>
```