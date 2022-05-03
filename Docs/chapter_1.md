### Difference Between Containerization and Virtualization

`Virtual machines` represent `hardware-level` virtualization where one operating system
is downloaded on top of a `host` operating system. Therefore, they are `heavy` and `fully`
`isolated`. There is `no` availability for `SSH` or `TTY`, and they are fully `secured`.
On the other hand, `containers` represent operating system `virtualization`. You can
download as many different operating systems in containers as applications need.
`Containers` are `light` and `fast`; however, they are `not` fully `isolated` but `secure`

### How to Set Up a Local Registry

A Docker `registry` is a location where Docker `images` can be `pushed`, `pulled`, and
`stored`. `Registered users` have their own spaces in the registry. This user-named space
is called `repository`. A repository is a `namespace` that is used for `storing` `images`. For
example, assume a user has a public image called firstapp that is pushed to `Docker Hub`. To reach to this image, one must use the image repository name, which is
`<username>/<image name>`. In this example, assume that I am the image owner. Then
the image repository is `engyfouda/firstapp`.
Registries can be hosted by a third party as a public or private registry. Docker Hub is
an example of it. Later in the book, we will learn about the `Docker Trusted Registry` and
how to install it and use it with the `Docker EE`. Also, there are `Google Container Registry`
and `AWS Container Registry`.
Let us see how to set up and use a `local registry`. Setting it up is easy and fast. It is
only one command as we will see. To test it, we will pull any image from Docker Hub,then push it to our local registry, delete all the local images, and pull it from the local
registry.

To set up a local registry without a graphical user interface `(GUI)`, we will use a
repository image that is on Docker Hub called `Registry with tag 2 (registry:2)`. The
installation command is
```dockerfile
$ docker run -d -p 5000:5000 --restart=always --name registry registry:2

Unable to find image 'registry:2' locally
2: Pulling from library/registry
df9b9388f04a: Pull complete
52dc419b0ee2: Pull complete
b6846b9db566: Pull complete
b0a23bbf973d: Pull complete
c50f110701a7: Pull complete
Digest: sha256:dc3cdf6d35677b54288fe9f04c34f59e85463ea7510c2a9703195b63187a7487
Status: Downloaded newer image for registry:2
050cbc7e1ed07d85024c52380f0be7b37bfa7fc9f01147a54f1f241b23934780
```

. Basically, we craft a container based on the registry:2 image.
The `-d` option is to run as a daemon in the `background`. The `-p` is to set the `<host port>: <container port>`; we will talk about ports in detail in Chapter 4. We set the restart
`policy to be always and set the container name as registry`.

To test this registry, we pull any image from Docker Hub, for example, 
```shell
$ docker pull busybox

Using default tag: latest
latest: Pulling from library/busybox
50e8d59317eb: Pull complete
Digest: sha256:d2b53584f580310186df7a2055ce3ff83cc0df6caacf1e3489bff8cf5d0af5d8
Status: Downloaded newer image for busybox:latest
docker.io/library/busybox:latest
```
. Then rename it to add the local repository name using 
```shell
$ docker tag busybox localhost:5000/firstapp`
```
Now, push it to the local registry using 

```shell 
$ docker push localhost:5000/firstapp

Using default tag: latest
The push refers to repository [localhost:5000/firstapp]
eb6b01329ebe: Pushed
latest: digest: sha256:52f431d980baa76878329b68ddb69cb124c25efa6e206d8b0bd797a828f0528e size: 527   
```


`Delete` all the `local` `images` using 
```shell
$ docker image remove busybox

Untagged: busybox:latest
Untagged: busybox@sha256:d2b53584f580310186df7a2055ce3ff83cc0df6caacf1e3489bff8cf5d0af5d8
```
and 

```shell
$ docker image remove localhost:5000/firstapp

Untagged: localhost:5000/firstapp:latest
Untagged: localhost:5000/firstapp@sha256:52f431d980baa76878329b68ddb69cb124c25efa6e206d8b0bd797a828f0528e
Deleted: sha256:1a80408de790c0b1075d0a7e23ff7da78b311f85f36ea10098e4a6184c200964
Deleted: sha256:eb6b01329ebe73e209e44a616a0e16c2b8e91de6f719df9c35e6cdadadbe5965

```
To make sure that we
do not have trace of these images, list the images using 
```shell
$ docker image ls
```
, and we will
not find these images in the list anymore. Pull the image from our local registry: `docker pull localhost:5000/firstapp`.
To `remove` the `local registry`, `stop it and remove` it using `docker container stop registry && docker container rm -v registry`.
To add a GUI, follow the steps in this site: http://joxit.github.io/dockerregistry-ui/.
