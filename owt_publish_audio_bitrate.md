
# Introduction

This document provides the guide to configurate published audio bitrate on browser (opus only).

## System Requirements

- Ubuntu 18.04
- owt-server v4.3.x
- owt-client-javascript v4.3.x
- Chrome (Version 81.0.4044.138 and later)

## Setup

- Apply patch on js sdk

```bash
# download code
git clone --branch 4.3.x https://github.com/open-webrtc-toolkit/owt-client-javascript.git

# apply patch
cd owt-client-javascript
git fetch origin pull/411/head:pr411
git checkout pr411
```

- **Rebuid js sdk and re-pack mcu**

- Install simple js samples  
See [owt-js-simple-samples](https://github.com/daijh/owt-js-simple-samples) doc
