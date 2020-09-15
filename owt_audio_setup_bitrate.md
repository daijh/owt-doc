介绍：
本文档提供了使用如何build一个可以设置audio 比特率的mcu包
系统要求：
Ubuntu 18.04
Owt server v4.3.x
编译打包：
拉下来源码
git clone --branch 4.3.x https://github.com/open-webrtc-toolkit/owt-server.git
git clone --branch 4.3.x https://github.com/open-webrtc-toolkit/owt-client-javascript.git
cd owt-client-javascript
切换到pr411：git fetch origin pull/411/head:pr411
完成后，rebuild js，rebuild mcu
设置audio 比特率演示
如下设置audioMaxBitrate即可，譬如100就是100k

function publish(localStream,audioCodec,videoCodec,videoMaxBitrate,audioMaxBitrate,simulcast,videoCodecpub,simulcastAudio) {
    let audio = false, video = false;
    let options;
    if (audioCodec.name || audioMaxBitrate) {
        audio = [{
            codec: audioCodec,
            maxAverageBitrate: parseInt(audioMaxBitrate),
        }]
    }
    if (videoCodec.name || videoMaxBitrate) {
        video = [{
            codec: videoCodec,
            maxBitrate: parseInt(videoMaxBitrate),
        }]
    }
    if (simulcast === "true"){
        if(simulcastAudio === "true"){
            options = {
                audio : true,
                video : SimulcastOptionsVideo
            }
        }
        else if(simulcastAudio === "false" || simulcastAudio === ""){
            options = {
                audio : false,
                video : SimulcastOptionsVideo
            }
        }
    }
    else
    {

        options = {
            audio: audio,
            video: video,
        };
    }
    let stream = localStreams.get(localStream.source.video || localStream.source.audio);
    return new Promise(function (resolve, reject) {
        let pubcation;
        client.addEventListener('streamadded', (e) => {
            resolve([e.stream, pubcation])
        })
        client.publish(stream, options, [videoCodecpub]).then((publication) => {
            publications.set(publication.id, publication);
            console.log(`get publication ${publication.id}`);
            pubcation = publicationTmp = publication;

        }, (err) => {
            console.log('publish local stream failed: ', err);
            reject(err);
        })

    })
}

