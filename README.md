QGIS Mapserver Demo Orchestration
=================================

![qgis-docker-server](https://cloud.githubusercontent.com/assets/178003/15494222/88538f5c-2189-11e6-8388-2c8f43a72fbd.gif)

# Overview

## What does it do?

It's a live web mapping gallery running QGIS Server in Docker and with deployed projects synced seamlessly from your desktop.

This repository contains the [docker-compose](https://docs.docker.com/compose/) orchestration configuration for running QGIS demo server at http://demo.qgis.org. To use this repository, you need to have [docker](https://www.docker.com) and docker-compose installed on any supported host. When it is running it will create a synchronised platform for hosting maps based on [QGIS Server](http://docs.qgis.org/testing/en/docs/user_manual/working_with_ogc/ogc_server_support.html), [BTSync](http://getsync.com) and [PostGIS](http://postgis.net). Note that PostGIS is bundled but currently not used on the live demo server, we will start to include QGIS Server in future revisions.

Once the composed service is busy running, it supports the following workflow:

* Install QGIS Desktop and BTSync on your desktop and synchronise a folder with the read/write btsync folder key - which you need to get from tim@qgis.org or richard@qgis.org. The data in the synchronised folder is also version controlled in the [demo.qgis.org](https://github.com/qgis/demo.qgis.org) GIT repository.
* Open one of the projects in the demo directory and improve it using QGIS. Or create a new project. For new projects be sure to add them to the [gallery page](https://github.com/qgis/demo.qgis.org/blob/master/index.html).
* New projects should also be placed in their own folder - for simple cases, copy the [Swellendam](https://github.com/qgis/demo.qgis.org/blob/master/demos/swellendam/index.html) index and modify it according to your needs.

## Why did we make this?

There are two reasons:

1. To showcase what you can do with QGIS and QGIS Server by creating an online gallery. Nothing advocates QGIS better than seeing the cartography you can produce with it.
2. To showcase how QGIS and Docker can be used to orchestrate and deploy web maps seamlessly.

## Caveats

 Not all of the demos will work on your local machine since they reference http://demo.qgis.org. Feel free to visit the aforementioned site to see how they renderer. We would like it if others out there could contribute new examples to the site. You can also use this project as a guide to see how to deploy similar live maps to your own organisation.

# General Architecture

<img width="869" alt="screen shot 2016-05-23 at 21 45 04" src="https://cloud.githubusercontent.com/assets/178003/15482421/cd3d770e-212f-11e6-8359-77865e49b73a.png">

## Contributing

## Get the code

First check out the sourecs to your local machine:

```
git clone https://github.com/qgis/docker-qgis-orchestration.git
cd docker-qgis-orchestration
```

## Build and run the services

On Linux just do:

```
docker-compose up -d qgis-server
```

On OSX or Windows, we recommend using [docker machine](https://docs.docker.com/machine/):

```
docker-machine create --driver virtualbox demo.qgis.org
docker-machine start demo.qgis.org
eval "$(docker-machine env demo.qgis.org)"
docker-compose up -d qgis-server
```



## Verify everything is running

After deploy is run you should have 3 running containers e.g.:

```
docker ps
CONTAINER ID        IMAGE                     COMMAND                  CREATED             STATUS              PORTS                  NAMES
2cffc0ce7729        kartoza/qgis-server       "/bin/sh -c 'apachect"   24 minutes ago      Up 24 minutes       0.0.0.0:8198->80/tcp   demo.qgis.org
e730206a9f89        kartoza/postgis:9.4-2.1   "/bin/sh -c /start-po"   24 minutes ago      Up 24 minutes       5432/tcp               db.demo.qgis.org
c35949d6f660        kartoza/btsync            "/start.sh"              24 minutes ago      Up 24 minutes       8888/tcp, 55555/tcp    btsync.demo.qgis.org
```

## Test the service

You can test the service is running on OSX or windows by pointing to port 8198 of your docker machine:

```
docker-machine ls
```

Take a note of the IP address of the demo.qgis.org machined and then open your browser at ``http://<ip address>:8198``


On Linux simply test by pointing your browser at http:///localhost:8198

## Reverse proxy for nginx

You will probably want to set up a reverse proxy pointing to your QGIS
Mapserver container (our orchestration scripts publish on 8198 by default).
If you are using nginx on the host, you can simply do:

```
upstream demo.qgis.org {
    server 127.0.0.1:8198;
}

server {

    # OTF gzip compression
    gzip on;
    gzip_min_length 860;
    gzip_comp_level 5;
    gzip_proxied expired no-cache no-store private auth;
    gzip_types text/plain application/xml application/x-javascript text/xml text/css application/json;
    gzip_disable [34m~@~\MSIE [1-6].(?!.*SV1)[34m~@~];

    # the port your site will be served on
    listen      80;
    # the domain name it will serve for
    server_name demo.qgis.org;
    charset     utf-8;


    # This gives equivalent of proxypreservehost in apache
    proxy_set_header Host $host;
    # max upload size, adjust to taste
    client_max_body_size 15M;

    location / {
        proxy_pass http://demo.qgis.org;
    }
}

```


# Contributing new demo maps

There are three basic ways you can contribute new maps for the gallery:

## Via BTSync

You can synchonise the data to your desktop using btsync - this is our read only key: ``BRKIFB3PYGOXTDBOWXH6G4UCN2GYZV5ER``. The read only key will only let you run the system on your own host, but changes you make will not be synchronised to the live server. For live synchronisation you will need the Read/Write btsync key - ask tim@qgis.org or richard@qgis.org for that. For obvious reasons, we will only give live sync access to trusted users, other users should provide their updates using one of the methods below.

## Via a GitHub Pull Request

You can fork the map demos repo here: https://github.com/qgis/demo.qgis.org and then add new demos or tweak the existing ones. Then submit a pull request to have your changes incorporated onto the live site.

## Via email

In this case simply send us a (small please!) zip file containing your new or improved demo and any changes to the landing page etc. You can send this to tim@qgis.org.


# Guidelines for contributing new maps

* There are still some limitations when it comes to hosting SVG markers and other file system based assets, so we suggest to avoid using those right now (or test it thoroughly first).
* Please use very small datasets - the datasets need to be synced around between lots of computers and the demos need to run fast.
* Please make sure all datasets provided are sharable.
* To author new maps, you do not actually need the docker stuff running - just create a small map that works well locally and that has all its data files colocated in the same directory, then share it e.g. by email as described above.


--------

Tim Sutton and Richard Duivenvoorde, August 2014

