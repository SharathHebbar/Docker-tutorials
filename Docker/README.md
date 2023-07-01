-d or --detach
-p or --publish

-p HOST:CONTAINER
docker container run --publish 80:80 nginx

to detach it from running on foreground

docker container run --publish 80:80 --detach nginx

Naming container
docker container run --publish 80:80 --name name-of-the-container nginx

Lists all running container
docker ps
docker container ps

docker container ls

lists all ran container 
docker container ls -a

Shows logs 
docker container logs

Remove containers
docker container rm ids/names

List running processes in the container
docker top container-name

Whats going on in containers

Process list in container
docker container top container-name

Details of one container config
docker container inspect container-name

Performance stats for all containers
docker container stats   

Run multiple container
docker container run --publish 80:80 --name nginx-container -d nginx
docker container run --publish 8080:80 --name httpd-container -d httpd

--env or -e should be used for environmental variable
docker container run --publish 3306:3306 --name mysql-container -d mysql

docker container run --publish 3306:3306 --name mysql-conatiner --env MYSQL_RANDOM_ROOT_PASSWORD=yes -d mysql


Shell -> Container 
Start container interactively
docker container run -it --name nginx-container nginx bash 

Run additional command in existing container
docker container exec -it

Start an container in interactive way
docker container start -ai nginx-container


Pulling docker images

docker pull alpine

For checking the port
docker container port container-name

For getting the IP Address
docker container inspect --format '{{ .NetworkSettings.IPAddress }}' webhost

Docker Network Commands

Show networks
docker network ls

Inspect a network 
docker network inspect

Create a network
docker network create --driver

attach a network to container
docker network container 

detach a network from container 
docker network disconnect

--network bridge
Default docker virtual network, which is NATed behind the Host IP

--network host
It gains performance by skipping virtual networks but sacrifices security of container model

--network none
removes eth0 and only leaves you with localhost interface in container

Run container on particular network 
docker network create my_test_container
docker container run -d --name new_nginx_container --network my_test_container nginx 
docker network inspect my_test_container

Connect a container to a network
Dynamically creates a NIC in a container on an existing virtual network 
docker network connect network-name<networkID> container-name<container ID> 
docker network disconnect network-name<networkID> container-name<container ID> 


Pinging from one container to another
docker container run -d --network my_test_container --name my_nginx_alpine nginx:alpine
docker container run -d --network my_test_container --name new_nginx_alpine nginx:alpine


docker container exec -it my_nginx_alpine ping new_nginx_alpine


DNS Round Robin Test
docker network create dude
docker container run -d --name dude1 --net dude --net-alias search elasticsearch:2
docker container run -d --name dude2 --net dude --net-alias search elasticsearch:2

docker container run --rm --net dude alpine nslookup search
docker container run --rm --net dude centos curl -s search:9200



Docker tagging

docker image tag sourceimage[tag] targetimage[tag]
docker image tag nginx mynginx

docker image tag mynginx mynginx:test
 
Pushing docker image to container registry
docker image push mynginx

Login and  logout
docker login
docker logout


Creating Dockerfile
docker build -f some-dockerfile
docker image build -t name .

ENV vars
One reason they were chosen as preferred way to inject key/value is they work everywhere on every OS and config

Using Prune to Keep Your Docker System Clean (YouTube)
You can use "prune" commands to clean up images, volumes, build cache, and containers. Examples include:

- docker image prune to clean up just "dangling" images

- docker system prune will clean up everything

- The big one is usually docker image prune -a which will remove all images you're not using. Use docker system df to see space usage.

Remember each one of those commands has options you can learn with --help.

Here's a YouTube video I made about it: https://youtu.be/_4QzP7uwtvI

Lastly, realize that if you're using Docker Toolbox, the Linux VM won't auto-shrink. You'll need to delete it and re-create (make sure anything in docker containers or volumes are backed up). You can recreate the toolbox default VM with docker-machine rm default and then docker-machine create


Container lifetime and persistent data

containers are usually immutable and ephemeral
immutable infrastructure: only re-deploy containers, never change
This is the ideal scenario but what about databases or unique data 
Docker gives us features to ensure these "separation of concerns"
This is known as Persistent Data"
Two ways: Volumnes and Bind Mounts
Volumes: Make special location outside of container UFS
Bind Mounts: link container path to host path


Volumes needs to be cleared after use it wont be cleared if container is deleted

named volume
docker container run -d --name mysql2 -e MYSQL_ALLOW_EMPTY_PASSWORD=True -v mysql-db:/var/lib/mysql mysql

if we delete the container and create a newer one with same named volume it will use the older volume instead of creating newer one

create volume
docker volume create volume-name

delete volume
docker volume prune 
docker volume rm volume-name

Bind mounting
Cant use in Dockerfile, must be used in container run 
maps hostfile or directory to a container file or directory
basically 2 locations pointing to the same files
again, skips UFS and host files overwrite any in container 
run -v /Users/username/stuff:/path/container  (mac or linux)
run -v //c/Users/bret/stuff:/path/container (windows)

docker container run -d --name ngnix -p 80:80 -v $(pwd):/usr/share/nginx/html nginx 

DOcker Compose
Running multiple containers

• Why: configure relationships between containers
• Why: save our docker container run settings in easy-to-read file
• Why: create one-liner developer environment startups
• Comprised of 2 separate but related things
• 1. YAML-formatted file that describes our solution options for:
• containers
• networks
• volumes
• 2. A CLI tool docker-compose used for local dev/test
automation with those YAML files

docker compose up
docker compose down
docker compose down -rmi imagename
docker compose down -v


Docker Swarm
Orchestration

initialize swarm 
docker swarm init

docker swarm init --advertise-addr <ip-addr>

replaces run command in swarm
docker service create alpine ping 8.8.8.8

Check running services
docker service ls
Shows the services

check container running inside service
docker service ps dunny_nash

Replicate the service
docker service update <id or name> --replicas 3

if we remove any container then it will replicate newer one
docker container rm -f funny_nash.1.fznz.....


if we want to remove 
docker service rm funny_nash

join token
docker swarm join-token manager

join -> will add worker node
docker swarm join --token <id> <ip addr>
worker nodes cant use swarm cmds

update worker node as manager
docker node update  --role manager node2

docker service ps <servicename>
all three services are running on different nodes


Scaling out network overlay
docker network create --driver overlay mydrupal
