# Docker Certified Associate
## Part 2 of 8: Image Creation 

## Course Outline: Image Creation

### Working with Images
Working with Docker Images: docker build will read the `DockerFfile` and build the image as per he instructions in the file.
```bash
$ docker build . #--build the docker in current dicrecoty
```
Dockerfile: It conatins the detailed instructions/command how to build the docker image. Format:
```bash
# Comment 
INSTRUCTION argument
```

A custom nginx Image using docker file:
```bash
#-- launch a new container
$ docker container run 0d -p 80:80 -t --name mynginx ubuntu
$ docker ps
#-- connect this container
$ docker container exec -it mynginx bash
#-- inside the terminal of the container now run the commands
$ apt-get update
$ agt-get install -y nginx
# view nginx status
$ /etc/init.d/nginx status
# edit default html file with custom message
$ cd /var/www/html
$ ls
```
Create a file into current docker directory
```bash
$ nano index.nginx-debian.html
$ echo "Welcome to Base Nginx Container from KPLABS">index.nginx-debian.html
```
Convert these commandas and location of html file into a Dockerfile
```bash 
FROM ubuntu
RUN apt-get update
RUN agt-get install -y nginx

COPY index.nginx-debian.html /var/www/html/
CMD nginx -g 'daemon off;'
```
 If you want to install nginx automatically, without it stopping to prompt you for approval, you should include what flag in the installation command?:
 `-y`
### Copy vs Add
COPY vs ADD 
We have a docker file as
```bash
FROM busybox
COPY copy.txt /tmp
ADD add.txt /tmp
CM ["sh]
```
Now build this `Dockerfile` to an image
```bash
$ docker build -t demobusybox .
```
Then create a new contsiner form the created image
```bash
$ docker images
$ docker container run -dt --name demobusybox busybox
```
* copy: COPY src (local or directory) to Destination

* add: supports 2 additional sources (URL, extract tar to destination) --- discouraged, instead use `wget` or `curl`
```bash
ADD http://example.com/big.tar.xz /usr/src/things
RUN tar -xJf /us/src/things/big.tar.xz -C /usr/src/things
RUN make -C /usr/src/things all
```
Alternate to creating multiple layers and reducing the size
Dockerfile HEALTHCHECK

Dockerfile ENTRYPOINT

Docker Tags First
Remove an image `docker rmi <name>`
build with a tag
```bash
$ docker  build -t demo:v1 .
$ docker images
```
assign tage ot an existing image
```bash
$ docker tag <uuid> demov:v2
```


### Docker Commit
 By default, the container being committed and its processes will be paused while the image is committed.

Docker Commit saves changes of a running container to form a new image with all of those changes
```bash
$ docker run -dt --name busybox busybox
$ docker container exec -it busybox sh
```
Craete some files and changes to many files. Now create a new image out of it:
```bash
$ docker container commit busybox busybox-modified-01
```
Run a new container out of modified image
```bash
$ docker run -dt --name busybox-custom busybox-modified-01
```
change a docker instruction `CMD`
```bash
$ docker container commit --change='CMD ["ash"]' busybox
$ docker run -dt --name busybox-modified-02 <shortid>
$ docker ps
```


Each of the command creates a layer on the image. We can view the Layers of Docker Image
```bash
$ docker history <name>
```

### Managing Images with CLI
#### Managing Images with CLI
Build, remove, save, tag etc.. can be done on docker images. We can run several child commands. We can see list of avilable docker child commands by:
```bash
$ docker image --help
```

#### Inspecting Docker Images
`docker image inspect` can be used to view associated properties of image, such as creation date, command, env variables, arch, OS, size etc..
```bash
$ docker image inspect nginx
$ docker image inspect nginx | grep Hostname
```
We can also view using `format` command using the tag (from `json`):
```bash
$ docker image inspect nginx -format='{{.Id}}'
$ docker image inspect nginx -format='{{.ContainerConfig}}'
$ docker image inspect nginx -format='{{json .ContainerConfig}}' # to view json key and value

$ docker image inspect nginx -format='{{.ContainerConfig.Hostname}}' # just to view the value of a key
```

