[![Docker Hub](http://img.shields.io/badge/docker-hub-brightgreen.svg?style=flat)](https://registry.hub.docker.com/u/hopsoft/graphite-statsd/)

# Docker Image for Graphite

## Get Graphite & Statsd running instantly

Graphite can be complex to setup.
This image will have you running & collecting stats in just a few minutes.

## Quick Start

```sh
sudo docker run -d \
  --name graphite \
  --restart=always \
  -p 80:80 \
  -p 2003:2003 \
  voduytuan/tc-graphite
```

This starts a Docker container named: **graphite**

That's it, you're done ... almost.

### Includes the following components

* [Nginx](http://nginx.org/) - reverse proxies the graphite dashboard
* [Graphite](http://graphite.readthedocs.org/en/latest/) - front-end dashboard
* [Carbon](http://graphite.readthedocs.org/en/latest/carbon-daemons.html) - back-end

### Mapped Ports

| Host | Container | Service |
| ---- | --------- | ------- |
|   80 |        80 | nginx   |
| 2003 |      2003 | carbon  |

### Mounted Volumes

| Host              | Container                  | Notes                           |
| ----------------- | -------------------------- | ------------------------------- |
| DOCKER ASSIGNED   | /opt/graphite              | graphite config & stats storage |
| DOCKER ASSIGNED   | /etc/nginx                 | nginx config                    |
| DOCKER ASSIGNED   | /etc/logrotate.d           | logrotate config                |
| DOCKER ASSIGNED   | /var/log                   | log files                       |

### Base Image

Built using [Phusion's base image](https://github.com/phusion/baseimage-docker).

* All Graphite related processes are run as daemons & monitored with [runit](http://smarden.org/runit/).
* Includes additional services such as logrotate.

### Visualize the Data

Open Graphite in a browser at [http://localhost/dashboard](http://localhost/dashboard).

## Secure the Django Admin

Update the default Django admin user account. _The default is insecure._

  * username: root
  * password: root
  * email: root.graphite@mailinator.com

First login at: [http://localhost/account/login](http://localhost/account/login)
Then update the root user's profile at: [http://localhost/admin/auth/user/1/](http://localhost/admin/auth/user/1/)

## Change the Configuration

Read up on Graphite's [post-install tasks](https://graphite.readthedocs.org/en/latest/install.html#post-install-tasks).
Focus on the [storage-schemas.conf](https://graphite.readthedocs.org/en/latest/config-carbon.html#storage-schemas-conf)

1. Stop the container `docker stop graphite`.
1. Find the configuration files on the host by inspecting the container `docker inspect graphite`.
1. Update the desired config files.
1. Restart the container `docker start graphite`.

**Note**: If you change settings in `/opt/graphite/conf/storage-schemas.conf`
be sure to delete the old whisper files under `/opt/graphite/storage/whisper/`.

---


Ensure that `storage-schemas.conf` retentions are no finer grained than 10 seconds.

```
[all]
pattern = .*
retentions = 5s:12h # WRONG
retentions = 10s:12h # OK
retentions = 60s:12h # OK
```

## A Note on Disk Space

If running this image on cloud infrastructure such as AWS,
you should consider mounting `/opt/graphite` & `/var/log` on a larger volume.

1. Configure the host to mount a large EBS volume.
1. Specify the volume mounts when starting the container.

    ```
    sudo docker run -d \
      --name graphite \
      --restart=always \
      -v /path/to/ebs/graphite:/opt/graphite \
      -v /path/to/ebs/log:/var/log \
      -p 80:80 \
      -p 2003:2003 \
      voduytuan/tc-graphite
    ```

## Additional Reading

* [Introduction to Docker](http://docs.docker.io/#introduction)
* [Practical Guide to StatsD/Graphite Monitoring](http://matt.aimonetti.net/posts/2013/06/26/practical-guide-to-graphite-monitoring/)
* [Configuring Graphite for StatsD](https://github.com/etsy/statsd/blob/master/docs/graphite.md)

