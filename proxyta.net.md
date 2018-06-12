title: proxyta.net
date: 2018-06-07
author: Nim
sites: saurel.me
category: Informatique
tags: web

*NB*: All of this is already done and available on [proxyta.net](https://proxyta.net). You can just clone it and use it. This post is only the step by step explanation.

## Who needs a reverse proxy, anyway ?

If you have ever managed a server with more than one web service on it, you are already familiar with some reverse proxy, as the good old [apache](https://httpd.apache.org/), the efficient [nginx](https://nginx.org/en/), and/or the brand new [traefik](https://traefik.io/).

My point is that if you want to test stuffs as if they were in production (and I hope you do), while not really testing them in your actual production environment (and I hope you don't), you would benefit from using a reverse proxy on your dev setup too.

Obviously, you will not add any complexity to your dev setup, unless there is something really simple, really easy, and really solid. Now guess what… Yup, that's the topic of this post :)

Bonus: if you do this, you can seemlessly switch a setup from test to prod and vice versa, without even letting your services know it !

## Bootstrap your environment

To implement this is really easy, thanks to [docker](https://www.docker.com/) & [traefik](https://traefik.io/). All you need is to declare a network on which you will put your reverse proxy container and all the services that you want to expose. Let's call it "proxytanet":

```
docker network create proxytanet
```

Then, use a simple, ready-to-go, `traefik.toml` configuration file:

```
defaultEntryPoints = ["http"]

[entryPoints]
  [entryPoints.http]
    address = ":80"

[docker]
  endpoint = "unix:///var/run/docker.sock"
  domain = "docker.localhost"
  watch = true
  exposedbydefault = false

[api]
```

And, to finish, a `docker-compose.yml` for it:

```
version: '3'

services:
  traefik:
    image: traefik:alpine
    restart: unless-stopped
    ports:
      - 80:80
      - 8080:8080
    networks:
      - proxytanet
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - ./traefik.toml:/etc/traefik/traefik.toml

networks:
  proxytanet:
    external: true
```

If you don't already have something binded on the ports 80 & 8080 of your host, a `docker-compose up -d` will finish this setup. You can now access to traefik's dashboard, on [http://localhost:8080](http://localhost:8080).

*NB*: This setup is available in [proxyta.net/dev](https://proxyta.net/tree/master/dev)

## Setup your services

As traefik is listening to docker.sock, you can plug any service only by declaring a few labels and putting the container on your `proxytanet` network. Long story short, in the `docker-compose.yml` of your app:

```
version: '3'

services:
  your_app:
    […]
    labels:
      traefik.enable: "true"
      traefik.frontend.rule: "Host: your_app.${DOMAIN_NAME:-local}, www.your_app.${DOMAIN_NAME:-local}"
    networks:
      - proxytanet

networks:
  proxytanet:
    external: true
```

In most cases, your service also need to access to other private containers, like a database or a redis, or a memcached. Then, you also have to put it in the (usually implicit) `default` network of its `docker-compose.yml` file:

```
version: '3'

services:
  db:
    […]
  your_app:
    […]
    labels:
      traefik.enable: "true"
      traefik.frontend.rule: "Host: your_app.${DOMAIN_NAME:-local}, www.your_app.${DOMAIN_NAME:-local}"
      traefik.docker.network: "proxytanet"
    networks:
      - proxytanet
      - default

networks:
  proxytanet:
    external: true
```

Just don't forget to tell traefik on which of the networks it has to listen, with the third label in this example.

## One more thing

You're almost done: the last thing you need is to configure a DNS that will redirect `your_app.local` to your ip. In a dev / test environment, it's just a matter of one line in `/etc/hosts`

```
127.0.0.1 your_app.local www.your_app.local
```

As you may have noticed, the configuration is `your_app.${DOMAIN_NAME:-local}`. In other words, if you have a `DOMAIN_NAME` variable (defined in your environment or in a `.env` file) set to `exemple.org`, traefik will make your service available on `your_app.example.org`.

## Production setup

In production, you can keep the architecture and the `proxytanet` network, all you need to change is the `traefik.toml` configuration to generate SSL certificates with [Let's Encrypt](https://letsencrypt.org/):

```
defaultEntryPoints = ["http", "https"]

[entryPoints]
  [entryPoints.http]
    address = ":80"
    [entryPoints.http.redirect]
      entryPoint = "https"
  [entryPoints.https]
    address = ":443"
    [entryPoints.https.tls]
    [entryPoints.https.redirect]
      regex = "^https://www.(.*)"
      replacement = "https://$1"

[docker]
  endpoint = "unix:///var/run/docker.sock"
  domain = "docker.localhost"
  watch = true
  exposedbydefault = false

[acme]
  storage = "acme.json"
  entryPoint = "https"
  OnHostRule = true
  [acme.httpChallenge]
    entryPoint = "http"
```

And the associated `docker-compose.yml`:

```
version: '3'

services:
  traefik:
    image: traefik:alpine
    restart: unless-stopped
    ports:
      - 80:80
      - 443:443
    networks:
      - proxytanet
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - ./traefik.toml:/etc/traefik/traefik.toml
      - ./acme.json:/acme.json
    command: --acme.email=${ACME_EMAIL}

networks:
  proxytanet:
    external: true
```

(don't forget to setup the `ACME_EMAIL` variable in your environment or a `.env` file, te receive informations from Let's Encrypt.)

*NB*: This setup is available in [proxyta.net/prod-le](https://proxyta.net/tree/master/prod-le) ; and if you prefer to use your own certificates rather than Let's Encrypt's, you can check [proxyta.net/prod-ssl](https://proxyta.net/tree/master/prod-ssl)

## Examples

This method has been developped in the context of [oxyta.net](https://oxyta.net), a [CHATONS](https://chatons.org/) (for *Collectif des Hébergeurs Alternatifs, Transparents, Ouverts, Neutres et Solidaires*, which can be nicely translated into KITTENS, for Keen Internet Talented Teams Engaged in Network Service) from our friends of [framasoft](https://framasoft.org/).

Hence the name.

In this CHATONS, we want to host several services for several organizations (french "associations loi 1901"), so we provide some exemples for different well known services:

- [nextcloud](https://framagit.org/altermediatic/docker-atelier/tree/master/cloud)
- [etherpad](https://framagit.org/altermediatic/docker-atelier/tree/master/pad)
- [a static website](https://framagit.org/altermediatic/docker-atelier/tree/master/homepage), [oxyta.net](https://oxyta.net)
- peertube (incoming…)
- gitlab (incoming…)

And I can also provide exemples for a django project (incoming…)

## That's all folks

Thanks for reading, and now, if you have any remarks, suggestions, or questions, feel free to participate in [proxyta.net](https://proxyta.net) !
