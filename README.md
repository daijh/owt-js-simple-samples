# owt-js-simple-samples

## INSTALL
copy "index.html" and "script2.js" into "owt-server/dist/extras/basic_example/public"

## Set videoCodec
- Set publish local stream video codec and subscrib remote mixed stream video codec as vp9  
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
