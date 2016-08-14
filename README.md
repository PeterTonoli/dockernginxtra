# Docker Ubuntu Nginx Pagespeed package builder
A [Docker](https://www.docker.com) image to build the [Nginx](https://nginx.org) web server with Google's automatic [PageSpeed](https://github.com/pagespeed/ngx_pagespeed) optimisation module using Debian/Ubuntu deb packages. All tested under Ubuntu 14.04LTS, but should work for any host that supports Docker.

- For each package a `Dockerfile` will build the OS image and create the deb package using [`checkinstall`](https://help.ubuntu.com/community/CheckInstall).
- The `extractdeb.sh` script will then:
	- run the image in a container
	- extract deb package back to the host file system
	- destroy the container

When installing package on target system remember that dependent packages will need to be installed manually, they are not brought over by the `checkinstall` process.

**Notes for Ubuntu 14.04LTS:** The Docker package in the Ubuntu repositories is (somewhat inconveniently) named `docker.io` due to an existing package clash. All build scripts assume you have symlinked the `docker.io` binary like so:

```sh
$ sudo apt-get install docker.io
$ ln -sf /usr/bin/docker.io /usr/local/bin/docker
```

Alternatively (and probably a better idea), you can install the latest stable [`docker-engine`](https://docs.docker.com/installation/ubuntulinux/) package from Docker's own repository using the following commands:

```sh
$ curl -sSL https://get.docker.com/ | sh
# add USERNAME to docker group - avoids the need for "sudo docker" calls.
$ sudo usermod -aG docker USERNAME
```

## Nginx
- **OS:** Ubuntu 14.04.4 LTS
- **Version:** 1.10.1
- **Configure:** [resource/configure.sh](resource/configure.sh)

Create and extract package:
```sh
$ ./build.sh
# waiting... as Docker builds image

$ ./extractdeb.sh
# package extract from container

$ ls -l nginx_1.10.1-1_amd64.deb
-rw-r--r-- 1 root root 3080472 Feb 27 13:51 nginx_1.10.1-1_amd64.deb
```

Install on target system:
```sh
# should be no dependent packages needed - based off packaged configure.sh
$ sudo dpkg -i --force-overwrite /path/to/nginx_1.10.0-1_amd64.deb
```

### Additional Modules:
* Brotli compression format [https://github.com/google/brotli](https://github.com/google/brotli)
* Upstream Fair Balancer [https://www.nginx.com/resources/wiki/modules/fair_balancer/#upstream-fair-balancer](https://www.nginx.com/resources/wiki/modules/fair_balancer/#upstream-fair-balancer)

### Credits:
[magnetikonline/dockerbuilddeb](https://github.com/magnetikonline/dockerbuilddeb/blob/master/README.md) for the excellent scaffold to build Docker build images.
