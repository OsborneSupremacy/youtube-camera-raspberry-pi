# YOUTUBE CAMERA RASPBERRY PI

## Overview

Configure a Raspberry to load a specific YouTube live stream and play it full-screen automatically on startup.

Suitable for a "virtual window".

## Initial Setup

_There are many ways to prepare a Raspberry Pi for this service. Feel free to use whatever method you prefer._

### Sign up For Raspberry Pi Connect

* Raspberry Pi Connect will let you connect to your Raspberry Pi from anywhere, making administration easier.
* Sign up for an account at <https://id.raspberrypi.com/> if you don't have already have one.

### Prepare the Raspberry Pi

1. Install [Raspberry Pi Imager](https://www.raspberrypi.com/software/) on your personal computer.
2. Use Raspberry Pi Imager to install Raspberry PI OS on an SD card. Use the following settings
    * Username: `cam`
    * Password: provide any password that you will remember.
    * ✅ Configure wireless LAN
        * Check this, assuming you're going to connect via WiFi.
        * Provide the network SSID and Password
3. Install the SD card in the Raspberry PI.
4. Boot up the Raspberry PI.
5. Once the system is booted up, enable Raspberry Pi Connect.
    * Sign in with your Raspberry PI ID.
6. Close all web browsers.

### Construct a YouTube URL

Do this step from your personal computer.

You are going to want a YouTube URL that plays a YouTube video (preferably one that runs indefinitely, such as a live cam) in full screen.

#### Sample URL

This README will use this URL as a sample. You can replace `KY7acUJJmuU` with the ID of your preferred video.

```none
https://www.youtube.com/embed/KY7acUJJmuU?rel=0&autoplay=1&fs=1&vq=hd720&mute=1
```

The parameters below are used in the URL. You probably don't need to modify them, but if you do, here's what they do.

* `embed` makes the video fullscreen (relative to the OS)
* `fs=1` makes the video fullscreen (relative to the browser)
* `autoplay=1` makes the video play automatically
* `vq=hd1080` sets the quality (not locked in -- YouTube can adjust this automatically)
* `mute=1` mutes the sound

### Update Service Configuration

Copy the configuration below configuration to a text editor. Update it with your URL from the previous step.

```console
[Unit]
Description=Live Stream
After=network.target

[Service]
ExecStart=/usr/bin/chromium-browser --start-fullscreen --app=https://www.youtube.com/embed/KY7acUJJmuU?rel=0&autoplay=1&fs=1&vq=hd720&mute=1
WorkingDirectory=/home/cam
Restart=on-failure
User=cam
Environment=DISPLAY=:0

[Install]
WantedBy=multi-user.target
```

### Create Service

1. On your personal computer, go to <https://connect.raspberrypi.com/>.
    * You should see you device listed.
2. Select `Connect via` ➡️ `Remote shell`
    * The remote shell should connect successfully.
    * The commands below should all be run in the remote shell.
3. Run `sudo nano /etc/systemd/system/youtube-cam.service`
4. Copy and paste the service definition from your text editor to Nano.
5. Exit Nano, saving the file.
6. Run `sudo systemctl enable youtube-cam.service`
    * This makes the service start on boot up.
7. Run `sudo systemctl start youtube-cam.service`
    * This starts the service manually.
    * If it doesn't start, something's wrong. Review the previous steps, or start from scratch.
8. Run `sudo reboot` to reboot the Pi, and verify that the video starts.

## Modifying

### Change WiFi network

1. After powering the device on, exit the video playing service (hold ESC for a few seconds to exit full-screen, then close the window).
2. Make the necessary WiFi updates using the native Raspberry PI OS interface.
3. Reboot the PI.

### Change Video

_This can be done directly in the PI, or via Raspberry PI Connect_

1. Construct a URL for your new video using the instructions from the previous section.
2. Run `sudo systemctl stop youtube-cam.service` to stop the service.
3. Run `sudo nano /etc/systemd/system/youtube-cam.service`
4. Replace the URL in the file with your new one.
5. Exit Nano, saving the file.
6. Run `sudo systemctl start youtube-cam.service` to restart the service.

## Troubleshooting

In general, try rebooting before you try any other troubleshooting steps.

### Syncing the video with realtime

* The video can fall behind realtime. If you change the playspeed speed to something faster than 1x, it will eventually catch up.

