# owt-server debug

## pstree
```
pstree -a -T -g
# or
pstree -a -T -g USER
```

>  ├─node,35416 . -U audio<br>
>  │   ├─node,36786 ./workingNode audio-8649ecc080e98ab0a429@10.239.61.11_2 audio-8649ecc080e98ab0a429@10.239.61.11...<br>
>  │   ├─node,37310 ./workingNode audio-8649ecc080e98ab0a429@10.239.61.11_3 audio-8649ecc080e98ab0a429@10.239.61.11...<br>
>  │   └─node,37796 ./workingNode audio-8649ecc080e98ab0a429@10.239.61.11_4 audio-8649ecc080e98ab0a429@10.239.61.11...<br>
>  ├─node,35416 . -U video<br>
>  │   ├─node,36794 ./workingNode video-f1e4c5bdb0c2148e35c2@10.239.61.11_2 video-f1e4c5bdb0c2148e35c2@10.239.61.11...<br>
>  │   ├─node,37317 ./workingNode video-f1e4c5bdb0c2148e35c2@10.239.61.11_3 video-f1e4c5bdb0c2148e35c2@10.239.61.11...<br>
>  │   └─node,37803 ./workingNode video-f1e4c5bdb0c2148e35c2@10.239.61.11_4 video-f1e4c5bdb0c2148e35c2@10.239.61.11...<br>
>  ├─node,35416 . -U streaming<br>
>  │   ├─node,36835 ./workingNode streaming-27d16371cb247492de10@10.239.61.11_2 streaming-27d16371cb247492de10@10.239.61.11...<br>
>  │   ├─node,37361 ./workingNode streaming-27d16371cb247492de10@10.239.61.11_3 streaming-27d16371cb247492de10@10.239.61.11...<br>
>  │   └─node,37845 ./workingNode streaming-27d16371cb247492de10@10.239.61.11_4 streaming-27d16371cb247492de10@10.239.61.11...<br>
>  ├─node,35416 . -U webrtc<br>
>  │   ├─node,36063 ./workingNode webrtc-0347eb08545ce819f7e5@10.239.61.11_0 webrtc-0347eb08545ce819f7e5@10.239.61.11...<br>
>  │   ├─node,36064 ./workingNode webrtc-0347eb08545ce819f7e5@10.239.61.11_1 webrtc-0347eb08545ce819f7e5@10.239.61.11...<br>
>  │   └─node,41580 ./workingNode webrtc-0347eb08545ce819f7e5@10.239.61.11_2 webrtc-0347eb08545ce819f7e5@10.239.61.11...<br>

## dist
```
cd owt-server/dist
tree -L 1
```

>├── analytics_agent<br>
├── audio_agent<br>
├── bin<br>
├── cluster_manager<br>
├── conference_agent<br>
├── extras<br>
├── logs<br>
├── management_api<br>
├── management_console<br>
├── NOTICE<br>
├── package.json<br>
├── portal<br>
├── recording_agent<br>
├── sip_agent<br>
├── sip_portal<br>
├── streaming_agent<br>
├── ThirdpartyLicenses.txt<br>
├── video_agent<br>
└── webrtc_agent<br>

## dist/logs
```
cd owt-server/dist/log
tree -L 1
```

>├── ```streaming-27d16371cb247492de10@10.239.61.11_0.log```<br>
├── ```streaming-27d16371cb247492de10@10.239.61.11_1.log```<br>
├── ```streaming-27d16371cb247492de10@10.239.61.11_2.log```<br>
├── ```streaming-27d16371cb247492de10@10.239.61.11_3.log```<br>
├── ```streaming-27d16371cb247492de10@10.239.61.11_4.log```<br>
├── ```video-f1e4c5bdb0c2148e35c2@10.239.61.11_0.log```<br>
├── ```video-f1e4c5bdb0c2148e35c2@10.239.61.11_1.log```<br>
├── ```video-f1e4c5bdb0c2148e35c2@10.239.61.11_2.log```<br>
├── ```video-f1e4c5bdb0c2148e35c2@10.239.61.11_3.log```<br>
├── ```video-f1e4c5bdb0c2148e35c2@10.239.61.11_4.log```<br>
├── ```webrtc-0347eb08545ce819f7e5@10.239.61.11_0.log```<br>
├── ```webrtc-0347eb08545ce819f7e5@10.239.61.11_1.log```<br>
├── ```webrtc-0347eb08545ce819f7e5@10.239.61.11_2.log```<br>

- pid<br>
pid of each node is log for debug (ps, gdb attach, ...)
> 2021-03-16 10:38:47.857  - INFO: WorkingNode - pid: 36142

- Manualy delete all logs, then start owt-server to capture clean logs for debug.

## logs for debug

1. Simple but useful, search "error/ERROR" key word in logs. It will indentify most obvious error. 
```
cd owt-server/dist/log
grep error -rnsi *
```

>```conference-1e4a0a31be63e8127b68@10.239.61.11_0.log```:7:2021-03-16 10:39:34.442  - INFO: AccessController - onFailed, sessionId: 151518900229859360 reason: error opening input url<br>
```streaming-27d16371cb247492de10@10.239.61.11_0.log```:8:2021-03-16 10:39:34,438  - ERROR: owt.LiveStreamIn - (0x28903d0)Error opening input No such file or directory<br>
```streaming-27d16371cb247492de10@10.239.61.11_0.log```:9:2021-03-16 10:39:34,438  - ERROR: owt.LiveStreamIn - (0x28903d0)Connect failed, {"type":"failed","reason":"error opening input url"}<br>
```streaming-27d16371cb247492de10@10.239.61.11_2.log```:10:2021-03-16 10:41:06,650  - WARN: owt.LiveStreamIn - (0x32412e0)Error read frame, End of file<br>

2. Search "SIGTERM" in logs. nodes are closed by "SIGTERM".
```
cd owt-server/dist/log
grep SIGTERM -rnsi *
```

3. Look all "video-xxx_x.log", "streaming-xxx_x.log", "webrtc-xxx_x.log", crash or callstack will be log at the end.

4. dmesg

5. Enable more logs by log controllers
    - log level
      1. INFO (for release)
      2. DEBUG (for debug)
      3. TRACE (not recommended, quite a log output)

    - log4cxx.properties<br>
control native cpp log, restart owt-server to take effect

    - log4js_configuration.json<br>
control js log, restart owt-server to take effect

```
cd owt-server/dist/video_agent
vi log4cxx.properties

# log4j.logger.owt.SVTHEVCEncoderBase=INFO
# log4j.logger.owt.SVTHEVCEncoder=INFO
# log4j.logger.owt.SVTHEVCMCTSEncoder=INFO
#
# INFO->DEBUG
#
# log4j.logger.owt.SVTHEVCEncoderBase=DEBUG
# log4j.logger.owt.SVTHEVCEncoder=DEBUG
# log4j.logger.owt.SVTHEVCMCTSEncoder=DEBUG
```

## mem leak and cpu usage
```
top

htop

free

#GNOME System Monitor
```

## gdb
TODO

## Stop server
Suggest to clean all node processes to stop owt-server
```
sudo killall -9 node
```