#### Docker Image Prune
cleanup unsued images/ dangling image (no tag & not ref by any containers) by `clean`:
```bash
$ docker image    # to list of available images
$ docker image clean # remove unused images
$ docker image clean -a # remove images without any container associated with it
$ docker ps -a # will show association of conatiner of active images
```

### Modify Images

#### Modify Image to Single Layer
```bash
$ docker images # will show available images
$ docker image history <image name> # show the layers of the image
```
Now pull an image to view the layers of it:
```bash
$ docker pull ubuntu
$ docker image history ubuntu
$ docker images # to see the size of the miage before convert it into single layer
REPOSITORY   TAG       IMAGE ID       CREATED        SIZE
mysql        latest    f6360852d654   47 hours ago   565MB
nginx        latest    021283c8eb95   2 weeks ago    187MB
ubuntu       latest    5a81c4b8502e   3 weeks ago    77.8MB
```
Now strat the container form ubuntu image
```bash
$ docker container run -dt --name myubuntu ubuntu
5f3f67b4fea4452f4dcd5c54333515ddab1008520c4c638220abd77f609456a8
$ docker ps
CONTAINER ID   IMAGE     COMMAND       CREATED          STATUS          PORTS     NAMES
5f3f67b4fea4   ubuntu    "/bin/bash"   47 seconds ago   Up 46 seconds             myubuntu
```
To modify thecontainer to merge layers into single layer, we have to export and import.
```bash
$ docker export myubuntu > myubuntudemo.tar 
$ ls -l myubuntudemo.tar
-rwxrwxrwx 1 mhaque mhaque 80337920 Jul 24 10:45 myubuntudemo.tar
$ cat myubuntudemo.tar | docker import - myubuntu:latest # pipe to create a new images with tage `latest`
```
The layers of the image has been flattened and considerably reduced the size of the image
```bash
$ docker images
REPOSITORY   TAG       IMAGE ID       CREATED         SIZE
myubuntu     latest    94b162b589b0   7 seconds ago   77.8MB
mysql        latest    f6360852d654   47 hours ago    565MB
nginx        latest    021283c8eb95   2 weeks ago     187MB
ubuntu       latest    5a81c4b8502e   3 weeks ago     77.8MB
$ docker image history myubuntu
IMAGE          CREATED              CREATED BY   SIZE      COMMENT
94b162b589b0   About a minute ago                77.8MB    Imported from -
```

#### Docker Registry
Sotres docker images into highly available centralised registry, `docker hub`:
* Docker Registry: Opensource
* AWS ECR: private registry
* Docker Hub

