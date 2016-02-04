# Supplejack Docker
Docker implementation of Supplejack stack (API, Manager, Worker, MongoDB, Redis and Solr). 

### Features
- Redis container
- Solr container
- Data container (Linked to host machine for development)
- Supplejack worker container
- Supplejack manager container
- Supplejack API container

### Prerequisites
- Dinghy
- Weave
- Docker Machine
- Docker Compose
- Virtualbox

### Getting Started
1. Install [Virtualbox](https://www.virtualbox.org/wiki/Downloads).
2. Install [Docker Toolbox](https://docs.docker.com/mac/step_one/).
3. Install [dinghy](https://github.com/codekitchen/dinghy).
4. Install [Weave](https://github.com/weaveworks/weave).
5. Clone this project. `git clone git@github.com:DigitalNZ/supplejack_docker.git`.

### Preparation

##### Go to project directory
`> cd supplejack_docker`

##### Create a new dinghy VM
`dinghy create --provider virtualbox`

##### Verify that dinghy HTTP proxy container runs.
`> docker ps`
```bash
CONTAINER ID        IMAGE                           COMMAND                  CREATED             STATUS              PORTS                         NAMES
4a9a0d3ec048        codekitchen/dinghy-http-proxy   "/app/docker-entrypoi"   2 days ago          Up 2 days           0.0.0.0:80->80/tcp, 443/tcp   dinghy_http_proxy
```

##### Launch Weave
```bash
> weave launch
> eval $(weave env)
```

##### Verify all docker networking services is running
`> docker ps`
```bash
CONTAINER ID        IMAGE                           COMMAND                  CREATED             STATUS              PORTS                         NAMES
ef75240f57c2        weaveworks/weaveexec:1.4.2      "/home/weave/weavepro"   47 hours ago        Up 47 hours                                       weaveproxy
40770b440599        weaveworks/weave:1.4.2          "/home/weave/weaver -"   47 hours ago        Up 47 hours                                       weave
4a9a0d3ec048        codekitchen/dinghy-http-proxy   "/app/docker-entrypoi"   2 days ago          Up 2 days           0.0.0.0:80->80/tcp, 443/tcp   dinghy_http_proxy
```

Note: You need to run these commands for each iTerm/Terminal tabs you make. I suggest you modify your `~/.bash_profile` or `~/.zshrc` file to automate these commands.

```bash
> eval $(dinghy shellinit)
> eval "$(weave env)"
```

### Running Docker Containers

##### Go to project directory
`> cd supplejack_docker`

##### Copy your ssh key to api, worker and manager
```bash
> cp ~/.ssh/id_rsa api/id_rsa
> cp ~/.ssh/id_rsa manager/id_rsa
> cp ~/.ssh/id_rsa worker/id_rsa
```

If you don't have one, refer to this [link](https://help.github.com/articles/generating-an-ssh-key/) to create one.

##### Build Docker containers (This will take a while)
`> docker-compose build`

##### Run Docker containers
`> docker-compose up --no-recreate`

##### Access Supplejack components

```
API: api.docker:3000
Manager: manager.docker:3001
Worker: worker.docker:3002/sidekiq
Solr: solr.docker:8983/solr/#/collection1
MongoDB Data: Mounted in host data/db - mongodb.weave.local:27017
Redis: redis://redis.weave.local:6379/0
```

##### Stop Docker containers and cleanup
Everytime you stop Docker containers, it is ideal to remove them as well. 

`> docker-compose stop && docker-compose rm -v`

You should end up with 3 docker containers `weaveproxy`, `weave` and `dinghy_http_proxy`.

```bash
> docker ps
CONTAINER ID        IMAGE                           COMMAND                  CREATED             STATUS              PORTS                         NAMES
ef75240f57c2        weaveworks/weaveexec:1.4.2      "/home/weave/weavepro"   2 days ago          Up 2 days                                         weaveproxy
40770b440599        weaveworks/weave:1.4.2          "/home/weave/weaver -"   2 days ago          Up 2 days                                         weave
4a9a0d3ec048        codekitchen/dinghy-http-proxy   "/app/docker-entrypoi"   2 days ago          Up 2 days           0.0.0.0:80->80/tcp, 443/tcp   dinghy_http_proxy
```
