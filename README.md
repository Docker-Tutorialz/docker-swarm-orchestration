## Docker Swarm Orchestration

Just a basic overview about how you can create and  scale NGINX web application on Docker Swarm cluster. I am using a Docker Swarm cluster on VirtualBox environment to demonstrate the NGINX in action.

### `Create a NGINX web app to demonstrate the usage on Swarm`

1. First of all let's create the NGINX service on Swarm:

```bash
$ docker service create --name nginxreplicas --replicas 5 -p 80:80 nginx
kzkjkewzyk9uq9aglw0m8q4vq
overall progress: 5 out of 5 tasks
1/5: running   [==================================================>]
2/5: running   [==================================================>]
3/5: running   [==================================================>]
4/5: running   [==================================================>]
5/5: running   [==================================================>]
verify: Service converged
```

Now you can check on your browser adding your IP and see `Welcome to nginx!` page on port `80`

2. Listing the service on Swarm:

```bash
$ docker service ls
ID             NAME            MODE         REPLICAS   IMAGE            PORTS
kzkjkewzyk9u   nginxreplicas   replicated   5/5        nginx:latest     *:80->80/tcp
```

3. Listing the Swarm nodes:

```bash
$ docker node ls
ID                            HOSTNAME    STATUS    AVAILABILITY   MANAGER STATUS   ENGINE VERSION
pjvbdhnru91ezuy7wd4wi8835     elliot01    Ready     Active         Reachable        20.10.10
j5pes0j09rstvzzi3bbnc63bk     elliot02    Ready     Active         Reachable        20.10.12
xy5ihg994aenz0u7k7zl6tkkw *   mr-robbot   Ready     Active         Leader           20.10.12
```

4. Checking on how many nodes the NGINX service is running:

```bash
# docker service ps nginxreplicas
ID             NAME                  IMAGE          NODE        DESIRED STATE   CURRENT STATE            ERROR                         PORTS
w1anflp6e0y0   nginxreplicas.1       nginx:latest   elliot01    Running         Running 51 minutes ago
xnzwmil050hu   nginxreplicas.2       nginx:latest   elliot02    Running         Running 52 minutes ago
drgxv7jf5egh   nginxreplicas.3       nginx:latest   mr-robbot   Running         Running 41 minutes ago
nu0oz10p1zzx   nginxreplicas.4       nginx:latest   elliot02    Running         Running 44 minutes ago
cuzobbyqdewg   nginxreplicas.5       nginx:latest   mr-robbot   Running         Running 41 minutes ago
```

If you check your browser and add the IP of 3 nodes, you can see the NGINX app running.


### `Testing the Docker Swarm orchestration`

1. Now I will connect on some worker node to check the containers in running state:

```bash
$ docker container ls
CONTAINER ID   IMAGE          COMMAND                  CREATED          STATUS          PORTS     NAMES
bf5795b3947a   nginx:latest   "/docker-entrypoint.???"   11 seconds ago   Up 5 seconds    80/tcp    nginxreplicas.4.ionxjjxn4pgyfe93bcjelggxn
945b93602865   nginx:latest   "/docker-entrypoint.???"   52 minutes ago   Up 52 minutes   80/tcp    nginxreplicas.2.xnzwmil050hu1vvorvm0it98t
```

As you can see I can 2 Docker containers running on this worker node. I will remove now one Docker container and check again:

```bash
$ docker container rm bf5 -f
bf5
```

Now I can see the other container has been creating in 8 seconds ago:

```bash
$ docker container ls
CONTAINER ID   IMAGE          COMMAND                  CREATED          STATUS          PORTS     NAMES
30bb9732b743   nginx:latest   "/docker-entrypoint.???"   8 seconds ago    Up 1 second     80/tcp    nginxreplicas.4.acprd321djie37d81i8257nlp
945b93602865   nginx:latest   "/docker-entrypoint.???"   53 minutes ago   Up 53 minutes   80/tcp    nginxreplicas.2.xnzwmil050hu1vvorvm0it98t
```

Now you can see the NGINX app running on some worker nodes like showing this:

```
Welcome to nginx!
If you see this page, the nginx web server is successfully installed and working. Further configuration is required.

For online documentation and support please refer to nginx.org.
Commercial support is available at nginx.com.

Thank you for using nginx.
```

2. Checking on the master node:

```bash
$ docker service ps nginxreplicas
ID             NAME                  IMAGE          NODE        DESIRED STATE   CURRENT STATE               ERROR                         PORTS
w1anflp6e0y0   nginxreplicas.1       nginx:latest   elliot01    Running         Running about an hour ago
xnzwmil050hu   nginxreplicas.2       nginx:latest   elliot02    Running         Running about an hour ago
drgxv7jf5egh   nginxreplicas.3       nginx:latest   mr-robbot   Running         Running 55 minutes ago
acprd321djie   nginxreplicas.4       nginx:latest   elliot02    Running         Running 11 minutes ago
ionxjjxn4pgy    \_ nginxreplicas.4   nginx:latest   elliot02    Shutdown        Failed 12 minutes ago       "task: non-zero exit (137)"
nu0oz10p1zzx    \_ nginxreplicas.4   nginx:latest   elliot02    Shutdown        Failed 13 minutes ago       "task: non-zero exit (137)"
wsevqb4f5njz    \_ nginxreplicas.4   nginx:latest   elliot02    Shutdown        Failed 57 minutes ago       "task: non-zero exit (137)"
glzncjospafs    \_ nginxreplicas.4   nginx:latest   elliot02    Shutdown        Failed 58 minutes ago       "task: non-zero exit (137)"
cuzobbyqdewg   nginxreplicas.5       nginx:latest   mr-robbot   Running         Running 55 minutes ago
```
