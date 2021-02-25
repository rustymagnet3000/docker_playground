# Docker command reminders

### Getting started

#### Info

`docker info`

#### Version

`docker --version`

#### Check docker is running

`docker run busybox date`

#### Where is docker

`which docker`

#### List local images

`docker image ls`

#### Pull ( slim linux image )

`docker pull alpine`

#### Quick setup

```docker
docker pull alpine:latest

docker run -it alpine 
```

### Build

#### Dockerfile, list, print commands inside Dockerfile and delete

```docker
docker build -f DockerfileAlpineNonRoot -t alpine_non_root:0.1 .
docker image ls
docker image history alpine_non_root
docker image rm alpine_non_root:0.1
```

#### Order matters

For an efficient use of the caching mechanism, [reference](https://www.docker.com/blog/containerized-python-development-part-1/):
> place the instructions for layers that change frequently after the ones that incur less changes.

```python
# Changes less frequently
COPY requirements.txt .

# install dependencies
RUN pip install -r requirements.txt

# Changes often
COPY src/ .
```

### General commands

#### View size of image

`docker image ls`

#### Show Container IDs

`docker ps`

#### Show Container IDs with memory footprint of the Thin R/W Layer

`docker ps -s`

#### A history of images and container IDs

`docker ps -a`

#### All exited container IDs

`docker ps --all --filter STATUS=exited`

#### All running container IDs

`docker ps --all --filter STATUS=running`

#### Load service [first time only]

`docker load -i foobar.tar.gz`

#### Start container

`docker start ctf`

#### Stop container

`docker stop ctf`

### Run

#### Interactive, terminal

`docker run -it ubuntu`

#### Interactive, terminal specify Bash

`docker run -it ubuntu bash`

#### Automatically remove container when it exits

`docker run --rm -it ubuntu`

#### Name container for Docker Container ls

`docker run --name foobar -it ubuntu`

#### Run service in background

`docker run -d -p 80:8181 blah_swagger/foobar`

#### Interactive, detach and allocate Pseudo Terminal

`docker run -idt ...`

#### Run in privileged

`docker run --privileged`

#### Run in non-privileged mode

`docker run -idt --name ctf duckll/ctf-box`

#### App Armor

`docker run --rm -it --security-opt apparmor=docker-default duckll/ctf-box`

### Audit

#### Image history

`docker image history alpine_non_root`

#### History

`docker history foobar:v1`

#### Logs from Container ID

`docker logs bd0657a17d54`

#### check if container is running as Privileged

`docker inspect --format='{{.HostConfig.Privileged}}' <container id>`

#### Stats

`docker stats < container ID >`

### Copy

#### from Host to Docker Container

`docker cp foo/bar.c bd0657a17d54://root/newbar.c`

#### check if image can mount disk on Host

`mount -t tmpfs none /mnt`

#### Run interactive Terminal with Cut and Paste

`docker container exec -it ctf bash`

#### Stop by Container ID

`docker stop <container id>`

#### Stop by Image name

`docker stop foobar-service`

### Remove

#### Container ( removed before Image removal )

`docker container rm <container id>`

#### Remove Image

`docker image rm <image id> --force`

#### Remove all stopped containers

`docker rm $(docker ps -a -q)`

#### Remove Image, force

`docker rmi -f duckll/ctf-box`

#### Security cheat sheet

<https://cheatsheetseries.owasp.org/cheatsheets/Docker_Security_Cheat_Sheet.html>

## circleci

### local setup

It was essential that you debug the `config.yaml` file before uploading to circleci.

```bash
brew install --ignore-dependencies circleci
circleci version
circleci setup
 - go to web interface for CircleCI and get Personal Access Token
 - Just press enter on the host
circleci context
```

### On every config.yaml change, run

```bash
circleci config process .circleci/config.yml > process.yml
circleci local execute -c process.yml --job build-and-test    
```

### Environment variables

circleci local execute -c process.yml --job build_test -e VAR1=FOO

## Snyk

### Setup

```bash
brew install npm
npm install -g snyk
snyk version
snyk auth               // prompts for password
< login via GitHub / Docker account >
```

### Check for vulnerabilities

```bash
snyk container test busybox
snyk container test $(basename $(pwd)) --file=Dockerfile
snyk test --docker ubuntu_vanilla --file=DockerfileUbuntu --exclude-base-image-vuln
```

## Docker CVEs

[CVE-2019-5736: runc container breakout](https://seclists.org/oss-sec/2019/q1/119)
