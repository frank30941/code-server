# code-server
online editor on docker and vscode
Quick start


``` shell
docker run --rm \
  --name=code-server2 \
  -e PUID=$(id -u) \
  -e PGID=$(id -g) \
  -e TZ=Asia/Taipei \
  -e PASSWORD=password `#optional` \
  -e SUDO_PASSWORD=password `#optional` \
  -e DOCKER_MODS=linuxserver/mods:code-server-docker \
  -p 8080:8080 \
  -v /var/run/docker.sock:/var/run/docker.sock \
  frank30941/code-server
```
## Build image
``` shell
cd code-server/server/
docker build --build-arg CODEUSER={yourname} -t="frank30941/code-server" . // Or your docker hub
```

## Registe DNS
I used the DNS service of [NCTU](https://nctu.me/) which the platform provides a free level 2 domain name.

## Set .env file
``` sh
CODEUSER=XXX
CODEDOMAINNAME=XXX
SUDO_PASSWORD=''
PASSWORD=XXX
```

## Set uid & gid
``` sh
echo 'export UID=$(id -u);' >> ~/.zshrc ; # or bashrc
echo 'export GID=$(id -g);' >> ~/.zshrc ; # or bashrc
```

## Referance
[demyx](https://hub.docker.com/r/demyx/code-server)
