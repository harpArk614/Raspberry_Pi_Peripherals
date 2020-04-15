# Raspberry Pi Camera Module

## Introduction
The Raspberry Pi Camera Module is a high quality 8 megapixel Sony IMX219 imagesensor custom designed add-on board for Raspberry Pi, featuring a fixed focus lens.

It is capable of 3280x2464 pixel static images, and also supports 1080p30, 720p60 and 640x480p90 video.

It attaches to Pi by way of one of the small sockets on the board upper surface and uses the dedicated CSi interface, designed especially for interfacing to cameras.
There are two versions of the Camera Module:

* The standard version, which is designed to take pictures in normal light
* The NoIR version, which doesn’t have an infrared filter, so you can use it together with an infrared light source to take pictures in the dark

## Setting up Raspberry Pi Camera
### Physical Installation

**Ensure your Raspberry Pi is turned off.** <br>
**Warning:** Cameras are sensitive to static. Earth yourself prior to handling the PCB. A sink tap or similar should suffice if you don’t have an earthing strap.

The camera board attaches to the Raspberry Pi via a 15-way ribbon cable. There are only two connections to make: the ribbon cable needs to be attached to the camera PCB, and to the Raspberry Pi itself. You need to get the cable the right way round, or the camera will not work. On the camera PCB, the blue backing on the cable should face away from the PCB, and on the Raspberry Pi it should face towards the Ethernet connection (or where the Ethernet connector would be if you're using a model A).

Although the connectors on the PCB and the Pi are different, they work in a similar way. On the Raspberry Pi itself, pull up the tabs on each end of the connector. It should slide up easily, and be able to pivot around slightly. Fully insert the ribbon cable into the slot, ensuring it is set straight, then gently press down the tabs to clip it into place. The camera PCB connector also requires you to pull the tabs away from the board, gently insert the cable, then push the tabs back. The PCB connector can be a little more awkward than the one on the Pi itself.

![Installation Image](https://projects-static.raspberrypi.org/projects/getting-started-with-picamera/eb7defb950e2f3eeb8aa5934d26cfd600860c8a0/en/images/pi-camera-attached.jpg)

The camera may come with a small piece of translucent blue plastic film covering the lens. This is only present to protect the lens while it is being mailed to you, and needs to be removed by gently peeling it off.

### Enabling the camera
Execute the following instructions on the command line to download and install the latest kernel, GPU firmware, and applications. You'll need an internet connection for this to work correctly.

```
sudo apt update
sudo apt full-upgrade
```

Now you need to enable camera support using the `raspi-config` program you will have used when you first set up your Raspberry Pi.

```
sudo raspi-config
```

Use the cursor keys to select and open `Interfacing Options`, and then select `Camera` and follow the prompt to enable the camera.

Upon exiting `raspi-config`, it will ask to `reboot`. The enable option will ensure that on reboot the correct GPU firmware will be running with the camera driver and tuning, and the GPU memory split is sufficient to allow the camera to acquire enough memory to run correctly.

To test that the system is installed and working, try the following command:

```
raspistill -v -o test.jpg
```

The display should show a five-second preview from the camera and then take a picture, saved to the file `test.jpg`, whilst displaying various informational messages.

You can also enable the camera from the Raspberry Pi Desktop application.
* Start up your Raspberry Pi.
* Go to the main menu and open the Raspberry Pi Configuration tool.
* Select the Interfaces tab and ensure that the camera is enabled
* Reboot your Raspberry Pi.

## Linux Terminal commands for Raspberry Pi Camera module
There are four applications provided: `raspistill`, `raspivid`, `raspiyuv` and `raspividyuv`. All the applications are driven from the command line.

`raspistill` and `raspiyuv` are very similar and are intended for capturing images; `raspivid` and `raspvidyuv` are for capturing video.

In addition, it's possible to omit the filename option (in which case the preview is displayed but no file is written), or to redirect all output to stdout.

### raspistill
`raspistill` is the command line tool for capturing still photographs with the camera module.

****
```
 --output    -o    Output filename <filename>
```

Specify the output filename. If not specified, no file is saved. If
the filename is '-', then all output is sent to stdout.<br>
Example: Taking a default capture on viewfinder, saving in image.jpg

```
raspistill  -o image.jpg
```
Example: Take a picture and send image data to stdout
```
raspistill -t 2000 -o -
```
Example: Take a picture and send image data to file
```
raspistill -t 2000 -o - > my_file.jpg
```
****
```
--timeout   -t    Time before capture and shut down
```

The program will run for this length of time, then take the capture
(if output is specified). If not specified, this is set to 5 seconds..<br>
Example: Taking a default capture after two seconds (note times are
specified in milliseconds) on viewfinder, saving in image.jpg

```
raspistill -t 2000 -o image.jpg
```
Example: Run preview ONLY for two seconds, no saved image.

```
raspistill -t 2000
```

****
```
--width   -w    Set image width <size>
--height    -h    Set image height <size>
```

Change the image width and height respectively.<br>
Example: Take a capture at a different resolution

```
raspistill -t 2000 -o image.jpg -w 640 -h 480 
```
****
```
--quality   -q    Set jpeg quality <0 to 100>
```

Change the image quality.Quality 100 is almost completely uncompressed. 75 is a good allround value.<br>
Example: reduce the quality considerably to reduce file size

```
raspistill -t 2000 -o image.jpg -q 5 
```

****
```
--preview   -p    Preview window settings <'x,y,w,h'>
```

Allows the user to define the size and location on the screen that
the preview window will be placed. Note this will be
superimposed over the top of any other windows/graphics.<br>
Example: Force the preview to appear at coordinate 100,100, with width
300 and height 200 pixels. 

```
raspistill -t 2000 -o image.jpg -p 100,100,300,200
```
****
```
--nopreview   -n    Do not display a preview window
```

Disables the preview window completely. Note that even though
the preview is disabled, the camera will still be producing frames,
so will be using power.<br>
Example: Disable preview entirely.

```
raspistill -t 2000 -o image.jpg -n 
```
****
```
--encoding, -e Encoding to use as output file
```

Valid options are jpg, bmp, gif and png. Note that unaccelerated
image types (gif, png, bmp) will take much longer to save than
jpg, which is hardware accelerated. Also note that the filename
suffix is completely ignored when encoding a file<br>
Example: Save the image as a png file (lossless compression, but slower
than JPEG).

```
raspistill -t 2000 -o image.png –e png
```
****
```
--timelapse   -tl   Timelapse mode
```

The specific value is the time between shots in milliseconds.
Note you should specify %04d at the point in the filename where
you want a frame count number to appear.<br>
Example: To produce a capture every 2 seconds over a total period of
30s, named image1.jpg, image0002.jpg...image0015.jpg. Note
that the %04d indicates a four-digit number with leading zeros
added to pad to the required number of digits. So, for example,
%08d would result in an eight-digit number.

```
raspistill -t 30000 -tl 2000 -o image%04d.jpg
```
Example: Take timelapse picture, one every 10 seconds for 10 minutes (10
minutes = 600000ms), named image_number_1_today.jpg,
image_number_2_today.jpg onwards.

```
raspistill -t 600000 -tl 10000 -o
image_num_%d_today.jpg 
```

****
```
--imxfx   -ifx    Set image effect
```
 Effect Suffix  | Effect Type
------------ | -------------
none | No effect
negative | Produces a negative image
solarise | Solarise the image
whiteboard | Whiteboard effect
blackboard | Blackboard effect
sketch | Sketch-style effect
denoise | Denoise the image
emboss | Embossed effect
oilpaint | Oil paint-style effect
hatch | Cross-hatch sketch style
gpen | Graphite sketch style
pastel | Pastel effect
watercolour | Watercolour effect
film | Grainy film effect
blur | Blur the image
saturation | Colour-saturate the image
colourswap | Not fully implemented
washedout | Not fully implemented
posterise | Not fully implemented
colourpoint | Not fully implemented
colourbalance | Not fully implemented
cartoon | Not fully implemented
<br>
Example: Set an emboss style image effect.

```
raspistill -t 2000 -o image.jpg -ifx emboss
```
****
```
--colfx   -cfx    Set colour effect <U:V>
```

The supplied U and V parameters (range 0 to 255) are applied to
the U and Y channels of the image. For example, --colfx 128:128
should result in a monochrome image.<br>
Example: Set the U and V channels of the YUV image to specific values
(128:128 produces a greyscale image).

```
raspistill -t 2000 -o image.jpg -cfx 128:128 
```
****

### raspistillyuv
Many of the options for `raspistillyuv` are the same as those for `raspistill`. 

Unsupported Options:
```
--exif, --encoding, --thumb, --raw, --quality
```

Extra Options:
```
--rgb   -rgb    Save uncompressed data as RGB888
```
This option forces the image to be saved as RGB data with 8 bits
per channel, rather than YUV420

Note that the image buffers saved in raspistillyuv are
padded to a horizontal size divisible by 16 (so there may be
unused bytes at the end of each line to made the width divisible
by 16). Buffers are also padded vertically to be divisible by 16,
and in the YUV mode, each plane of Y,U,V is padded in this way.

### raspivid
Image size and preview settings are the same as for stills
capture. Default size for video recording is 1080p (1920x1080)

****
```
--output, -o Output filename <filename>.
```
Specify the output filename. If not specified, no file is saved. If
the filename is '-', then all output is sent to stdout.<br>
Example: Record a 5s clip with default settings (1080p30)

```
raspivid -t 5000 -o video.h264
```
Example: Encode a 5s camera stream and send image data to stdout
```
raspivid -t 5000 -o -
```

Example: Encode a 5s camera stream and send image data to file
```
raspivid -t 5000 -o - > my_file.h264
```

****
```
--width, -w Set image width <size>
--height, -h Set image height <size>

```
Set the width and height of resulting video. Width should be between 64 and 1920. Height should be between 64 and 1080.<br>
Example:  Record a 5s clip at 720p

```
raspivid -t 5000 -o video.h264 -w 1280 -h 720
```

****
```
--bitrate, -b Set bitrate
```

Use bits per second, so 10MBits/s would be -b 10000000. For
H264, 1080p a high quality bitrate would be 15Mbits/s or more.<br>
Example: Record a 5s clip at a specified bitrate (3.5MBits/s)

```
raspivid -t 5000 -o video.h264 -b 3500000
```

****
```
--framerate, -fps Specify the frames per second to record
```

At present, the minimum frame rate allowed is 2fps, the
maximum is 30fps. This is likely to change in the future.<br>
Example:Record a 5s clip at a specified framerate (5fps)

```
raspivid -t 5000 -o video.h264 -f 5
```
****

## Python Module for Raspberry Pi Camera
### picamera
This package provides a pure Python interface to the Raspberry Pi camera module for Python 2.7 (or above) or Python 3.2 (or above).
### Installation
To install picamera on Raspbian, it is best to use the system’s package manager: apt. This will ensure that picamera is easy to keep up to date, and easy to remove should you wish to do so. It will also make picamera available for all users on the system. To install picamera using apt simply run:

```
sudo apt-get update
sudo apt-get install python-picamera python3-picamera
```
On distributions other than Raspbian, it is probably simplest to install system wide using Python’s pip tool:
```
sudo pip install picamera
```
### picamera API
To use picamera package in your python program import the picamera module
```
import picamera
```
