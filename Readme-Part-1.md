# Docker Certified Associate
## Part 1 of 8: Get Started with Docker

### Downlaod Image from docker-hub repository
```
docker pull nginx
docker images # list available images
```

### Start the Container
we could do 'port binding' `-p` `host_port:container_port` in detached `-dt` mode by naming the image: 
```
docker run -dt -p 8080:80 nginx
docker ps # show status of docker containers

```

### Start container with use defined name
```
docker run --name mynginx -dt -p 8080:80 nginx
```

### Running a docker container in background and foreground
```
docker run --name attached -dt -p 8080:80 nginx #---foreground, everything will come in the terminal 
docker run --name detached -dt -p 8080:80 nginx
```

## Checking the nginx Docker Server from command-line
The nginx server is runing under local server host ip: `127.0.0.1:8080` and we can verify in command-line as:
```
C:\Restech\Learning\Docker>curl -I 127.0.0.1:8080
HTTP/1.1 200 OK
Server: nginx/1.25.1
Date: Fri, 21 Jul 2023 01:24:10 GMT
Content-Type: text/html
Content-Length: 615
Last-Modified: Tue, 13 Jun 2023 15:08:10 GMT
Connection: keep-alive
ETag: "6488865a-267"
Accept-Ranges: bytes
```

## Stop and Remove unused container
```
docker ps -a #-- list of containers with their status

docker container stop <name/uuid/short_id>

docker container ls -aq #--IDs of the docker containers currenlty running

#--- stop each and every containers that are in the system
docker container stop $(docker container ls -aq)

```

Removing container/s:
```
docker container rm <name/uuid/short_id>
docker container rm $(docker container ls -aq)
```

## Run a new command in a running container via `exec`
create a new container
```
docker container run -d --name docker-exec nginx
# see if the container has started
docker ps
```
try to connect the `bash` of the newly created container from commandline:
```
docker container exec -it docker-exec bash
```

## Container Commands
Importance of IT (`i`:stdin, `t`: tty) flag:  
```
# Login to the container
docker connect exec -it
```

Default container commands: Container will run the command with pid=1 (default) while creating `.dockerfile`
```
docker container run -d nginx sleep 500 # override default command and create a new container valid for 500 secs
docker start <name/uuid/short_id>
```

## Policies and Usage
Docker restart policies
we can define the policy by `--restart` tag
```
no : do not automatically restart (default)
on-failure : if it exits due to an error (non-zero exit code)
unless-stopped : unless explicitly stopped/restarted
always : always restart if stops
```

For production we have to specify the unless-stopped so that we do not need to restart manually
```
docker container run -d --restart unless-stopped nginx
```

Removing unnecessary Container Images we created during development phase
```
docker rm <name/uuid/short_id> 
#--remove a running container (stop then remove)
docker rm -f <name/uuid/short_id> 
```

Analyse the Disk Usage of Docker Components
```
df -h #for linux will show the disk usage/utilisation

#--componnet level disk matrix
C:\Restech\Learning\Docker>docker system df
TYPE            TOTAL     ACTIVE    SIZE      RECLAIMABLE
Images          1         1         186.9MB   0B (0%)
Containers      3         1         3.281kB   2.186kB (66%)
Local Volumes   0         0         0B        0B
Build Cache     0         0         0B        0B
```
to see component level info:
```
docker system df -v
C:\Restech\Learning\Docker>docker system df -v
Images space usage:

REPOSITORY   TAG       IMAGE ID       CREATED       SIZE      SHARED SIZE   UNIQUE SIZE   CONTAINERS
nginx        latest    021283c8eb95   2 weeks ago   187MB     0B            186.9MB       3

Containers space usage:

CONTAINER ID   IMAGE     COMMAND                  LOCAL VOLUMES   SIZE      CREATED       STATUS                   NAMES
e54a964c4d0e   nginx     "/docker-entrypoint.…"   0               1.09kB    3 hours ago   Exited (0) 3 hours ago   attached
622218e757a2   nginx     "/docker-entrypoint.…"   0               1.09kB    3 hours ago   Up 3 hours               detached
3c2505394939   nginx     "/docker-entrypoint.…"   0               1.09kB    4 hours ago   Exited (0) 4 hours ago   heuristic_wilson

Local Volumes space usage:

VOLUME NAME   LINKS     SIZE

Build cache usage: 0B

CACHE ID   CACHE TYPE   SIZE      CREATED   LAST USED   USAGE     SHARED
```

Automaticalluy Deletre Container on exit
After the job is completed, if we need to delete it automatically using `--rm` option while running the docker container


# Certificate
I have obtained the Certificate [Docker Certified Associate - Part 1 of 8: Get Started with Docker](Certificate_95029568_1689913284.pdf)