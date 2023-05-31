## Streaming to YouTube from the Pi

The following example code demonstrates how to stream to a streaming service such as YouTube from the Pi.  The shell script can be configured to run on boot.  Note this requires the Pi to be connected to the internet via WiFi, Ethernet, or a radio connection as described in  [connectivity](connectivity.md).
```bash
#! /bin/bash
 
VBR="2500k"                                    # bitrate of the video
FPS="30"                                       # FPS
QUAL="medium"                                  # the quality of the stream
YOUTUBE_URL="rtmp://a.rtmp.youtube.com/live2"  # replace with your youtube RTMP URL
 
SOURCE="/dev/video0"                           # source UDP
KEY="**************"                           # replace with your yt livestreaming key
 
ffmpeg \
    -i "$SOURCE" -deinterlace \
    -vcodec libx264 -pix_fmt yuv420p -preset $QUAL -r $FPS -g $(($FPS * 2)) -b:v $VBR \
    -acodec libmp3lame -ar 44100 -threads 6 -qscale 3 -b:a 712000 -bufsize 512k \
    -f flv "$YOUTUBE_URL/$KEY"
```

This script can be configured to run on boot using systemctl.  Save the above script and take note of the file location.  Create a systemd configuration file for this script using the following command:
```bash
sudo nano /etc/systemd/system/live_stream.service
```

Add the following content:
```
[Unit]  
Description=Starts a live stream of the pis camera view
After=multi-user.target

[Service]  
Type=simple  
Restart=always  
ExecStart=/location/of/the/above/script.sh

[Install]  
WantedBy=multi-user.target
```

Save the file, and run the following command to make sure the script will run successfully on boot:
```bash
sudo systemctl daemon-reload
sudo systemctl enable live_stream.service
sudo systemctl start live_stream.service
sudo systemctl status live_stream.service
```
If the above command return that the script is running without error, it is now configured to run when the Pi boots.

## Running the Captured Footage Through the AI Back End

The back end can be configured as follows to view the live stream:
```python
import cv2
import multiprocessing
import os
import shutil
import subprocess
import time
from PIL import Image, ImageFile, ImageOps
import yt_dlp

# define a function that downloads frames from the live stream on a separate thread
def start_download(url, ydl_opts):
    print('starting download')
    yt_dlp.YoutubeDL(ydl_opts).download([url])
    print('ending download')
    

# set up vidgear to capture images from the youtube stream
url = 'https://www.youtube.com/watch?v=lXzSU7ezjp8'

# define the options for downloading the video
ydl_opts = {
    'format': 'best[ext=mp4]',
    'quiet': True,
    'no_warnings': True,
    'outtmpl': './clips/live.mp4'
}

# remove previous downloads
if os.path.exists('./clips/live.mp4.part'):
    os.remove('./clips/live.mp4.part')
if os.path.exists('./clips/live.mp4'):
    os.remove('./clips/live.mp4')
if os.path.exists('./clips/saved.mp4'):
    os.remove('./clips/saved.mp4')

# start the download process
download_proc = multiprocessing.Process(target=start_download, args=(url, ydl_opts,))
download_proc.start()

# start with 2 seconds of video to run the model on
while not os.path.exists('./clips/live.mp4.part'):
  pass
time.sleep(2)
shutil.copyfile('./clips/live.mp4.part', './clips/saved.mp4')

StreamCapture.SHOW_PREDICTION_IMGS = False

# try running FishNET!
try:
    # open the downloaded video using OpenCV
    last_frame = 1
    cap = cv2.VideoCapture('./clips/saved.mp4')
    cap.set(cv2.CAP_PROP_POS_FRAMES, last_frame)

    while True:
        # read from the downloaded portion of the live stream
        ret, frame = cap.read() # get the latest frame from the live stream

        # if there are no more frames, copy the contents of live.mp4 to saved.mp4
        if not ret:
            shutil.copyfile('./clips/live.mp4.part', './clips/saved.mp4')

            # reopen the file with cv2 making sure to resume at the last frame
            last_frame = int(cap.get(cv2.CAP_PROP_POS_FRAMES))
            cap = cv2.VideoCapture('./clips/saved.mp4')
            cap.set(cv2.CAP_PROP_POS_FRAMES, last_frame)
            continue

        frame_image = Image.fromarray(cv2.cvtColor(frame, cv2.COLOR_BGR2RGB))
        frame_image.convert('RGB').save('fish.png')
        StreamCapture.run_model(frame_image)

except KeyboardInterrupt:
    cap.release()
    download_proc.terminate()
```