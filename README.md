## Docker Swarm Orchestration

Just a basic overview about how you can create and  scale NGINX web application on Docker Swarm cluster. 

### `Create a NGINX web app to demonstrate the usage on Swarm`

1. First of all let's create the NGINX service on Swarm:

```
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

```
$ docker service ls
ID             NAME            MODE         REPLICAS   IMAGE            PORTS
kzkjkewzyk9u   nginxreplicas   replicated   5/5        nginx:latest     *:80->80/tcp
```

3. Listing the Swarm nodes:

```
$ docker node ls
ID                            HOSTNAME    STATUS    AVAILABILITY   MANAGER STATUS   ENGINE VERSION
pjvbdhnru91ezuy7wd4wi8835     elliot01    Ready     Active         Reachable        20.10.10
j5pes0j09rstvzzi3bbnc63bk     elliot02    Ready     Active         Reachable        20.10.12
xy5ihg994aenz0u7k7zl6tkkw *   mr-robbot   Ready     Active         Leader           20.10.12
```

4. Checking on how many nodes the NGINX service is running:

```
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



