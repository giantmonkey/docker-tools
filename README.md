docker-tools
============

A suite of tools to help with docker deployments in a production setting. 

All tools support a `--help` option that explains the individual arguments.

You need to install [docker-py](https://github.com/dotcloud/docker-py) first:

    easy_install docker-py


## dt-rebuild-image

Can rebuild images from Dockerfiles recursively. 

Unchanged images are intelligently detected by timestamp and may be skipped. Expects your Dockerfiles to be in ~/docker-files or /var/lib/docker-files by default.

Use cases:

1. You change your base image (ex. giantmonkey/ubuntu) and want to rebuild all images that depend on it

        dt-rebuild-image --include-descendants giantmonkey/ubuntu
    
2. You want to rebuild an image (ex. giantmonkey/nginx) including all the parents because of software updates.

        dt-rebuild-image --include-parents giantmonkey/nginx
    
    
## dt-attach

Enter the specified container.

It uses lxc-attach internally to attach to the running container. Use this execute commands inside an otherwise running container.

    dt-attach nginx
    
You can also execute commands directly by using a pipe:

    echo "ps aux" | dt-attach nginx
    

## dt-dns-config

Automatically configure your local dns for all containers.

This assumes you are running dnsmasq. By default all host entries are written to /etc/hosts.docker so make sure that dnsmasq reads this file.

Each container gets it's name as it's host. Additionally your domain is append to the name to form a fully qualified domain name. So suppose your container is named nginx and the domainname of your server is docker.giantmonkey.de then dt-dns-config will create the hostnames nginx and nginx.docker.giantmonkey.de. You can also specify more than one domain name.

    dt-dns-config --domain docker1.giantmonkey.de --domain docker2.giantmonkey.de
    

## dt-dns-reload

Reload your dns config into dnsmasq.

This command needs to be run after `dt-dns-config`.


## dt-varnish-config

Automatically configure varnish (the caching proxy) for all containers.

This assumes you are running a varnish container. A backend is created for each container and name based routing is setup so that you have vhosts running on the same IP.

Suppose you have a container named nginx and your domain is docker.giantmonkey.de then varnish will be configured to route all http requests refering to nginx.docker.giantmonkey.de to the container named nginx.

Again you can specify multiple domains:

    dt-varnish-config --container varnish --domain docker1.giantmonkey.de --domain docker2.giantmonkey.de
    
If you have multiple container for the same service then make sure that their names have the same prefix followed by a number. For example nginx-1 and nginx-2. Both containers will then be load balanced by varnish for the domain nginx.docker.giantmonkey.de.
    

## dt-varnish-reload

Reload the varnish config.

This command needs to be run after `dt-varnish-config`.
