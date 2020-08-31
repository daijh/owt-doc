
# Introduction

This document provides the guide to add stream to OWT with SRT protocol.

## System Requirements

- Ubuntu 18.04
- owt-server v4.3.x
- owt-client-javascript v4.3.x

## OWT Source code

### Apply SRT patch in server code
- Use following command to patch SRT PR in owt-server 4.3.x branch.

```bash
cd /home/owt-server
curl https://patch-diff.githubusercontent.com/raw/open-webrtc-toolkit/owt-server/pull/619.patch | git am
```
- Install SRT dependencies
If OWT dependencies have been installed in your environment, please run scripts/installSRT.sh to reinstall ffmpeg and webrtc dependencies only as follow:

```bash
./scripts/installSRT.sh 
or 
./scripts/installSRT.sh --with-nonfree-libs
```
Otherwise please run scripts/installDepsUnattended.sh to start a full dependencies install.

- Build OWT
If you have build and pack OWT in your environment, you can run following command to rebuild OWT:
```bash
./scripts/build.js -t mcu -r -c
```

### Apply SRT patch in client code
- Use following command to patch SRT PR in owt-client-javascript 4.3.x branch.
```bash
cd /home/owt-client-javascript
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
./scripts/pack.js -f -i -s /home/owt-client-javascript/dist/samples/conference
cd dist
cp /home/owt-js-simple-samples/index.html extras/basic_example/public/
cp /home/owt-js-simple-samples/script2.js extras/basic_example/public/
./bin/init-all.sh
./bin/start-all.sh
```

## Add SRT stream to OWT
### OWT work as caller mode(Recommended mode)
- Open https://serverip:3004 in Chrome browser
- Use ffmpeg command to start a SRT listener:
```bash
./ffmpeg -re -i /home/big-buck-bunny-animation-1080p.mp4 -c copy -f mpegts srt://10.239.158.38:20000?mode=listener
```
- Select "caller" in streaming area in UI and input SRT listener url in "srturl" in UI and then click "streamingIn SRT start" button to add stream to OWT.

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
./ffmpeg -re -i /home/big-buck-bunny-animation-1080p.mp4 -c copy -f mpegts srt://10.239.158.38:20000
```

Make sure SRT feature is enabled in ffmpeg. Please follow modules in scripts/installCommonDeps.sh if you build ffmpeg from source code:
```bash
install_srt()
install_ffmpeg()
```


Then you can see srt streaming in mix stream.
