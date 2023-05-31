## Capturing Video at Regular Intervals
The following example code can be run on the camera pi to capture and save video at regular intervals.  The captured footage is stored locally on the Pi and must be manually transferred to the device running the AI back end.  In the below example, the footage is stored on a USB drive mounted on `/mnt/usb/.
```python
from picamera import PiCamera
from time import sleep
from datetime import datetime
 
picamera = PiCamera()
picamera.resolution = (720, 480)
picamera.framerate = 1
 
n = 0 # keeps track of the number of clips saved

RECORDING_INTERVAL = 15 # the number of minutes that the camera records for at each interval
 
while(True):
        # check to see if it is between the hours of 7:00 am and 5:30 pm
        time = str(datetime.now().time())[0:5].split(':')
        #print(time)
        if int(time[0]) > 7 and int(time[0]) < 18:
                # time to record!
                print('recording...')
                picamera.start_recording('/mnt/usb/clip'+str(n)+'_{0}.h264'.format(datetime.now().strftime("%Y-%m-%d")))
                sleep(RECORDING_INTERVAL*60)
                picamera.stop_preview()
                picamera.stop_recording()
                sleep(3600 - RECORDING_INTERVAL*60)
                n += 1
        else:
                # wait
                print('not a good time, waiting...')
                sleep(3600) # wait an hour
```

This script can be configured to run on boot using systemctl.  Save the above script and take note of the file location.  Create a systemd configuration file for this script using the following command:
```bash
sudo nano /etc/systemd/system/local_video_capture.service
```

Add the following content:
```
[Unit]  
Description=Captures video at regular intervals and saves the footage locally
After=multi-user.target

[Service]  
Type=simple  
Restart=always  
ExecStart=/usr/bin/python3 /location/of/the/above/script.py

[Install]  
WantedBy=multi-user.target
```

Save the file, and run the following command to make sure the script will run successfully on boot:
```bash
sudo systemctl daemon-reload
sudo systemctl enable local_video_capture.service
sudo systemctl start local_video_capture.service
sudo systemctl status local_video_capture.service
```
If the above command return that the script is running without error, it is now configured to run when the Pi boots.

## Offloading the Video
After [deployment](Deployment.md), the captured video must be offloaded from the Pi in order to be run through the AI back end.  This can be done using the `scp` command. 

Begin by ssh-ing into the directory where the above script has been storing video.  Use the `zip` command to compress the captured video:
```bash
zip recordings.zip *.h264
```

Next, use `scp` to transfer the zip file to the device ssh-ing into the Pi.  Run the following command on this other device:
```bash
scp [username]@[hostname].local:where/the/recordings/are/stored/on/the/pi/recordings.zip /path/to/save/file
```

## Running the Captured Footage Through the AI Back End

The back end can be configured as follows to process the collected footage (where the above zip file has been extracted to `./clips/Deployment`):
```python
try:
    # iterate over each frame in the video file captured locally on the camera pi
    for clip in sorted(os.listdir('./clips/Deployment')):
        video = './clips/Deployment/' + clip
        cap = cv2.VideoCapture(video)
        clip_date = clip.split('_')[1].split('.')[0] # just get the date from file name of the form clipN_[date].h264

        # iterate over the frames
        while cap.isOpened():
            # read the current frame
            ret, frame = cap.read()

            if not ret:
                break

            frame_image = Image.fromarray(frame)
            frame_image.convert('RGB').save('fish.png')
            # rotate the image
            #frame_image = frame_image.rotate(90)
            run_model(frame_image)

        # release the video capture object
        cap.release()
except KeyboardInterrupt:
    print('exiting...')
```

