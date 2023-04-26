## Streaming to YouTube from the Pi

The following example code demonstrates how to stream to a streaming service such as YouTube from the Pi.  The shell script can be configured to run on boot.  Note this requires the Pi to be connected to the internet via WiFi, Ethernet, or a radio connection as described in  [connectivity](connectivity.md).
```bash
# TODO add bash script to strart live stream on the pi
```

This script can be configured to run on boot using systemctl.  Save the above script and take note of the file location.  Create a systemd configuration file for this script using the following command:
```bash
sudo nano /etc/systemd/system/live_stream.service
```

Add the following content:
```json
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

```