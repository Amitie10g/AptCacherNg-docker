## AptCacherNg Docker container image

This container image allows to dockerize [AptCacherNg](https://wiki.debian.org/AptCacherNg), making your Debian-based containers to use the AptCacherNg proxy for both building and running containers. In the best of cases, it will greatly speed up the package installing process on scenarios with large ammount of packages.

## Usage

### Starting AptCacherNg

Start AptCacherNg container, using a dedicated network
```
docker run -d -p 3142:3142 --name apt-cacher-ng -v $(pwd)/cache:/var/cache/apt-cacher-ng amitie10g/apt-cacher-ng
```

### Building your container

Before get started, add this at the top of the Dockerfile step context, in order to instruct apt to use your proxy:

```
ARG APT_PROXY
RUN if [ ! -z "$APT_PROXY" ]; then echo "Acquire::http { Proxy \"http://$APT_PROXY:3142\"; };" >> /etc/apt/apt.conf.d/01proxy; fi
```

And add this at the bottom of the step, in order to remove the proxy from the final image:

```
rm /etc/apt/apt.conf.d/01proxy
```

Then, build your container:

```
docker build -v APT_CACHE=$(docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' apt-cacher-ng) -t <your container:tag> .
```

### Running your container

```
docker run --network=mynetwork -v APT_CACHE=$(docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' aptcacherng) <your container:tag>  <commands>
```

## Licensing
* The Dockerfile has been released into the Public domain (The Unlicense)
* The AptCacherNg  and Ubuntu container base image are subjected to the corresponding licenses
