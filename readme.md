# SENOMAS DOCKER

<!-- TOC -->

- [SENOMAS DOCKER](#senomas-docker)
    - [EXPORT IMAGE](#export-image)
    - [IMPORT IMAGE](#import-image)
    - [PORTAINER](#portainer)
    - [CLEAN UP](#clean-up)
    - [REGISTRY](#registry)
    - [INSTALL UBUNTU 16.04](#install-ubuntu-1604)

<!-- /TOC -->

## [DOCKER P2P](p2p.md)

## EXPORT IMAGE

```bash
docker save -o <file name>.tar <instance name>
```

## IMPORT IMAGE

```bash
docker load -i <file name>.tar
```

## PORTAINER

```bash
docker run --name portainer --restart=always -d -p 9000:9000 \
   -v /var/run/docker.sock:/var/run/docker.sock \
   portainer/portainer
```

## CLEAN UP

```bash
docker rmi $(docker images -q -f dangling=true)
docker volume rm $(docker volume ls -qf dangling=true)
docker run -v /var/run/docker.sock:/var/run/docker.sock -v /var/lib/docker:/var/lib/docker --rm martin/docker-cleanup-volumes
```

## REGISTRY

```bash
docker run -d -p 5000:5000 --restart=always --name registry \
  -v /docker/registry/data:/var/lib/registry \
  -v /docker/registry/auth:/auth \
  -v /docker/registry/certs/:/certs \
  -e "REGISTRY_AUTH=htpasswd" \
  -e "REGISTRY_AUTH_HTPASSWD_REALM=Registry Realm" \
  -e "REGISTRY_AUTH_HTPASSWD_PATH=/auth/htpasswd" \
  -e "REGISTRY_HTTP_TLS_CERTIFICATE=/certs/code.senomas.com.crt" \
  -e "REGISTRY_HTTP_TLS_KEY=/certs/code.senomas.com.key" \
  registry:2

docker run --entrypoint htpasswd registry:2 -Bbn dev dodol123 > /docker/registry/auth/htpasswd
```

```bash
docker run -d -p 5000:5000 --restart=always --name registry \
  -v /home/docker/registry/data:/var/lib/registry \
  -v /home/docker/registry/auth:/auth \
  -e "REGISTRY_AUTH=htpasswd" \
  -e "REGISTRY_AUTH_HTPASSWD_REALM=Registry Realm" \
  -e REGISTRY_AUTH_HTPASSWD_PATH=/auth/htpasswd \
  registry:2
```

```bash
docker run -d -p 5000:5000 --restart=always --name registry \
  -v /home/docker/registry/data:/var/lib/registry \
  -v /home/docker/registry/auth:/auth \
  registry:2
```

## INSTALL UBUNTU 16.04

But first, let's update the package database:

```bash
sudo apt-get update
```

Now let's install Docker. Add the GPG key for the official Docker repository to the system:

```bash
sudo apt-key adv --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys 58118E89F3A912897C070ADBF76221572C52609D
```

Add the Docker repository to APT sources:

```bash
echo "deb https://apt.dockerproject.org/repo ubuntu-xenial main" | sudo tee /etc/apt/sources.list.d/docker.list
```

Update the package database with the Docker packages from the newly added repo:

```bash
sudo apt-get update
```

Make sure you are about to install from the Docker repo instead of the default Ubuntu 16.04 repo:

```bash
apt-cache policy docker-engine
```

You should see output similar to the follow:
Output of apt-cache policy docker-engine

```bash
docker-engine:
  Installed: (none)
  Candidate: 1.11.1-0~xenial
  Version table:
     1.11.1-0~xenial 500
        500 https://apt.dockerproject.org/repo ubuntu-xenial/main amd64 Packages
     1.11.0-0~xenial 500
        500 https://apt.dockerproject.org/repo ubuntu-xenial/main amd64 Packages
```

Notice that docker-engine is not installed, but the candidate for installation is from the Docker repository for Ubuntu 16.04. The docker-engine version number might be different.

Finally, install Docker:

```bash
sudo apt-get install -y docker-engine
```

Docker should now be installed, the daemon started, and the process enabled to start on boot. Check that it's running:

```bash
sudo systemctl status docker
```

The output should be similar to the following, showing that the service is active and running:

```bash
Output
● docker.service - Docker Application Container Engine
   Loaded: loaded (/lib/systemd/system/docker.service; enabled; vendor preset: enabled)
   Active: active (running) since Sun 2016-05-01 06:53:52 CDT; 1 weeks 3 days ago
     Docs: https://docs.docker.com
 Main PID: 749 (docker)
```

Installing Docker now gives you not just the Docker service (daemon) but also the docker command line utility, or the Docker client. We'll explore how to use the docker command later in this tutorial.

Step 2 — Executing the Docker Command Without Sudo (Optional)
By default, running the docker command requires root privileges — that is, you have to prefix the command with sudo. It can also be run by a user in the docker group, which is automatically created during the installation of Docker. If you attempt to run the docker command without prefixing it with sudo or without being in the docker group, you'll get an output like this:

Output
docker: Cannot connect to the Docker daemon. Is the docker daemon running on this host?.
See 'docker run --help'.
If you want to avoid typing sudo whenever you run the docker command, add your username to the docker group:

```bash
sudo usermod -aG docker $(whoami)
```

You will need to log out of the Droplet and back in as the same user to enable this change.

If you need to add a user to the docker group that you're not logged in as, declare that username explicitly using:

```bash
sudo usermod -aG docker username
```
