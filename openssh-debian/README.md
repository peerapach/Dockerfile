## inspired by linuxserver/openssh-server - https://github.com/linuxserver/docker-openssh-server

openssh-debian is a debian linux sandboxed environment that allows ssh access.

Usage
Here are some example snippets to help you get started creating a container.

### Use by public key by env
```
docker run -d \
  --name=openssh-server \
  --hostname=debian `#optional` \
  -e PUID=1000 \
  -e PGID=1000 \
  -e TZ=Asia/Bangkok \
  -e PUBLIC_KEY=yourpublickey `#optional` \
  -e SUDO_ACCESS=false `#optional` \
  -e PASSWORD_ACCESS=false `#optional` \
  -e USER_NAME=debian `#optional` \
  -p 2222:2222 \
  -v /path/to/config:/config \
  --restart unless-stopped \
  peerapach/openssh-debian
```

### Use public key from file

```
docker run -d \
  --name=openssh-server \
  --hostname=debian `#optional` \
  -e PUID=1000 \
  -e PGID=1000 \
  -e TZ=Asia/Bangkok \
  -e PUBLIC_KEY_FILE=/path/to/temp/.ssh/public_key_file `#optional` \
  -e SUDO_ACCESS=false `#optional` \
  -e PASSWORD_ACCESS=false `#optional` \
  -e USER_NAME=debian `#optional` \
  -p 2222:2222 \
  -v ${HOME}/.ssh/:/path/to/temp/.ssh \
  --restart unless-stopped \
  peerapach/openssh-debian
```
### Use password

```
docker run -d \
  --name=openssh-server \
  --hostname=debian `#optional` \
  -e PUID=1000 \
  -e PGID=1000 \
  -e TZ=Asia/Bangkok \
  -e USER_PASSWORD=password \
  -e SUDO_ACCESS=false `#optional` \
  -e PASSWORD_ACCESS=true `#optional` \
  -e USER_NAME=debian `#optional` \
  -p 2222:2222 \
  --restart unless-stopped \
  peerapach/openssh-debian
```
