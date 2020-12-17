# 360 video docker guide

## **360 Server**

## Setup docker
- Import image

```bash
# load centos-7.6-ovc-server-f
docker load -i centos-7.6-ovc-server
```

- Start container

```bash
# run container 360-server
docker run --net="host" \
--privileged \
-e http_proxy \
-e https_proxy \
-e no_proxy \
-e DISPLAY \
--name 360-server \
-itd centos-7.6-ovc-server-f \
/bin/bash

# attach to container 360-server
docker attach 360-server
```

## Start service in container

```bash
# in 360-server container
cd /home/owt-server/dist
./bin/init-all.sh

# run service
run_360Server.sh

# configure 4k
cd /home/RESTful
./restApi.sh # usage
./restApi.sh -s 4k

# restart service to make configuration to take effect
run_360Server.sh

# start input
cd /home/RESTful
./restApi.sh -c /home/Sample-Videos/test1_h265_3840x2048_30fps_30M_200frames.mp4

# log for debug
cd /home/owt-server/dist/logs
```

## **360 Client**

## Setup docker
- Import image

```bash
# load ubuntu-18.04-ovc-client-f
docker load -i ubuntu-18.04-ovc-client
```

- Start container

```bash
# run container 360-client
docker run --net="host" \
--privileged \
-e http_proxy \
-e https_proxy \
-e no_proxy \
-e DISPLAY \
--name 360-client \
-itd ubuntu-18.04-ovc-client-f \
/bin/bash

# Do not need to attach to container, only copy source into host
# docker attach 360-client
```

## Start client in host
- Copy into ubuntu-18.04 host for developement

```bash
docker cp 360-client:/home/ovc/Immersive-Video-Sample
```

- Build

```bash
# install deps
sudo -E apt update
sudo -E apt install -y git build-essential wget python cmake pkg-config libglib2.0-dev libgtk-3-dev libasound2-dev libpulse-dev
sudo -E apt install -y yasm libgoogle-glog-dev libva-dev libglm-dev libglfw3-dev libgles2-mesa-dev libglu1-mesa-dev liblzma-dev

# build
cd Immersive-Video-Sample/WebRTC-Sample/owt-linux-player

# delete cmake cache, and rebuild
rm -rf player
./build_player.sh

# configure player
cd player/

# <resolution>4k</resolution>
# <server_url>http://owt-server-ip:3001</server_url>
vi config.xml

# run
# Press "arrow" keys to change viewport
# Press "Esc" key to exit
./render

# log
cd logfiles
```

