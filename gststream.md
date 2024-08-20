```apache
apt-get install libgstreamer1.0-dev libgstreamer-plugins-base1.0-dev libgstreamer-plugins-bad1.0-dev gstreamer1.0-plugins-base gstreamer1.0-plugins-good gstreamer1.0-plugins-bad gstreamer1.0-plugins-ugly gstreamer1.0-libav gstreamer1.0-tools gstreamer1.0-x gstreamer1.0-alsa gstreamer1.0-gl gstreamer1.0-gtk3 gstreamer1.0-qt5 gstreamer1.0-pulseaudio
```

```apache
$ sudo apt install libglib2.0-dev libgstreamer1.0-dev
# Install as needed.
$ sudo apt install libgstreamer-plugins-base1.0-dev \
    gstreamer1.0-plugins-base gstreamer1.0-plugins-good \
    gstreamer1.0-plugins-bad gstreamer1.0-plugins-ugly gstreamer1.0-libav
```

检查

```apache
gst-inspect-1.0 --version
```


$ sudo apt install intel-media-va-driver-non-free libglib2.0-dev libgstreamer1.0-dev gstreamer1.0-libav gstreamer1.0-vaapi
# Install as needed.
$ sudo apt install libgstreamer-plugins-base1.0-dev 
    libgstreamer-plugins-bad1.0-dev \
    libgstreamer-plugins-base1.0-dev \
    libgstreamer-plugins-good1.0-dev \
    gstreamer1.0-plugins-base gstreamer1.0-plugins-good \
    gstreamer1.0-plugins-bad gstreamer1.0-plugins-ugly gstreamer1.0-libav gstreamer1.0-tools

$ sudo apt install  libfmt-dev libgoogle-glog-dev

sudo apt install libglib2.0-dev libgstreamer1.0-dev libgstreamer-plugins-base1.0-dev gstreamer1.0-plugins-base gstreamer1.0-plugins-good gstreamer1.0-plugins-bad gstreamer1.0-plugins-ugly gstreamer1.0-libav -y


Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
Some packages could not be installed. This may mean that you have
requested an impossible situation or if you are using the unstable
distribution that some required packages have not yet been created
or been moved out of Incoming.
The following information may help to resolve the situation:

The following packages have unmet dependencies:
 gstreamer1.0-plugins-bad : Depends: libgstreamer-plugins-bad1.0-0 (= 1.20.3-0ubuntu1.1) but 1.20.3-0ubuntu1 is to be installed
 gstreamer1.0-plugins-bad-apps : Depends: gstreamer1.0-plugins-bad (= 1.20.3-0ubuntu1) but 1.20.3-0ubuntu1.1 is to be installed
E: Error, pkgProblemResolver::Resolve generated breaks, this may be caused by held packages.



W0731 18:06:39.559147 218539 gst_video_player.cc:837] ERROR from element mppvideodec19: No valid frames decoded before end of stream
W0731 18:06:39.559244 218539 gst_video_player.cc:838] Error details: ../gst-libs/gst/video/gstvideodecoder.c(1416): gst_video_decoder_sink_event_default (): /GstPipeline:pipeline20/GstDecodebin3:decodebin3-20/GstMppVideoDec:mppvideodec19:
no valid frames found

E0731 18:29:03.023550 220821 gst_video_player.cc:649] Failed to get the current state
I0731 18:29:03.023816 220821 gst_video_player.cc:64] creating video player
I0731 18:29:03.023845 220821 gst_video_player.cc:697] uri /appdata/VCR/123456789/session111/vcr-0.mp4
I0731 18:29:03.023865 220821 gst_video_player.cc:53] current rank is 266
I0731 18:29:03.026073 220821 gst_video_player.cc:791] source name filesrc13
W0731 18:29:03.030284 220857 gst_video_player.cc:837] ERROR from element mppvideodec3: No valid frames decoded before end of stream
W0731 18:29:03.030330 220857 gst_video_player.cc:838] Error details: ../gst-libs/gst/video/gstvideodecoder.c(1416): gst_video_decoder_sink_event_default (): /GstPipeline:pipeline4/GstDecodebin3:decodebin3-4/GstMppVideoDec:mppvideodec3:
no valid frames found

echo 'export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/lib' | sudo tee -a /etc/profile
source /etc/profile


gst-launch-1.0 urisourcebin uri=rtsp://192.168.88.121/live/1 name=source ! parsebin ! decodebin3 ! videoconvert ! video/x-raw,format=RGBA ! fakesink name=testsink