We can run a local registry (https://hub.docker.com/_/registry)
```bash
# run a local registry
$ docker run -d -p 5000:5000 --restart always --name registry registry:2
# view list of available images
$ docker images
REPOSITORY   TAG       IMAGE ID       CREATED          SIZE
myubuntu     latest    94b162b589b0   13 minutes ago   77.8MB
mysql        latest    f6360852d654   47 hours ago     565MB
nginx        latest    021283c8eb95   2 weeks ago      187MB
ubuntu       latest    5a81c4b8502e   3 weeks ago      77.8MB
registry     2         4bb5ea59f8e0   5 weeks ago      24MB
# view list of running containers
$ docker ps 
CONTAINER ID   IMAGE        COMMAND                  CREATED          STATUS          PORTS                    NAMES
aa947a54e72d   registry:2   "/entrypoint.sh /etc…"   54 seconds ago   Up 53 seconds   0.0.0.0:5000->5000/tcp   registry
5f3f67b4fea4   ubuntu       "/bin/bash"              19 minutes ago   Up 19 minutes                            myubuntu
 
```

#### Pushing Images to Registry
We need to follow: tag, dns information with port
`docker push`` will be pushed to the registry
```bash
# verify if we can push a custom image in this registry
$ docker tag ubuntu:latest localhost:5000/myubuntu
$ docker images #now view the images
REPOSITORY               TAG       IMAGE ID       CREATED          SIZE
myubuntu                 latest    94b162b589b0   18 minutes ago   77.8MB
mysql                    latest    f6360852d654   47 hours ago     565MB
nginx                    latest    021283c8eb95   2 weeks ago      187MB
ubuntu                   latest    5a81c4b8502e   3 weeks ago      77.8MB
localhost:5000/myubuntu   latest    5a81c4b8502e   3 weeks ago      77.8MB
registry                 2         4bb5ea59f8e0   5 weeks ago      24MB

# Now push the custom image into the registry
$ docker push localhost:5000/myubuntu
Using default tag: latest
The push refers to repository [localhost:5000/myubuntu]
59c56aee1fb4: Pushed
latest: digest: sha256:75399abc111a48bcabcfe40c8fa5d1d44fb99e078ab449338d08f06dad34127e size: 529
# now view the images in registry
$ docker ps
CONTAINER ID   IMAGE        COMMAND                  CREATED          STATUS          PORTS                    NAMES
aa947a54e72d   registry:2   "/entrypoint.sh /etc…"   8 minutes ago    Up 8 minutes    0.0.0.0:5000->5000/tcp   registry
5f3f67b4fea4   ubuntu       "/bin/bash"              27 minutes ago   Up 27 minutes                            myubuntu
```
Now pull the image form registry by name:
```bash
$ docker pull localhost:5000/myubuntu
Using default tag: latest
latest: Pulling from myubuntu
Digest: sha256:75399abc111a48bcabcfe40c8fa5d1d44fb99e078ab449338d08f06dad34127e
Status: Downloaded newer image for localhost:5000/myubuntu:latest
localhost:5000/myubuntu:latest
$ docker images # view list of images
REPOSITORY                TAG       IMAGE ID       CREATED       SIZE
nginx                     latest    021283c8eb95   2 weeks ago   187MB
ubuntu                    latest    5a81c4b8502e   3 weeks ago   77.8MB
localhost:5000/myubuntu   latest    5a81c4b8502e   3 weeks ago   77.8MB
registry                  2         4bb5ea59f8e0   5 weeks ago   24MB
```
#### Docker Hub
We can push/pull images form `docker hub` registry
```bash
# pull the busybox image for the demonstration with docker hub registry 
$ docker pull busybox
Using default tag: latest
latest: Pulling from library/busybox
3f4d90098f5b: Pull complete
Digest: sha256:3fbc632167424a6d997e74f52b878d7cc478225cffac6bc977eedfe51c7f4e79
Status: Downloaded newer image for busybox:latest
docker.io/library/busybox:latest
```
* [Create Credential](https://www.howtogeek.com/devops/how-to-login-to-docker-hub-and-private-registries-with-the-docker-cli/) for docker hub

We need to log in to docker hub to push/pull for it:
```bash 
$ docker login
Login with your Docker ID to push and pull images from Docker Hub. If you dont have a Docker ID, head over to https://hub.docker.com to create one.
Username: mohammadrestech
Passowrd:

# push/pull image to docker hub: <username>/<img_name>:<version>
$ docker tag busybox  mohammadrestech/mydemo:v1
$ docker images # to see availabe images
$ docker push mohammadrestech/mydemo:v1 #push to docker-hub
$ docker logout
```

#### Docker Search
Search a specific iimage form docker hub
```bash
$ docker search <image_keyword>
# limit the output to n numbers sorted by stars
$ docker search nginx --limit 5
# filter the search result based on different property
# IS-OFFICIAL
$ docker search nginx --filter "is-official=true"
NAME      DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
nginx     Official build of Nginx.                        18787     [OK]
unit      Official build of NGINX Unit: Universal Web …   7         [OK]
```
# Certificate
I have obtained the Certificate [Docker Certified Associate - Part 1 of 2 of 8: Image Creation](Certificate_95060885_1690163815.pdf)    