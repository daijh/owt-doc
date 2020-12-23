
# Introduction

This document provides the guide to add stream to OWT with SRT protocol.

## System Requirements

- Ubuntu 18.04
- owt-server v4.3.x
- owt-client-javascript v4.3.x

## OWT Source code

### Apply SRT patch in server code
- Use following command to patch SRT PR in owt-server 4.3.x branch.<br>
It includes SRT streaming input and multiple SRT synchronization.

```bash
git clone https://github.com/open-webrtc-toolkit/owt-server.git
git checkout -t remotes/origin/4.3.x

cd owt-server

# srt streaming input and synchronization
curl https://patch-diff.githubusercontent.com/raw/open-webrtc-toolkit/owt-server/pull/744.patch | git am

# fix streaming out type error
curl https://patch-diff.githubusercontent.com/raw/open-webrtc-toolkit/owt-server/pull/795.patch | git am
```

- Install dependencies

```bash
cd owt-server

# re-install deps
rm ./build -rf
./scripts/installDepsUnattended.sh
```

- Build OWT

```bash
./scripts/build.js -t mcu -r -c
```

### Apply SRT patch in client code
- Use following command to patch SRT PR in owt-client-javascript 4.3.x branch.

```bash
git clone https://github.com/open-webrtc-toolkit/owt-client-javascript.git
git checkout -t remotes/origin/4.3.x

cd owt-client-javascript

curl https://patch-diff.githubusercontent.com/raw/open-webrtc-toolkit/owt-client-javascript/pull/401.patch | git am
```

- Build client sdk with following command

```bash
cd owt-client-javascript/scripts/
grunt
```

### Pack and run OWT 
Pack OWT server and client and then start OWT:
```bash
cd owt-server

# create dist
./scripts/pack.js --full --install-module --no-pseudo --with-ffmpeg --sample-path <PATH>/owt-client-javascript/dist/samples/conference

cd ./dist

# update SRT test page and js script
cp owt-js-simple-samples/index.html extras/basic_example/public/
cp owt-js-simple-samples/script2.js extras/basic_example/public/

./bin/init-all.sh
./bin/start-all.sh
```

## Add SRT stream to OWT
### OWT work as caller mode (***Recommended mode***)
- Open https://serverip:3004 in Chrome browser
- Use ffmpeg command to start a SRT listener

```bash
# <ip> is local ip, set <port> to 20000 for test
ffmpeg -re -i test.mp4 -c copy -f mpegts srt://<ip>:<port>?mode=listener
```

- Select "caller" in streaming area in UI and input SRT listener url in "url" in UI and then click "streamingIn SRT start" button to add stream to OWT.

### OWT work as listener mode
- Open https://serverip:3004 in Chrome browser
- Use default listener mode in UI and click "streamingIn SRT start" button to start a SRT listener in OWT, then the ip and port will return by the restful request and print in Chrome console. You can specify SRT listen port range in streaming_agent.toml:

```bash
# The SRT listening UDP port range
maxport = 20000 #default: 0
minport = 10000 #default: 0
```

- Use ffmpeg command to push a mp4 stream to owt server with following command:

```bash
# <ip> is local ip, set <port> to 20000 for test
./ffmpeg -re -i test.mp4 -c copy -f mpegts srt://<ip>:<port>
```

Then you can see srt streaming in mix stream.
