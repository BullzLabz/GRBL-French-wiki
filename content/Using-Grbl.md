_This wiki page is intended to provide various instructions on how to use Grbl. Please feel free to contribute and help keep this page up-to-date!_

## Getting Started (For New Users.)
After flashing Grbl to your Arduino, connecting to Grbl is pretty simple. You can use the Arduino IDE itself to connect to Grbl. Experiment or play with it, just to see if you like it. Other serial port programs, such as CoolTerm or PuTTY, work great too. The instructions are pretty much the same. 

* Open up the Arduino IDE and make sure your Arduino with Grbl is connected to your USB port.
* Select the Arduino's Serial Port in the Tools menu, as you would normally with an Arduino.
* Open up the 'Serial Window' in the Tools menu.
 * If you are using Grbl v0.9, make sure to change the baud rate from 9600 to 115200.
* Once open, you should see a Grbl welcome message like ```Grbl v0.Xx ['$' for help]```. This means all is good! You're connected!
 * Make sure you change the "No line ending" drop-down menu to "Carriage return". If you are using any other serial port program, you must do the same.
 * If you haven't received the welcome message or some garbled characters, make sure that the baud rate is set at 9600 (or 115200 for v0.9+).

From here, you can simply start sending Grbl some G-code commands, and it'll perform them for you. Or, you can type ```$``` to get some help on what some of Grbl's special commands are or how to write some of your machine settings into Grbl's EEPROM memory.

When have started to feel comfortable with G-code/CNC and you're ready to run a whole G-code program, we recommend that you use one of the many great GUIs that users have written to stream your G-code programs to Grbl and to fully harness all of Grbl's capabilities.

**NOTE:** Check out ShapeOko's [Wiki](http://www.shapeoko.com/wiki/index.php/Communication_/_Control). It has the most up-to-date and comprehensive list of Grbl GUIs.

## How to Stream G-Code Programs to Grbl

