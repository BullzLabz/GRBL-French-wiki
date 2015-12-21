_Please feel free to contribute more up-to-date answers or questions!_

#### How do you pronounce Grbl!?

This is probably the number one question, and the answer is: "It depends on who you ask." 

When Simen Svale Skogsrud first sat down and wrote Grbl in 2009, he named it after a bigger version of a computer mouse. It's small, useful, and doesn't do much other than what its designed to do. So, if you ask him, it's pronounced like "_gerbil_". If you ask Sonny Jeon, the developer since 2011, he'd say it's pronounced '_grr-ble_', because he was teased too many times by his wife when working on the project. If you ask Edward Ford of Shapeoko, he'd probably say '_garble_', even though he knows better. In any case, pronounce it however you'd like. We love hearing the various interpretations of our name!

## Compiling Grbl

#### How do I compile Grbl and upload it?
See our [Compiling Grbl](https://github.com/grbl/grbl/wiki/Compiling-Grbl) for a list of recommended ways to do this!

#### Where can I download a pre-compiled Grbl HEX file to flash onto my Arduino?
Links to pre-compiled Grbl HEX files are located down near the bottom of the main front page in the README area.

#### The HEX file is larger than 32K that the Arduino will hold. Will it still upload?
Yes, it should. The compiled HEX file is in hexidecimal format, not binary. So it can be somewhat larger than the 32K flash limit on Arduinos. No more than about double the 32K, though.

## Flashing Grbl

#### How do I flash Grbl to my Arduino?
Please check the Wiki help pages for details. If they are not up-to-date, please notify us or update them if you found they were in error or have another method. Thanks!

#### Does grbl overwrite the Arduino bootloader?  
No, grbl fits on the ATmega328P without having to overwrite the bootloader; you will still be able to upload Arduino sketches after flashing without having to re-burn the bootloader. NOTE: Grbl v1.0 may be too large for Arduino Duemilanove-compatible boards, as their boot loader takes up 1.5KB, rather than 0.5KB on the Uno. This means that there is 1KB less available flash.

#### I've got a Duemilanove-compatible board and I can't seem to fit Grbl onto it! What do I do?
Experiments in re-flashing the Arduino Uno's 0.5KB 'Optiboot' boot loader and replacing the Duemilanove/Nano/Micro/etc 1.5KB boatloader have shown it's possible. Simply connect an ISCP programmer or use another Arduino as a programmer. (Search web on how to do this. It's very easy!) Make sure to select `Arduino Uno` as the board type, as you'll be forcing the IDE to burn the Uno's boot loader to the connected board. Select the appropriate programmer type and click `Burn Bootloader`. If everything went right, 'Optiboot' will now be on your Duemilanove-compatible Arduino and you'll have an extra 1KB of flash to use! Make sure you choose `Arduino Uno` from then on out with this updated Arduino.

## Connecting Grbl

#### My CNC moves erratically when I boot up my Arduino! Why does it do this?
The Arduino bootloader takes a second or two to boot up before Grbl initializes. During this time, the stepper enable pin is LOW, which is enabled, before Grbl finishes its initialization and sets the pin to HIGH to disable the steppers. This brief moment makes your stepper drivers susceptible to electronic noise, so if your driver step pins have enough noise to falsely indicate a step, your steppers may start moving erratically. 

We are looking into this, but this may be an unavoidable problem with straight-up Arduinos. There are some solutions however. You can try to locate the source of the electronic noise and remove it (a fan too close the other electronics). You can place a pull-up resistor on the stepper enable line to disable the steppers during the boot-up process. You can also remove the Arduino bootloader altogether and install Grbl through the ICSP header, which requires specific hardware to do it.

When the Arduino board is USB powered and the stepper drivers have their own logic voltage supply, don't forget to connect the ground of both circuits.

## Configuring Grbl

#### Writing individual settings is tedious. Is there a way to speed this up?
Yes, there is a very simple way to write all of your settings at once. Just copy and paste the current settings to a text file, meaning the whole print-out of the '$$' command with labels and all. Grbl will ignore those labels because they are inside '()' comments. Change the values after the '=' characters to whatever you need. Save the file and stream it to grbl using the 'simple_stream.py' streaming script in the '/script' folder of our repo. (Or, you can use the other streaming script `stream.py` with the `-s` settings-mode flag.) Once it's streamed, all your settings are updated!

#### My Grbl settings and parameters are all funky after flashing Grbl! How do I clear my EEPROM to start from a clean slate?!
After flashing, Grbl tries to check the settings in EEPROM. If it finds an incompatibility, it will automatically clear the EEPROM and restore its default. We try to cover all scenarios within the limited flash space we can devote to checking it, but sometimes we miss something and the data in the EEPROM may be incompatible with the version you have. Or, something went wrong with flashing process and corrupted the EEPROM, which does happen every once in a while. This results in the data having weird numbers and values. In Grbl v0.9j and later, the easiest way to clear out the EEPROM is to use the `$RST=*` command. This will restore the EEPROM space that Grbl uses to their defaults. For earlier versions of Grbl or to do a complete EEPROM wipe, an Arduino IDE example can do this for you, found in `File->Examples->EEPROM->eeprom_clear`. Change the 512 bytes to 1024 bytes in the for loop and upload it. This should wipe your EEPROM. When you re-flash Grbl, you'll start out from a clean slate. 

#### Homing cycle isn't working right! The movements are all going in wrong directions!
Grbl's homing cycle assumes you have set up axes directions correctly. So on a standard mill, this means the positive directions for each axis is: Z-axis spindle moves up, Y-axis table moves toward you (or carriage moves away), and X-axis table moves to the left. Once you have this setup, the homing cycle defaults to searching for the limit switch all in the positive direction, starting with the z-axis and followed by the x-axis and y-axis together. If you happen to place one of your limit switches on the other end of travel on one of your axes, then you can use the homing direction mask to have it search in the negative direction. LinuxCNC.org has a great diagram on their website describing the proper coordinate system setup.

#### After homing, why are some of my machine positions negative and not zero!?!
After a homing cycle, Grbl sets the machine coordinate frame in all negative space. This is common on professional CNC machines that is mostly due to tradition and being able to identify what you are looking at when both machine and work positions are displayed. If your limit switch is not on the positive end of the axes (and you've inverted the homing direction), Grbl will set the machine position to your `-max_travel` setting. If you find this so annoying that you can't stand it, you can always have Grbl set the machine position to zero, regardless of where your limit switches are. You will need to uncomment the `#define HOMING_FORCE_SET_ORIGIN` in config.h, re-compile, and re-flash. That's it! 

#### When setting homing pull-off to something greater than zero, why is it crashing INTO my limit switch?! 
The homing pull-off issue is almost always caused by a user wiring in normally-closed limit switches, rather than normally-open switches. Or, the user has inverted their limit switch setting incorrectly for their type of switch. The reason Grbl defaults to requiring normally-open limit switches is purely for the fact of maintaining historical continuity. Either normally-open or normally-closed switch work with Grbl, if they are connected to ground and the limit pins. Arduinos have a nice little internal pull-up resistor already there, so typically you won't need to add another one. Although it's recommended that you add a small capacitor in parallel between the pin and ground to create a simple low-pass filter circuit to help eliminate transient electrical noise.

## Using Grbl

#### How do I connect and start using Grbl?
Grbl communicates through the serial port, just as in the Arduino IDE. You may connect to it via any standard serial terminal program (i.e Coolterm) at 115200 baud (9600 for v0.8 and prior). Once you are connected, you should be presented with a short message indicating the Grbl version and settings how-to. Just type valid G-code commands followed by an enter and Grbl should respond with an **ok** or an **error:** message. Note: You won't see any character echoes as you type commands to Grbl.

#### How do I stream a complete G-code program to Grbl?
Streaming g-code programs to Grbl may be done by a simple send-and-respond method through the serial port. Every command followed by a return is responded to when Grbl is ready to receive another command. See the **Using Grbl** wiki page for more details, as there are multiple streaming scripts and GUIs available to do this for you.

#### Why can't I just upload a file to Grbl? Or I swear XON/XOFF flow control used to work!
You would think that just uploading a file to Grbl would work, but it won't. This functionality requires some kind of serial flow control to indicate to the computer when the receiver's (Grbl) serial read buffer is full and when it's ready to get more data. The Arduino's hardware flow control lines are hardwired to reset and re-flash the Arduino, not for flow control. XON/XOFF software flow control is not officially supported by Arduinos, but they **used** to work on older Arduinos. This was due to a recent switch in the Arduino's USB-to-serial emulator chips, from FTDI to Atmega. The idea was to allow for people to flash their own firmware onto these emulator chips for their own nefarious needs, where FTDI chips were a closed-platform. This switch inadvertantly removed the FTDI's XON/XOFF software flow control support. As far as we know, there is no push to bring XON/XOFF flow control back. Although, since this firmware is now open-source, it may show up if someone does it or badgers the right people.

#### My stepper drivers require a time delay between the direction pin and step pin settings! (Or I'm noticing that my steppers drift after many many direction changes.) How do I configure this/fix this problem?
This problem comes from Grbl's main stepper algorithm. It sets the direction pin immediately before the stepper pins, which may not occur with enough time in between for certain stepper drivers to acknowledge a change in direction. This can cause a slight stepper drift after many direction changes.
The Grbl master branch has an experimental compile-time option that creates a user-specified time delay after the direction pin is set and before the step pulse is initiated. This is done by enabling another interrupt (Timer2 compare). However, since it does add another interrupt, there is a chance that this can adversely effect Grbl's high-end performance (i.e. high step frequencies or complex curves), but this has not been thoroughly tested to verify this. If everything proves to be solid, we will consider adding this feature in later releases. So, please report any successes or problems with this feature!
There is also a hack/work-around without needing to re-compile. The Grbl invert mask setting not can not only invert your direction pins, but also your stepper pins as well. So instead of being normal low, they can be normal high. Since most stepper drivers acknowledge a step by sensing only a rising(or falling) edge and the other is ignored, you can create a virtual direction pin time delay. Note that now your Grbl pulse microseconds settings will now define this time delay and you will no longer have any control over your step pulse time length (but this shouldn't matter since your stepper drivers shouldn't care.) Although there have been reports that certain stepper drivers don't like to be held normal high for prolonged periods, but it doesn't hurt to try. :)

#### Grbl is acting very weird. Dropping connections, freezing, dropping lines. What's up?
There can be a lot of reasons for this, but the most common is **electromagnetic interference**. If you have AC power lines or your stepper motor wiring near the logic wires or USB cord, the electro-magnetics from these 'high' power lines can cause the logic lines to spike occasionally. This leads to weird connection problems, freezing, etc., etc. Make sure you route these clear of each other and if you can't, try routing them at a 90 degree angle to each other (works with Ethernet cables, should work here as well). Another solution can be placing a small capacitor in parallel with your logic lines creating a high frequency low-pass filter. This will even out some of those spikes. Also, check for grounding. If your setup is poorly grounded, ground loops can cause the logic input voltages to be unstable. Make sure you star-ground wherever you can, especially your motor drivers.

In the past, users reporting this problem have typically noted it haa been caused by too many devices on a shared AC circuit, poor grounding, a vacuum motor too close, or a logic line too close to a cooling fan.

Also, if you are dropping lines when streaming, this could be caused by a problem with USB-to-serial drivers on particular computers or environments (Java). It's been reported that the Java-based Universal GcodeSender can drop lines (although reported to have been fixed recently), where the Grbl-supported Python streaming script does not. Please try using the supported streaming script as comparison if you are experiencing this problem.

## G-codes

#### Where are G-codes defined? What does each of them do? 
Grbl tries to follow the NIST NGC/RS274 v3 standard for numerical control, aka G-code. EMC2 and Mach3 follows this standard very closely as well. Click the [link](http://citeseerx.ist.psu.edu/viewdoc/summary?doi=10.1.1.15.7813) to download and read the G-code standard document. Some of the NIST standard G-codes are a bit out-dated, so we also use some of the LinuxCNC.org G-code definitions in these cases. LinuxCNC.org also provides great documentation on their G-codes. [G-codes](http://www.linuxcnc.org/docs/2.5/html/gcode/gcode.html) [M-codes](http://www.linuxcnc.org/docs/2.5/html/gcode/m-code.html) [Other codes] (http://www.linuxcnc.org/docs/2.5/html/gcode/other-code.html)

#### Why are some of Grbl's G-codes a little different as on some other CNC machines?
While we follow the NIST NGC/RS274 v3 and LinuxCNC standards, this is actually not completely standardized between all existing CNC machines and manufacturers. Numerical control is pretty old, arcane, and predates MS-DOS. In other words, it's a mess. There have been pushes to standardized it, like by NIST, and it has been successful for the most of common G-code commands. Yet, there are a few G-codes that are not standardized, such as the G-codes G28/G30, G92.X, etc. It's a goal of Grbl to follow a published standard so that people may build off of it, so as not to muddy the G-code waters even more.

#### Then why do you use some of LinuxCNC's G-codes rather than only the NIST standard? Are you muddying the G-code waters? 
Yes, in a way. The NIST standard only cover the basic functions and some of those are out-dated. There are some really useful G-code functions that other CNC machines and manufacturers use that aren't covered by NIST. We picked one, LinuxCNC, which is user supported, open-source and familiar to hobbyists already. So we think that just as long as we stick to one, everyone's happy.

#### Why would you separate a circle into multiple arcs? Not just draw a full circle?
This comes from a problem of how the arc are defined in G-codes. In radius mode **R**, solving the path for a complete circle or semi-circle will cause severe numerical round-off problems that are unavoidable. This can lead to an error in the tool path. In fact, NIST guidelines state only use **R** mode for arc angles from 0- 165 and 195-345 degrees. Some CNC manufacturers actually don't allow users to draw a complete circle to avoid this problem altogether, limiting users to either a maximum 90 or 180 degree arc motions only. It is good practice to separate all of your arc motions into 90 or 180 degree motions. However, incremental arc mode **I,J** does not have this problem, but it's still good practice to separate your arcs.

#### Grbl seems to be acting weird when streaming when there are G10, G28.1, or G30.1 commands in the program. What's going on?
This has to do with EEPROM writing and how it automatically shuts down all interrupt processes while it's writing, including the serial interrupts. These pauses can happen up to 20 milliseconds, which means that a serial character can be lost within that time period. The G10, G28.1, and G30.1 commands all write to the EEPROM the coordinate offset parameters so that they are persistent between sessions. In practice, you almost never need to update these **in** a G-code program, since they are performed during machine setup and typically hand-coded. If you do need to stream these commands in a program for some reason, there unfortunately isn't really a way to get around this problem. If you come upon a good solution, please let us know.

#### With this EEPROM read/write issue mentioned above, what data is kept in EEPROM? 
Grbl stores only a few things in EEPROM. The '$$' main settings array, G54-G59 work coordinate offsets, G28/G30 pre-defined positions, $N startup-line strings, and the $I build info user string.