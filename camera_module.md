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

![Camera Module Port](https://cdn.shopify.com/s/files/1/0176/3274/articles/rpi_1024x.png?v=1558435064)

Although the connectors on the PCB and the Pi are different, they work in a similar way. On the Raspberry Pi itself, pull up the tabs on each end of the connector. It should slide up easily, and be able to pivot around slightly. Fully insert the ribbon cable into the slot, ensuring it is set straight, then gently press down the tabs to clip it into place. The camera PCB connector also requires you to pull the tabs away from the board, gently insert the cable, then push the tabs back. The PCB connector can be a little more awkward than the one on the Pi itself.

![Installation Image](https://cdn-shop.adafruit.com/1200x900/1367-07.jpg)

The camera may come with a small piece of translucent blue plastic film covering the lens. This is only present to protect the lens while it is being mailed to you, and needs to be removed by gently peeling it off.

### Enabling the camera
Execute the following instructions on the command line to download and install the latest kernel, GPU firmware, and applications. You'll need an internet connection for this to work correctly.

```bash
sudo apt update
sudo apt full-upgrade
```

Now you need to enable camera support using the `raspi-config` program you will have used when you first set up your Raspberry Pi.

```bash
sudo raspi-config
```

Use the cursor keys to select and open `Interfacing Options`, and then select `Camera` and follow the prompt to enable the camera.

Upon exiting `raspi-config`, it will ask to `reboot`. The enable option will ensure that on reboot the correct GPU firmware will be running with the camera driver and tuning, and the GPU memory split is sufficient to allow the camera to acquire enough memory to run correctly.

To test that the system is installed and working, try the following command:

```bash
raspistill -v -o test.jpg
```

The display should show a five-second preview from the camera and then take a picture, saved to the file `test.jpg`, whilst displaying various informational messages.

You can also enable the camera from the Raspberry Pi Desktop application.
* Start up your Raspberry Pi.
* Go to the main menu and open the Raspberry Pi Configuration tool.

![Configuration Tool](https://www.pyimagesearch.com/wp-content/uploads/2019/07/remote_development_raspbian_desktop_rpi_configuration.png)

* Select the Interfaces tab and ensure that the camera is enabled 

![Interfaces tab](https://picamera.readthedocs.io/en/release-1.12/_images/enable_camera.png)

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

```bash
raspistill  -o image.jpg
```
Example: Take a picture and send image data to stdout
```bash
raspistill -t 2000 -o -
```
Example: Take a picture and send image data to file
```bash
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

```bash
raspistill -t 2000 -o image.jpg
```
Example: Run preview ONLY for two seconds, no saved image.

```bash
raspistill -t 2000
```

****
```
--width   -w    Set image width <size>
--height    -h    Set image height <size>
```

Change the image width and height respectively.<br>
Example: Take a capture at a different resolution

```bash
raspistill -t 2000 -o image.jpg -w 640 -h 480 
```
****
```
--quality   -q    Set jpeg quality <0 to 100>
```

Change the image quality.Quality 100 is almost completely uncompressed. 75 is a good allround value.<br>
Example: reduce the quality considerably to reduce file size

```bash
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

```bash
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

```bash
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

```bash
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

```bash
raspistill -t 30000 -tl 2000 -o image%04d.jpg
```
Example: Take timelapse picture, one every 10 seconds for 10 minutes (10
minutes = 600000ms), named image_number_1_today.jpg,
image_number_2_today.jpg onwards.
bash
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

```bash
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

```bash
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

```bash
raspivid -t 5000 -o video.h264
```
Example: Encode a 5s camera stream and send image data to stdout
```bash
raspivid -t 5000 -o -
```

Example: Encode a 5s camera stream and send image data to file
```bash
raspivid -t 5000 -o - > my_file.h264
```

****
```
--width, -w Set image width <size>
--height, -h Set image height <size>

```
Set the width and height of resulting video. Width should be between 64 and 1920. Height should be between 64 and 1080.<br>
Example:  Record a 5s clip at 720p

```bash
raspivid -t 5000 -o video.h264 -w 1280 -h 720
```

****
```
--bitrate, -b Set bitrate
```

Use bits per second, so 10MBits/s would be -b 10000000. For
H264, 1080p a high quality bitrate would be 15Mbits/s or more.<br>
Example: Record a 5s clip at a specified bitrate (3.5MBits/s)

```bash
raspivid -t 5000 -o video.h264 -b 3500000
```

****
```
--framerate, -fps Specify the frames per second to record
```

At present, the minimum frame rate allowed is 2fps, the
maximum is 30fps. This is likely to change in the future.<br>
Example:Record a 5s clip at a specified framerate (5fps)

```bash
raspivid -t 5000 -o video.h264 -f 5
```
****

## Python Module for Raspberry Pi Camera
### picamera
This package provides a pure Python interface to the Raspberry Pi camera module for Python 2.7 (or above) or Python 3.2 (or above).
### Installation
To install picamera on Raspbian, it is best to use the system’s package manager: apt. This will ensure that picamera is easy to keep up to date, and easy to remove should you wish to do so. It will also make picamera available for all users on the system. To install picamera using apt simply run:

```bash
sudo apt-get update
sudo apt-get install python-picamera python3-picamera
```
On distributions other than Raspbian, it is probably simplest to install system wide using Python’s pip tool:
```bash
sudo pip install picamera
```
### picamera API
To use picamera package in your python program import the picamera module
```python
import picamera
```
### Capturing to a file
Capturing an image to a file is as simple as specifying the name of the file as the output of whatever `capture()` method you require:
```python
from time import sleep
from picamera import PiCamera

camera = PiCamera()
camera.resolution = (1024, 768)
camera.start_preview()
# Camera warm-up time
sleep(2)
camera.capture('foo.jpg')
```

Note that files opened by picamera (as in the case above) will be flushed and closed so that when the `capture()` method returns, the data should be accessible to other processes.

### Capturing resized images
Sometimes, particularly in scripts which will perform some sort of analysis or processing on images, you may wish to capture smaller images than the current resolution of the camera. Although such resizing can be performed using libraries like PIL or OpenCV, it is considerably more efficient to have the Pi’s GPU perform the resizing when capturing the image. This can be done with the *resize* parameter of the `capture()` methods:
```python
from time import sleep
from picamera import PiCamera

camera = PiCamera()
camera.resolution = (1024, 768)
camera.start_preview()
# Camera warm-up time
sleep(2)
camera.capture('foo.jpg', resize=(320, 240))
```
The *resize* parameter can also be specified when recording video with the `start_recording()` method.

### Capturing consistent images
You may wish to capture a sequence of images all of which look the same in terms of brightness, color, and contrast (this can be useful in timelapse photography, for example). Various attributes need to be used in order to ensure consistency across multiple shots. Specifically, you need to ensure that the camera’s exposure time, white balance, and gains are all fixed:

* To fix exposure time, set the `shutter_speed` attribute to a reasonable value.
* Optionally, set `iso` to a fixed value.
* To fix exposure gains, let `analog_gain` and `digital_gain` settle on reasonable values, then set `exposure_mode` to `'off'`.
* To fix white balance, set the `awb_mode` to `'off`', then set `awb_gains` to a (red, blue) tuple of gains.
It can be difficult to know what appropriate values might be for these attributes. For `iso`, a simple rule of thumb is that 100 and 200 are reasonable values for daytime, while 400 and 800 are better for low light. To determine a reasonable value for `shutter_speed` you can query the `exposure_speed` attribute. For exposure gains, it’s usually enough to wait until `analog_gain` is greater than 1 before `exposure_mode` is set to `'off'`. Finally, to determine reasonable values for `awb_gains` simply query the property while `awb_mode` is set to something other than `'off'`. Again, this will tell you the camera’s white balance gains as determined by the auto-white-balance algorithm.

The following script provides a brief example of configuring these settings:
```python
from time import sleep
from picamera import PiCamera

camera = PiCamera(resolution=(1280, 720), framerate=30)
# Set ISO to the desired value
camera.iso = 100
# Wait for the automatic gain control to settle
sleep(2)
# Now fix the values
camera.shutter_speed = camera.exposure_speed
camera.exposure_mode = 'off'
g = camera.awb_gains
camera.awb_mode = 'off'
camera.awb_gains = g
# Finally, take several photos with the fixed settings
camera.capture_sequence(['image%02d.jpg' % i for i in range(10)])
```

### Capturing timelapse sequences
The simplest way to capture long time-lapse sequences is with the `capture_continuous()` method. With this method, the camera captures images continually until you tell it to stop. Images are automatically given unique names and you can easily control the delay between captures. The following example shows how to capture images with a 5 minute delay between each shot:
```python
from time import sleep
from picamera import PiCamera

camera = PiCamera()
camera.start_preview()
sleep(2)
for filename in camera.capture_continuous('img{counter:03d}.jpg'):
    print('Captured %s' % filename)
    sleep(300) # wait 5 minutes
```
However, you may wish to capture images at a particular time, say at the start of every hour. This simply requires a refinement of the delay in the loop (the `datetime` module is slightly easier to use for calculating dates and times; this example also demonstrates the `timestamp` template in the captured filenames):
```python
from time import sleep
from picamera import PiCamera
from datetime import datetime, timedelta

def wait():
    # Calculate the delay to the start of the next hour
    next_hour = (datetime.now() + timedelta(hour=1)).replace(
        minute=0, second=0, microsecond=0)
    delay = (next_hour - datetime.now()).seconds
    sleep(delay)

camera = PiCamera()
camera.start_preview()
wait()
for filename in camera.capture_continuous('img{timestamp:%Y-%m-%d-%H-%M}.jpg'):
    print('Captured %s' % filename)
    wait()
```
### Capturing in low light
Using similar tricks to those in Capturing consistent images, the Pi’s camera can capture images in low light conditions. The primary objective is to set a high gain, and a long exposure time to allow the camera to gather as much light as possible. However, the `shutter_speed` attribute is constrained by the camera’s `framerate` so the first thing we need to do is set a very slow framerate. The following script captures an image with a 6 second exposure time (the maximum the Pi’s V1 camera module is capable of; the V2 camera module can manage 10 second exposures):
```python
from picamera import PiCamera
from time import sleep
from fractions import Fraction

# Force sensor mode 3 (the long exposure mode), set
# the framerate to 1/6fps, the shutter speed to 6s,
# and ISO to 800 (for maximum gain)
camera = PiCamera(
    resolution=(1280, 720),
    framerate=Fraction(1, 6),
    sensor_mode=3)
camera.shutter_speed = 6000000
camera.iso = 800
# Give the camera a good long time to set gains and
# measure AWB (you may wish to use fixed AWB instead)
sleep(30)
camera.exposure_mode = 'off'
# Finally, capture an image with a 6s exposure. Due
# to mode switching on the still port, this will take
# longer than 6 seconds
camera.capture('dark.jpg')
```
In anything other than dark conditions, the image produced by this script will most likely be completely white or at least heavily over-exposed.

When using long exposures, it is often preferable to use `framerate_range` instead of `framerate`. This allows the camera to vary the framerate on the fly and use shorter framerates where possible (leading to shorter capture delays). This hasn’t been used in the script above as the shutter speed is forced to 6 seconds (the maximum possible on the V1 camera module) which would make a framerate range pointless.

### Capturing to a network stream
This is a variation of Capturing timelapse sequences. Here we have two scripts: a server (presumably on a fast machine) which listens for a connection from the Raspberry Pi, and a client which runs on the Raspberry Pi and sends a continual stream of images to the server. We’ll use a very simple protocol for communication: first the length of the image will be sent as a 32-bit integer (in Little Endian format), then this will be followed by the bytes of image data. If the length is 0, this indicates that the connection should be closed as no more images will be forthcoming. This protocol is illustrated below:

![Image Protocol](https://picamera.readthedocs.io/en/release-1.13/_images/image_protocol.svg)

Firstly the server script (which relies on PIL for reading JPEGs, but you could replace this with any other suitable graphics library, e.g. OpenCV or GraphicsMagick):
```python
import io
import socket
import struct
from PIL import Image

# Start a socket listening for connections on 0.0.0.0:8000 (0.0.0.0 means
# all interfaces)
server_socket = socket.socket()
server_socket.bind(('0.0.0.0', 8000))
server_socket.listen(0)

# Accept a single connection and make a file-like object out of it
connection = server_socket.accept()[0].makefile('rb')
try:
    while True:
        # Read the length of the image as a 32-bit unsigned int. If the
        # length is zero, quit the loop
        image_len = struct.unpack('<L', connection.read(struct.calcsize('<L')))[0]
        if not image_len:
            break
        # Construct a stream to hold the image data and read the image
        # data from the connection
        image_stream = io.BytesIO()
        image_stream.write(connection.read(image_len))
        # Rewind the stream, open it as an image with PIL and do some
        # processing on it
        image_stream.seek(0)
        image = Image.open(image_stream)
        print('Image is %dx%d' % image.size)
        image.verify()
        print('Image is verified')
finally:
    connection.close()
    server_socket.close()
```
Now for the client side of things, on the Raspberry Pi:
```python
import io
import socket
import struct
import time
import picamera

# Connect a client socket to my_server:8000 (change my_server to the
# hostname of your server)
client_socket = socket.socket()
client_socket.connect(('my_server', 8000))

# Make a file-like object out of the connection
connection = client_socket.makefile('wb')
try:
    camera = picamera.PiCamera()
    camera.resolution = (640, 480)
    # Start a preview and let the camera warm up for 2 seconds
    camera.start_preview()
    time.sleep(2)

    # Note the start time and construct a stream to hold image data
    # temporarily (we could write it directly to connection but in this
    # case we want to find out the size of each capture first to keep
    # our protocol simple)
    start = time.time()
    stream = io.BytesIO()
    for foo in camera.capture_continuous(stream, 'jpeg'):
        # Write the length of the capture to the stream and flush to
        # ensure it actually gets sent
        connection.write(struct.pack('<L', stream.tell()))
        connection.flush()
        # Rewind the stream and send the image data over the wire
        stream.seek(0)
        connection.write(stream.read())
        # If we've been capturing for more than 30 seconds, quit
        if time.time() - start > 30:
            break
        # Reset the stream for the next capture
        stream.seek(0)
        stream.truncate()
    # Write a length of zero to the stream to signal we're done
    connection.write(struct.pack('<L', 0))
finally:
    connection.close()
    client_socket.close()
```
The server script should be run first to ensure there’s a listening socket ready to accept a connection from the client script.

### Recording video to a file
Recording a video to a file is simple:
```python
import picamera

camera = picamera.PiCamera()
camera.resolution = (640, 480)
camera.start_recording('my_video.h264')
camera.wait_recording(60)
camera.stop_recording()
```
Note that we use `wait_recording()` in the example above instead of `time.sleep()` which we’ve been using in the image capture recipes above. The `wait_recording()` method is similar in that it will pause for the number of seconds specified, but unlike `time.sleep()` it will continually check for recording errors (e.g. an out of disk space condition) while it is waiting. If we had used `time.sleep()` instead, such errors would only be raised by the `stop_recording()` call (which could be long after the error actually occurred).

### Recording over multiple files
If you wish split your recording over multiple files, you can use the `split_recording()` method to accomplish this:
```python
import picamera

camera = picamera.PiCamera(resolution=(640, 480))
camera.start_recording('1.h264')
camera.wait_recording(5)
for i in range(2, 11):
    camera.split_recording('%d.h264' % i)
    camera.wait_recording(5)
camera.stop_recording()
```
This should produce 10 video files named `1.h264`, `2.h264`, etc. each of which is approximately 5 seconds long (approximately because the `split_recording()` method will only split files at a key-frame).

The `record_sequence()` method can also be used to achieve this with slightly cleaner code:
```python
import picamera

camera = picamera.PiCamera(resolution=(640, 480))
for filename in camera.record_sequence(
        '%d.h264' % i for i in range(1, 11)):
    camera.wait_recording(5)
```
Changed in version 1.3: The `record_sequence()` method was introduced in version 1.3

### Controlling the LED
In certain circumstances, you may find the V1 camera module’s red LED a hindrance (the V2 camera module lacks an LED). For example, in the case of automated close-up wild-life photography, the LED may scare off animals. It can also cause unwanted reflected red glare with close-up subjects.

One trivial way to deal with this is simply to place some opaque covering on the LED (e.g. blue-tack or electricians tape). Another method is to use the `disable_camera_led` option in the boot configuration.

However, provided you have the RPi.GPIO package installed, and provided your Python process is running with sufficient privileges (typically this means running as root with `sudo python`), you can also control the LED via the `led` attribute:
```python
import picamera

camera = picamera.PiCamera()
# Turn the camera's LED off
camera.led = False
# Take a picture while the LED remains off
camera.capture('foo.jpg')
```
> **Note**<br>
The camera LED cannot currently be controlled when the module is attached to a Raspberry Pi 3 Model B as the GPIO that controls the LED has moved to a GPIO expander not directly accessible to the ARM processor.

> **Warning**<br>
Be aware when you first use the LED property it will set the GPIO library to Broadcom (BCM) mode with GPIO.setmode(GPIO.BCM) and disable warnings with GPIO.setwarnings(False). The LED cannot be controlled when the library is in BOARD mode.

## Citations
* [https://picamera.readthedocs.io/en/release-1.13](https://picamera.readthedocs.io/en/release-1.13/)
* [https://www.raspberrypi.org/documentation](https://www.raspberrypi.org/documentation/)
* [https://projects.raspberrypi.org/en/projects/getting-started-with-picamera](https://projects.raspberrypi.org/en/projects/getting-started-with-picamera)
