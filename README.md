QGIS Mapserver Demo Orchestration
=================================

Orchestration scripts for running QGIS demo server.

To use you need to have docker and docker-compose installed on any supported host. 

# General Architecture

<img width="869" alt="screen shot 2016-05-23 at 21 45 04" src="https://cloud.githubusercontent.com/assets/178003/15482421/cd3d770e-212f-11e6-8359-77865e49b73a.png">

# Get the code

First check out the sourecs to your local machine:

```
git clone https://github.com/qgis/docker-qgis-orchestration.git
cd docker-qgis-orchestration
```

# Build and run the services

On OSX or Windows, we recommend using docker machine:

```
docker-machine create --driver virtualbox demo.qgis.org
docker-machine start demo.qgis.org
eval "$(docker-machine env demo.qgis.org)"
docker-compose up -d qgis-server
```

On Linux you probably don't use docker-machine so just do:

```
docker-compose up -d qgis-server
```

# Verify everything is running

After deploy is run you should have 3 running containers e.g.:

```
docker ps
CONTAINER ID        IMAGE                     COMMAND                  CREATED             STATUS              PORTS                  NAMES
2cffc0ce7729        kartoza/qgis-server       "/bin/sh -c 'apachect"   24 minutes ago      Up 24 minutes       0.0.0.0:8198->80/tcp   demo.qgis.org
e730206a9f89        kartoza/postgis:9.4-2.1   "/bin/sh -c /start-po"   24 minutes ago      Up 24 minutes       5432/tcp               db.demo.qgis.org
c35949d6f660        kartoza/btsync            "/start.sh"              24 minutes ago      Up 24 minutes       8888/tcp, 55555/tcp    btsync.demo.qgis.org
```

# Test the service

You can test the service is running on OSX or windows by pointing to port 8198 of your docker machine:

```
docker-machine ls
```

Take a note of the IP address of the demo.qgis.org machined and then open your browser at http://<ip address>:8198


On Linux simply test by pointing your browser at http:///localhost:8198

# Additional notes

 Not all of the demos will work on your local machine since they reference http://demo.qgis.org. Feel free to visit the afore mentioned site to see how they renderer. We would live it if others out there could contribute new examples to the site. You can also use this project as a guide to see how to deploy similar live maps to your own organisation.

# Making the projects editable on your deskop

You can synchonise the data to your desktop using btsync - this is our read only key: ``BRKIFB3PYGOXTDBOWXH6G4UCN2GYZV5ER``




# Reverse proxy for nginx

Lastly you will probably want to set up a reverse proxy pointing to your QGIS
Mapserver container (our orchestration scripts publish on 8198 by default).
If you are using nginx on the host, you can simply do:

```

```


--------

Tim Sutton and Richard Duivenvoorde, August 2014

