Grbl has the neat ‘$’-command to tweak the settings at runtime. Connect to Grbl using the serial terminal of your choice (baud rate 9600 unless you changed that in config.h) as 8-N-1 (8-bits, no parity, and 1-stop bit.) Once connected you should get the Grbl-prompt, which looks like this:

```
Grbl v0.7
'$' to dump current settings
```

Type $ and press enter. You should not see any local echo of the $ and enter, but Grbl should respond with:

```
$0 = 400.0 (steps/mm x) 
$1 = 400.0 (steps/mm y) 
$2 = 400.0 (steps/mm z) 
$3 = 30 (microseconds step pulse) 
$4 = 480.0 (mm/sec default feed rate) 
$5 = 480.0 (mm/sec default seek rate) 
$6 = 0.100 (mm/arc segment) 
$7 = 0 (step port invert mask. binary = 0) 
$8 = 25 (acceleration in mm/sec^2) 
$9 = 0.05 (cornering junction deviation in mm)
'$x=value' to set parameter or just '$' to dump current settings
```

To change e.g. the microseconds step pulse option to 50us you would type this, followed by an enter:
```
$3=50
```
If everything went well, Grbl will respond with an 'ok' and this setting is stored in eeprom and will be retained forever or until you change them. You can check if Grbl has received and stored your setting correctly by typing $ to view the system settings again.

### $0, $1 and $2 – Steps/mm

Grbl needs to know how far each step will take the tool in reality. To calculate steps/mm for an axis of your machine you need to know:

* The mm per revolution of the lead screw
* The full steps per revolution of your steppers (typically 200)
* The microsteps per step of your controller (typically 1, 2, 4, 8, or 16). _Tip: Using high microstep values (e.g 16) can reduce your stepper motor torque, so use the lowest that gives you the desired axes resolution and comfortable running properties._

The steps/mm can then be calculated like this: ```steps_per_mm = (steps_per_revolution*microsteps)/mm_per_rev```

Compute this value for every axis and write these settings to Grbl.

### $3 – Microseconds/step pulse

Stepper drivers are rated for a certain minimum step pulse length. Check the data sheet or just try some numbers. You want as short pulses as the stepper drivers can reliably recognize. If the pulses are too long you might run into trouble running the system at high feed rates. Generally something between 20 and 50 microseconds works.

### $4 and $5 – Default seek and feed rates

This setting sets the default seek(G0) and feed rates(G1,G2,G3) after Grbl powers on and initializes. The seek rate (aka rapids) is used for moving from point A to point B as quickly as possible, usually for traversing into position or homing. The seek rate should be set at the maximum speed your machine can go in any axes movement. The default feed rate usually does not enter into the picture as feed rates will generally be specified in the g-code program, but if not, this default feed rate will be used.

### $6 – Mm/arc segment

Grbl renders circles and arcs by subdividing them into teeny tiny lines. You will probably never need to adjust this value – but if you find that your circles are too crude (really? one tenth of a millimeter is not precise enough for you? Are you in nanotech?) you may adjust this. Lower values gives higher precision but may lead to performance issues.

### $7 – invert mask

Some cnc-stepper controllers needs its high-low inputs inverted for both direction and steps. Signal lines are normally held high or low to signal direction or held high and goes low for a couple of microseconds to signal a step event. To achieve this, Grbl can invert the output bits to accomodate particular needs. The invert mask value is a byte that is xored with the step and direction data before it is sent down the stepping port. That way you can use this both to invert step pulses or to invert one or more of the directions of the axes. The bits in this byte corresponds to the pins assigned to stepping in config.h. Note that bits 0 and 1 are not used for inversion. Per default bits are assigned like this:

```
#define X_STEP_BIT 2
#define Y_STEP_BIT 3
#define Z_STEP_BIT 4
#define X_DIRECTION_BIT 5
#define Y_DIRECTION_BIT 6
#define Z_DIRECTION_BIT 7
```

If you wanted to invert the X and Y direction in this setup you would calculate a value by bitshifting like this (in your favorite calculating environment):

``> (1<<X_DIRECTION_BIT)|(1<<Y_DIRECTION_BIT)``

Which is equal to 96, so issuing this command would invert them:

``$7=96``

Now when you view the current settings, you should now see this in your invert mask line with the binary representation of the number (bits 5 and 6 should now show a 1 to indicate inversion.)

``$7 = 96 (step port invert mask. binary = 1100000)``

### $8 – Acceleration

This is the acceleration in mm/second/second. You don’t have to understand what that means, suffice it to say that a lower value gives smooooother acceleration while a higher value yields tighter moves and reach the desired feedrates much quicker. In technical terms, this is the point to point acceleration of your machine, independent of axes. Set this acceleration value as high as your most limiting axes can let you without losing *ANY* steps. Usually you'd like to give yourself a little buffer, because if you lose steps, Grbl has no idea this has happened (steppers are open-loop control) and will keep going.

### $9 –  Cornering junction deviation in mm

Cornering junction deviation is used by the acceleration manager to determine how fast it can move through a path. The math is a bit complicated but in general, higher values gives generally faster, possibly jerkier motion. Lower values makes the acceleration manager more careful and will lead to careful and slower cornering. So if you run into problems where your machine tries to take a corner too fast, *decrease* this value to make it slow down. If you want your machine to move faster through junctions, *increase* this value to speed it up. For technical people, hit this [link](http://t.co/KQ5BvueY) to read about Grbl's cornering algorithm, which accounts for both velocity and junction angle with a very simple, efficient, and robust method.

# Other Resources:
* **[DANK](http://dank.bengler.no/-/page/show/5474_configuringgrbl)** _Last updated 2/2011._ Only applies to grbl v0.6.