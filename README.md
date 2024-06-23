This project helps build an ansible stack - you can progress and add terraform if you like. This is great for beginners to explore devops without having to worry about cloud stack/ec2. All local.

----

### Prerequisite:

 [Docker](https://docs.docker.com/desktop/) 
 
 [Nodejs](https://nodejs.org/en/download/package-manager) - used only for running the commands (you can see them under `package.json` > `scripts`)

----

Top level overview:

We intend to create 2 docker image types which will contain `ubuntu`: 
  1. master - Ansible controller machine
  This contains `openssh-server`, `ansible` installation. Check `dockerfile-main` for details.

  2. slave - Target machine
  This contains `openssh-server` installation. Check `dockerfile-common` for details.

`openssh-server` - so we can ssh inside the machine (although not really needed since docker already has built in utilities)

`ansible` - this is what we want to setup for the master machine so we can have an ansible stack to play with

----

##### Run the command below, to create docker images for 2 types of machine:
```
$ npm run docker-compose:ubuntu:machine-builder
```

##### Once the docker images are created... Run the command below, so we can run 2 ubuntu instances in our local (1 master, 1 slave)
```
$ npm run docker-compose:main:up
```

##### To Stops/Shutdown 2 running docker containers: 
```
$ npm run docker-compose:main:down
```

----

Notes:

##### Ways to access the containers - `ssh` / `docker exec`

##### To access by `ssh`, the ubuntu instances are accessible by "root"

.env file contains ssh passwords:
- `MAIN_MACHINE_SSH_PASSWORD`
- `COMMON_MACHINE_SSH_PASSWORD`

You can use any `ssh` tool you want. Here's [Putty](https://www.putty.org/) if you are old school.

----

Once the master machine is created and running, we can push the ansible codes (containing an example ansible-playbook to install `apache2` to the slave machine)

##### make directory inside the master container
```
$ docker exec -it docker-ubuntu-master-1 mkdir -p /home/user/ansible
```

##### copy the files to master container
```
$ docker cp XXX_PATH_TO_DOCKER_FILES_XXX/Docker/ansible/. docker-ubuntu-master-1:/home/user/ansible/
```

##### Then you can execute ansible playbook on the master machine (ansible ready) to run an example playbook (to install apache2)

[Ansible](https://docs.ansible.com/ansible/latest/cli/ansible-playbook.html)
```
$ ansible-playbook -i /home/user/ansible/hosts/hosts /home/user/ansible/playbook/install_apache.yml
```

Details of ansible playbook installation:
```
Host
\ansible\hosts\hosts

Installation Details (playbook)
\ansible\playbook\install_apache.yml
```

Once ansible installs the apache2 successfully, you can check the apache2 by opening this link:

[localhost:8081](localhost:8081)

----

The docker-compose implementation utilizes both `volume` and `network` settings. It’s important to ensure that the machines (`master` and `slave`) are part of the same network so they can discover each other. Since this setup is intended for `localhost` usage, the Docker containers must be able to ping each other, which means they should be configured within the same network.
