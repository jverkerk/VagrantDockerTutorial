# VagrantDockerTutorial

Conceptual tutorial:
Tutorial URL : https://docs.docker.com/engine/swarm/swarm-tutorial/#/three-networked-host-machines 
Prerequisites for the above tutorial is to have three machines running with Docker Engine.
To make life easier the following git repo will have a Vagrant file that can be used to stand up three machines
for the tutorial; https://github.com/jverkerk-cb/VagrantDockerTutorial
Notes:
The Vagrant file will allocate specific IP addresses to each of the hosts. Use those IPs in the tutorial to not
have to deal with network port exporting etc.
How to use:
Git clone the git repo into a separate folder on your machine and run the command vagrant up.
Once done you'll have three CentOS 7.2 machines available each of which has docker-engine installed on it.
On the manager1 node you'll want to run the docker visualizer to be able to follow along what's happening.
Log onto manager1 (vagrant ssh manager1) and run the following command;
docker run -it -d -p 5000:5000 -e HOST=172.20.20.10 -e PORT=5000 -v /var/run/docker.sock:/var/run/docker.sock manomarks/visualizer

Open up a browser and connect to http://172.20.20.10:5000 to see the visualization of what you are doing in the tutorial.
Follow the tutorial

Extra things you may want to try:
Play with the different service modes (replicated vs. global)
Take a node out of the swarm ("docker swarm leave" on one of the workers
Join the worker back in ("docker swarm join" on the worker that left the swarm)
What happens if you have the manager leave the swarm.

Use case tutorial;
While the last tutorial was great for understanding the concept of Docker swarm, it didn't give you a "usable" setup.
In this tutorial we'll once again use roughly the same machines, however this time we'll stand up a swarm that runs
NGINX and serves up a simple webpage. Also we'll add in an extra machine that runs HAproxy to simulate how we'd
be using this in the real world. 

Clone repo for vagrant files (in seperate folder)
https://github.com/jverkerk-cb/DockerSwarm 
Start the vagrant boxes 
Vagrant up
Log into manager1 and start docker visualizer:
docker run -it -d -p 5000:5000 -e HOST=172.20.20.10 -e PORT=5000 -v /var/run/docker.sock:/var/run/docker.sock manomarks/visualizer
Initialize swarm (on manager1)
docker swarm init --advertise-addr 172.20.20.10
Have the two workers join the swarm:
docker swarm join --token {generated token} 172.20.20.10:2377
Log back onto manager1 and create service with all sorts of options (smile) :
docker service create --mount type=bind,src=/docker-nginx/html,dst=/usr/share/nginx/html --name docker_nginx --replicas 3 --update-delay 10s --publish 80:80 nginx
Check status of the new service on command line:
docker service ps docker_nginx
OR check Docker swarm visualizer;
http://172.20.20.10:5000/
Check the status of HAproxy by opening up the following link. 
http://172.20.20.15/haproxy?stats
If thing are running properly you should see something like this:
Systems Engineering > Docker Swarm - tutorials > image2016-8-31 20:38:29.png
Now test our docker swarm by testing this link:
http://172.20.20.15/




equivalent docker-engine command:
docker run --name docker-nginx -p 80:80 -d -v /docker-nginx/html:/usr/share/nginx/html nginx

