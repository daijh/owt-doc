# Introduction
Guide for owt-server 360 video development

## System Requirements
- Centos 7

## Build

```bash
# install gcc7
sudo -E yum install devtoolset-7
source /opt/rh/devtoolset-7/enable
gcc -v


# install cmake3, needed by safestringlib
wget https://cmake.org/files/v3.13/cmake-3.13.1.tar.gz
tar -zvxf cmake-3.13.1.tar.gz
cd cmake-3.13.1
./bootstrap
make -j8
sudo -E make install


# download owt-server source code
git clone https://github.com/open-webrtc-toolkit/owt-server.git

cd owt-server

git reset --hard fd71357d6fdbd57d3c4be2028976bc2b34fff781
wget https://patch-diff.githubusercontent.com/raw/open-webrtc-toolkit/owt-server/pull/708.patch
git am 708.patch

# install deps
# sometimes this step will fail silently
# change "#!/bin/bash" -> "#!/bin/bash -ex", in "scripts/installCommonDeps.sh" for debug
./scripts/installDepsUnattended.sh

# nvm is installed in user path
source ~/.bashrc
nvm use v10 # add this line into end of .bashrc

# build
./scripts/build.js -t mcu -r -c


# download js client (under the same path with owt-server)
git clone https://github.com/open-webrtc-toolkit/owt-client-javascript.git

cd owt-client-javascript

git reset --hard d727af2927731ff16214d73f57964a992258636d

# build js client
cd owt-client-javascript/scripts
npm install -g grunt-cli
npm install
grunt


# create owt-server/dist
cd owt-server

./scripts/pack.js --full --install-module --no-pseudo --with-ffmpeg --sample-path ../owt-client-javascript/dist/samples/conference


# donwload Immersive-Video-Sample (under the same path with owt-server)
git clone https://github.com/OpenVisualCloud/Immersive-Video-Sample.git

cd Immersive-Video-Sample

git reset --hard 1f088f206be59a2a2fc9f59e7e6611d46f2da7f1

# install owt-server configuration/streaming control scripts
cd Immersive-Video-Sample/WebRTC-Sample/owt-server/image/owt-immersive/scripts/scripts
npm install
```

## Run Server

```bash
cd owt-server/dist

# init owt-server, only init once after reboot
./bin/init.all
# Update RabbitMQ/MongoDB Account? [No/yes]
# Press "Enter" to use default RabbitMQ/MongoDB account

# start owt-server
./bin/start-all.sh

# stop owt-server
./bin/stop-all.sh
killall -9 node

# check logs for debug
ls logs/

# restart owt-server = stop + rm logs + start
./bin/stop-all.sh
killall -9 node

rm logs/* -v

./bin/start-all.sh
```

## Start 360 video streaming

```bash
cd Immersive-Video-Sample/WebRTC-Sample/owt-server/image/owt-immersive/scripts/

# start owt-server
# configure owt-server, 4k or 8k
./restApi.sh -s 4k
# restart owt-server to take into effect

# start input, 4k or 8k
# it is ready for connction from clients
./restApi.sh  -c  Immersive-Video/Immersive-Video-Sample/Sample-Videos/test1_h265_3840x2048_30fps_30M_200frames.mp4
# check logs
# owt-server/dist/logs/streaming-xxx-x.log
# owt-server/dist/logs/video-xxx-x.log
# owt-server/dist/logs/webrtc-xxx-x.log
```

## Rebuild scvp
- Create *simple_install_deps.sh* under *owt-server/scripts*

```source
#!/bin/bash
SCRIPT=`pwd`/$0
FILENAME=`basename $SCRIPT`
PATHNAME=`dirname $SCRIPT`
ROOT=$PATHNAME/..
BUILD_DIR=$ROOT/build
CURRENT_DIR=`pwd`

LIB_DIR=$BUILD_DIR/libdeps
PREFIX_DIR=$LIB_DIR/build/
DISABLE_NONFREE=true
CLEANUP=false
NIGHTLY=false
NO_INTERNAL=false
INCR_INSTALL=false

cd $PATHNAME
. installCommonDeps.sh

mkdir -p $PREFIX_DIR

reinstall_360scvp(){
    install_safestringlib

    local SCVP_VER="v1.2.0"
    local SCVP_REPO=https://github.com/OpenVisualCloud/Immersive-Video-Sample.git

    mkdir -p ${LIB_DIR}
    pushd ${LIB_DIR}

    git clone ${SCVP_REPO}
    pushd Immersive-Video-Sample/src/360SCVP
    git checkout ${SCVP_VER}
    mkdir build
    pushd  build

    sed -i "s@INCLUDE_DIRECTORIES\(.*\)@INCLUDE_DIRECTORIES\1\nINCLUDE_DIRECTORIES(${PREFIX_DIR}/include)@" ../CMakeLists.txt
    sed -i "s@LINK_DIRECTORIES\(.*\)@LINK_DIRECTORIES\1\nLINK_DIRECTORIES(${PREFIX_DIR}/lib)@" ../CMakeLists.txt

    cmake -DCMAKE_INSTALL_PREFIX=${PREFIX_DIR} -DCMAKE_INSTALL_LIBDIR=lib ../
    make -j
    make install

    popd
    popd
    popd
}

reinstall_360scvp
```

- Build scvp

```bash
cd owt-server

# delete previous source
rm build/libdeps/safestringlib/ -rf
rm build/libdeps/Immersive-Video-Sample/

# reinstall deps
./scripts/simple_install_deps.sh
# check libs
ls build/libdeps/build/lib/

# rebuild owt-server
./scripts/build.js -t mcu -r -c

# copy to dist
cp  -v  source/agent/webrtc/webrtcLib/build/Release/webrtc.node  dist/webrtc_agent/webrtcLib/build/Release/webrtc.node

cp  -v  build/libdeps/build/lib/lib360SCVP.so  dist/webrtc_agent/lib/lib360SCVP.so
# restart owt-server
```

## Known limitations
- TODO

## Useful links
- Immersive WebRTC sample<br>
https://github.com/OpenVisualCloud/Immersive-Video-Sample/tree/master/WebRTC-Sample