### [bCNC] (https://github.com/vlachoudis/bCNC)
![bCNC Screenshot](https://raw.githubusercontent.com/vlachoudis/bCNC/doc/Screenshots/bCNC.png)

An advanced fully featured g-code sender for GRBL. bCNC is a cross platform program (Windows, Linux, Mac) written in python with minimal external dependencies. The sender is robust and fast able to work nicely with old or slow hardware like [Rasperry PI](http://www.openbuilds.com/threads/bcnc-and-the-raspberry-pi.3038/) (As it was validated by the GRBL mainter on heavy testing).

**Features:**
- simple and intuitive interface for small screens
- importing g-code and **dxf** files
- fast g-code sender (works nicely on RPi and old hardware)
- workspace configuration (dialog for G54..G59 commands)
- user configurable buttons
- g-code **function evaluation** with run time expansion
- Easy probing:
  - simple probing
  - center finder with a probing ring
  - **auto leveling**, Z-probing and auto leveling by altering the g-code during
    sending.
  - height color map display
  - **manual tool change** expansion and automatic tool length probing
- Various Tools:
  - user configurable database of materials, endmills, stock
  - properties database of materials, stock, end mills etc..
  - basic **CAM** features (profiling, pocketing, drilling)
  - User g-code plugins:
    - bowl generator
    - finger joint box generator
    - simple spur gear generator
    - spirograph generator
    - surface flatten
    - ...
- G-Code editor and display
    - graphical display of the g-code, and workspace
    - graphically moving and editing g-code
    - reordering code and **rapid motion optimization**
    - moving, rotating, mirroring the g-code
- web pendant to be used via smart phones

### [Universal G-code Sender (UGS)](https://github.com/winder/Universal-G-Code-Sender) [Java Cross-Platform]
![UGS main window](https://raw.github.com/winder/Universal-G-Code-Sender/master/pictures/1.0.6_command_table.png)
![UGS visualizer](https://github.com/winder/Universal-G-Code-Sender/raw/master/pictures/1.0.6_visualizer.png)

A full-featured GUI, developed by @wwinder, that streams, visualizes G-code, and has complete control and feedback functionality for Grbl's higher level features. It's written in Java, which means it can be run on any Java-capable machine including the RaspberryPi! The Grbl group works closely with this project and highly recommend using this GUI. If you find any issues or would like to request more GUI features, @wwinder has done a wonderful job in completing many of the requests.

--------
### [Easel](http://app.easel.com) [Browser-based CAD + CAM + Grbl controller]

![Easel Screenshot](http://cl.ly/image/0m3Q0w0u0c1F/Screen%20Shot%202014-10-07%20at%205.22.00%20PM.png)

Easel is a web based project developed by Inventables specifically for us with the Shapeoko + Grbl. It is an all-in-one package for design (including SVG imports), toolpath generation, and machine control. In addition to 2D design tools, Easel lets you preview your toolpaths in 3D before sending them to your machine. Easel is in constant development by the Inventables team. You can request features or report issues through the feedback button in the app. 

----------

### [GRBLweb](http://andrewhodel.github.io/grblweb/) [Web Browser]

![GRBLweb ui](http://andrewhodel.github.io/grblweb/grblweb_ui_small.png)

GRBLweb is a web based GCODE sender and controller for GRBL. Multiple serial devices can be connected to control multiple machines.

There is also a pre-built Raspberry Pi image based on Raspbian running GRBLweb available [here](http://andrewhodel.github.io/grblweb/).

----------

### GrblPanel [Windows]
![GrblPanel](https://cloud.githubusercontent.com/assets/8202594/4531716/a1f748c2-4d8c-11e4-8cde-bf5f4d1d06a3.JPG)

Maintained by retired computer professional @gerritv, **GrblPanel** is a GUI that implements more advanced features and functionality commonly found in production machines like Haas, Fanuc, etc. All of the required tools for setting up and running a milling job are neatly arranged and designed to be easily accessible based on decades-old accepted workflows in machine shops. **GrblPanel** currently only works in Windows via .Net v4.5, but will eventually be updated for cross-platform use through [Mono](http://www.mono-project.com).


-----------

### [grblControl](https://github.com/Denvi/grblControl) [Windows/Linux]
![screenshot_heightmap_original](https://cloud.githubusercontent.com/assets/5119037/9604203/7e979a52-50cf-11e5-8b10-23e54977a782.png)

GUI application for GRBL-based CNC-machines with G-Code visualizer.

Supported functions:
* Controlling GRBL-based cnc-machine via console commands, buttons on form, numpad.
* Monitoring cnc-machine state.
* Loading, editing, saving and sending of G-code files to cnc-machine.
* Visualizing G-code files.
* Autoleveling Z-axis for PCB milling.


-----------

### Python Streaming Scripts _(Officially Supported by Grbl)_ [Cross-Platform]

__NOTE: If you are having difficulties with streaming to Grbl, we will ask you to use this Python streaming script to eliminate the GUI you are using as the source of the issue. Before posting to the issues thread, please use this script to run your G-code program.__

Included with the source code and officially supported by Grbl, two Python streaming scripts are supplied to illustrate simple and more complex streaming methods that work well cross-platform. These scripts don't fully support all of the Grbl's features, but are intended more as a way to compare or troubleshoot other garden variety or newly-written GUIs out there. These are located in the 'script' folder on the main repository. **Note:** The streaming scripts require the [pySerial](http://pyserial.sourceforge.net) module installed.

* Install the [pySerial](http://pyserial.sourceforge.net) module.
* Download [simple_stream.py](https://github.com/grbl/grbl/blob/master/script/simple_stream.py) Python script.
* Open the script in a plain text editor and change the following line to reflect your system:  

`s = serial.Serial('/dev/tty.usbmodem1811',9600)`  

* In place of **/dev/tty.usbmodem1811**(Mac), you should put the serial port device name of your Arduino. This will be different for each machine and OS. For example, on a Linux system this would look like **/dev/ttyACM0**. Or on a Windows machine, this may look like **COM3**. 
* The script looks for and reads gcode from a file named **grbl.gcode**, you should create this file and put the gcode you want to execute in it. Or simply change this name in the script to your needs.
* Open a terminal/command window and change directories to the location of the Python script and execute the Python script with the following command:  

`./simple_stream.py`  (Mac/Linux)
`python simple_stream.py` (Windows)

* You should now see the gcode being streamed to grbl along with 'ok' messages and your machine should begin moving.

The other, more advanced streaming script **stream.py** has command line arguments and does not require modifying the script itself, unlike **simple_stream.py**. The main difference is that **stream.py** uses a character counting scheme to ensure that Grbl's serial read buffer is full, which effectively creates another buffer layer on top of Grbl's internal motion queue. This allows for Grbl to access and parse the next G-code block immediately from the serial read buffer, rather than wait for the 'ok' send and response in the **simple_stream.py** script. This is very useful for motions, like curves, that have very rapid, short line segments in succession that may cause buffer starvation, which can lead to strange motion hiccups. In other words, it ensures a smoother motion. Use this script, if you are not afraid of command line or are experiencing weird motions.

-------

### Other GUIs

#### grblUI
A simple graphical user interface: https://github.com/jgeisler0303/grblUI. Programmed in Java, using rxtx for serial communication. Should theoretically run on Linux, Mac and Windows alike. Apparently some problems on Mac. Any feedback, tips and tricks appreciated (Issues or Wiki in grblUI). Check out the ready to use jar in the Downloads.

#### grblgui
A graphical G-Code Streamer: https://github.com/cody82/grblgui. Programmed in Java, using rxtx for serial communication and OpenGL 2.0 for rendering.

Notable features:
* It displays the job duration and remaining time to complete in minutes.
* It displays current speed.
* You can toggle feed hold and enter G-Code commands.
* It displays the buffer status graphically on the toolpath!

In development:
* Simulate the milling process and display the resulting model.


#### CNCinfusion [Windows]
Currently under development in C#   https://github.com/nm156/CNCInfusion

#### Gcode Sender [Windows] 
https://github.com/downloads/OttoHermansson/GcodeSender/gcodesender.exe
http://www.contraptor.org/forum/t-287260/gcode-sender-program

-------

## Serial Emulators:

Other than CoolTerm or PuTTY, Linux and Mac systems have a great lightweight serial emulator called `screen` that's either built in or easily installable (apt-get install screen) through the terminal interface.

If your device is connected on /dev/ttyACM0 (for Mac, /dev/tty.usbxxxx), type `screen /dev/ttyACM0 115200` to connect to the device at 115200 baud. There you'll be connected to Grbl. To exit

To get out of the screen interface, simply press `Ctrl-a` followed by a `k`. 