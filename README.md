This document provides the instruction to build MCU and associated web sample that allows controlling opus bitrate properly.

## System Requirement
Ubuntu 18.04

## Build MCU
pull the owt-server(MCU) source code and JavaScript SDK source code:
````
git clone --branch 4.3.x https://github.com/open-webrtc-toolkit/owt-server.git
git clone --branch 4.3.x https://github.com/open-webrtc-toolkit/owt-client-javascript.git
````

Go to your checkout source of JavaScript SDK, and apply [the patch](https://github.com/open-webrtc-toolkit/owt-client-javascript/pull/411) for JavaScript SDK with audio bandwidth control enabled:
````
git fetch origin pull/411/head:pr411
````

Follow instructions at [MCU build guideline](https://github.com/open-webrtc-toolkit/owt-server/blob/master/README.md) and [JavaScript SDK build guideline](https://github.com/open-webrtc-toolkit/owt-client-javascript/blob/master/README.md) to build MCU, and use JavaScript SDK with above patch applied to pack MCU.

## Update Sample with audio bitrate setting
Copy "index.html" and "script2.js" into "dist/extras/basic_example/public" under your MCU binary.

# Explainer to the Audio Bitrate settings
In order to set the audio bitrate for publishing(Currently only opus allows setting that), for example, to 100Kbps, in the sample, we do this:
````
function publish(localStream,audioCodec,videoCodec,videoMaxBitrate,audioMaxBitrate,simulcast,videoCodecpub,simulcastAudio) {
    let audio = false, video = false;
    let options;
    if (audioCodec.name || audioMaxBitrate) {
        audio = [{
            codec: audioCodec,
            maxBitrate: parseInt(audioMaxBitrate),
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
````
so when you're calling above publish API, make sure you set audioCodec to "opus", and "audioMaxBitrate" to 100 (in kbps).

## Set VideoCodec
- The updates sample also allows you publishing stream or subscribing stream with other codecs such as vp9:
  Enter URL "https://owt-server-ip:3004/?videoCodec=vp9"

## Set publish audio bitrate
- Set publish local stream audio bitrate in kbps, only opus supported   
  Enter URL "https://owt-server-ip:3004/?audioBitrate=128"

## Subscribe forward stream - opus stereo  
```
audio = {
    codecs: [{name: "opus", channelCount: 2, clockRate: 48000}],
    stereo: true,
  }
```

### Subscribe sample
- Select "remote stream" as forward.<br>
Postfix "-common" id is mixed stream.<br>
Non postfix "-common" id is forward stream.
- Select "trace kind" as "audio-and-video", or "audio" for audio-only.
- Select "audio codec" as "opus".
- Leave all other as default.
- Press "subscibe"

### Multiple SRT streaming inputs
- Start multiple SRT in **Listener** mode
- Fill multiple SRT streaming input URLs
  - url-1
  - url-2
  - url-3
  - url-4
- Start multiple SRT streaming inputs simultaneously in **Caller** mode

## Links
- [Streaming out setup guide](https://github.com/daijh/owt-doc/blob/master/streaming_out_setup_guide.md)
- [SRT setup guide](https://github.com/daijh/owt-doc/blob/master/owt_srt_setup_guide.md)
- [Configure publish audio bitrate](https://github.com/daijh/owt-doc/blob/master/owt_publish_audio_bitrate.md)
