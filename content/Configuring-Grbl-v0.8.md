Grbl has a system ‘$’-command to tweak the settings and trigger runtime commands. Connect to Grbl using the serial terminal of your choice (baud rate 9600 unless you changed that in config.h) as 8-N-1 (8-bits, no parity, and 1-stop bit.) Once connected you should get the Grbl-prompt, which looks like this:
```
Grbl 0.8c ['$' for help]
```
Type $ and press enter. You should not see any local echo of the $ and enter, but Grbl should respond with a help message:
```
$$ (view Grbl settings)
$# (view # parameters)
$G (view parser state)
$N (view startup blocks)
$x=value (save Grbl setting)
$Nx=line (save startup block)
$C (check gcode mode)
$X (kill alarm lock)
$H (run homing cycle)
~ (cycle start)
! (feed hold)
? (current status)
ctrl-x (reset Grbl)
```
Of these commands, only four of them are used to configure Grbl `[$$, $x=value, $N, $Nx=line]`, while the rest are runtime commands that either report the current state in Grbl or tweak the behavior.

#The four configuration commands: `$$, $x=val, $N, $Nx=line`

### $$ - View Grbl settings, $x=val - Save Grbl setting
When connected to Grbl, type `$$` and press enter. Grbl should respond with a list of the current system settings, similar to as shown in the following. All of these settings are persistent and kept in EEPROM, so if you power down, these will be loaded back up the next time your power up your Arduino.

```
$0=755.906 (x, step/mm)
$1=755.906 (y, step/mm)
$2=755.906 (z, step/mm)
$3=30 (step pulse, usec)
$4=500.000 (default feed, mm/min)
$5=500.000 (default seek, mm/min)
$6=28 (step port invert mask, int:00011100)
$7=25 (step idle delay, msec)
$8=50.000 (acceleration, mm/sec^2)
$9=0.050 (junction deviation, mm)
$10=0.100 (arc, mm/segment)
$11=25 (n-arc correction, int)
$12=3 (n-decimals, int)
$13=0 (report inches, bool)
$14=1 (auto start, bool)
$15=0 (invert step enable, bool)
$16=0 (hard limits, bool)
$17=0 (homing cycle, bool)
$18=0 (homing dir invert mask, int:00000000)
$19=25.000 (homing feed, mm/min)
$20=250.000 (homing seek, mm/min)
$21=100 (homing debounce, msec)
$22=1.000 (homing pull-off, mm)
```

To change e.g. the microseconds step pulse option to 10us you would type this, followed by an enter:
```
$3=10
```
If everything went well, Grbl will respond with an 'ok' and this setting is stored in EEPROM and will be retained forever or until you change them. You can check if Grbl has received and stored your setting correctly by typing `$$` to view the system settings again.

### $N - View startup blocks, $Nx=line - Save startup block

`$Nx` are the startup blocks that Grbl runs everytime you power on Grbl or reset Grbl. In other words, a startup block is a line of g-code that you can have Grbl auto-magically run to set your g-code defaults, or anything else you need Grbl to do everytime you start up your machine. At the moment, Grbl will store two blocks of g-code as a system default, but it can store anywhere from 1 to 5, as a compile-time option (changing config.h and re-compiling). 

So, when connected to Grbl, type `$N` and then enter. Grbl should respond with something short like:
```
$N0=
$N1=
ok
```
Not much to go on, but this just means that there is no g-code block stored in line `$N0` for Grbl to run upon startup. `$N1` is the next line to be run. (If you re-compile to have more, it will execute in order on to `$N4`.)

To set a startup block, type `$N0=` followed by a valid g-code block and an enter. Grbl will run the block to check if it's valid and then reply with an `ok` or an `error:` to tell you if it's successful or something went wrong. 

For example, say that you want to use your first startup block `$N0` to set your g-code parser modes like G54 work coordinate, G20 inches mode, G17 XY-plane. You would type `$N0=G20 G54 G17` with an enter and you should see an 'ok' response. You can then check if it got stored by typing `$N` and you should now see a response like `$N0=G20G54G17`.

Once you have a startup block stored in Grbl's EEPROM, everytime you startup or reset you will see your startup block printed back to you and a response from Grbl to indicate if it ran ok. So for the previous example, you'll see:
```
Grbl 0.8c ['$' for help]
G20G54G17ok
```
If you have multiple g-code startup blocks, they will print back to you in order upon every startup. And if you'd like to clear one of the startup blocks, type `$N0=` without anything following the equal sign.

Also, if you have homing enabled, the startup blocks will execute immediately after the homing cycle, not at startup. 

IMPORTANT: Be very careful when storing any motion (G0/1,G2/3,G28/30) commands in the startup blocks. These motion commands will run everytime you reset or power up Grbl, so if you have an emergency situation and have to e-stop and reset, a startup block move can and will likely make things worse quickly.

# Grbl's `$x=val` settings and what they mean

### $0, $1 and $2 – XYZ, steps/mm

Grbl needs to know how far each step will take the tool in reality. To calculate steps/mm for an axis of your machine you need to know:

* The mm per revolution of the lead screw
* The full steps per revolution of your steppers (typically 200)
* The microsteps per step of your controller (typically 1, 2, 4, 8, or 16). _Tip: Using high microstep values (e.g 16) can reduce your stepper motor torque, so use the lowest that gives you the desired axes resolution and comfortable running properties._

The steps/mm can then be calculated like this: ```steps_per_mm = (steps_per_revolution*microsteps)/mm_per_rev```

Compute this value for every axis and write these settings to Grbl.

### $3 – Step pulse, microseconds

Stepper drivers are rated for a certain minimum step pulse length. Check the data sheet or just try some numbers. You want as short pulses as the stepper drivers can reliably recognize. If the pulses are too long you might run into trouble running the system at high feed rates. Generally something between 5 and 50 microseconds works fine.

### $4 and $5 – Default feed and seek rates, mm/min

This setting sets the default seek(G0) and feed rates(G1,G2,G3) after Grbl powers on and initializes. The seek rate (aka rapids) is used for moving from point A to point B as quickly as possible, usually for traversing into position. The seek rate should be set at the maximum speed your machine can go in any axes movement. The default feed rate usually does not enter into the picture as feed rates will generally be specified in the g-code program, but if not, this default feed rate will be used.

NOTE: In future releases, the default feed rate will be removed. G-code standards do not have a default feed rate (G1,G2,G3), but error out if there has been no 'F' feed rate command specified. The seek rate (aka rapids) will be updated so users can specify the maximum speed each axes can move, rather than have just one speed that limits all of them.

### $6 – Step port invert mask, int:binary

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

``$6=96``

Now when you view the current settings, you should now see this in your invert mask line with the binary representation of the number (bits 5 and 6 should now show a 1 to indicate inversion.)

``$6=96 (step port invert mask. int:1100000)``

### $7 - Step idle delay, msec

Every time your steppers complete a motion and come to a stop, Grbl will disable the steppers by default. The stepper idle lock time is the time length Grbl will keep the steppers locked before disabling. Depending on the system, you can set this to zero and disable it. On others, you may need 25-50 milliseconds to make sure your axes come to complete stop before disabling. (My machine tends to drift just slightly if I don't have this enabled.) OR, you can always keep your axes enabled at all times by setting this value to the maximum 255 milliseconds. Again, just to repeat, you can keep all axes always enabled by setting `$7=255`.

### $8 – Acceleration, mm/sec^2

This is the acceleration in mm/second/second. You don’t have to understand what that means, suffice it to say that a lower value gives smooooother acceleration while a higher value yields tighter moves and reach the desired feedrates much quicker. In technical terms, this is the point to point acceleration of your machine, independent of axes. Set this acceleration value as high as your most limiting axes can let you without losing *ANY* steps. Usually you'd like to give yourself a little buffer, because if you lose steps, Grbl has no idea this has happened (steppers are open-loop control) and will keep going.

### $9 –  Junction deviation, mm

Cornering junction deviation is used by the acceleration manager to determine how fast it can move through a path. The math is a bit complicated but in general, higher values gives generally faster, possibly jerkier motion. Lower values makes the acceleration manager more careful and will lead to careful and slower cornering. So if you run into problems where your machine tries to take a corner too fast, *decrease* this value to make it slow down. If you want your machine to move faster through junctions, *increase* this value to speed it up. For technical people, hit this [link](http://t.co/KQ5BvueY) to read about Grbl's cornering algorithm, which accounts for both velocity and junction angle with a very simple, efficient, and robust method.

### $10 – Arc, mm/segment

Grbl renders circles and arcs by subdividing them into teeny tiny lines. You will probably never need to adjust this value – but if you find that your circles are too crude (really? one tenth of a millimeter is not precise enough for you? Are you in nanotech?) you may adjust this. Lower values gives higher precision but may lead to performance issues.

### $11 - N-arc correction, int

This is an advanced setting that shouldn't be changed unless there are circumstances that you need to. To make G02/03 arcs possible in Grbl, Grbl approximates the location of the next arc segment by a small angle approximation. N-arc correction is the number of approximate arc segments performed before Grbl computes an exact arc segment to correct for the approximation error drift. Computing these exact locations are computationally expensive, but there are some extreme cases where the small angle approximations can introduce enough error to be noticeable, like very very small arcs with a large arc segment length. Change this setting only if find strange problems with arcs, but it's not recommended to go below 3 because this may lead to buffer starvation, where the axes slow down and hiccup. But, results can vary.

### $12 - N-decimal, int

Set how many decimal places all of the floating point values Grbl reports. Not much more complicated than that.

### $13 - Report inches, bool

Grbl v0.8 has a real-time positioning reporting feature to provide a user feedback on where the machine is exactly at that time. By default it is set to report in mm, but by sending a `$13=1` command, you send this boolean flag to true and the status reporting feature will now report in inches. `$13=0` to set back to mm.

### $14 - Auto start, bool

In a more professional CNC environment, pros start a job by loading up their program and then pressing the 'cycle start' button on their machine. It begins the job. Grbl does the same thing, but not by default. As a learning tool, we 'auto-cycle start' any g-code command that user sends to Grbl, for just jogging the machine to see if move in the direction they think it should go, or just seeing with what their machine can do. This makes it easier to load up your machine with Grbl and pick up on how it works, rather than having to diligently hit 'cycle start' anytime you want to move any your axes. Once you learn your machine and get a good handle on g-code, you can disable the 'auto cycle-start' feature by sending Grbl `$14=0` command. (May need a soft-reset or power cycle to load the change.)

Another way of saying that might be:

If $14=0 then some gcode commands like Xn, Yn etc. won't happen when you enter them at a serial terminal.
If $14=1 then the gcode motion commands will happen. 

Apparently big cnc's won't execute gcode until the operator presses a 'cycle start' button. The gcode commands were received but queued and awaiting the 'cycle start' button. Makes sense yes? When $14=0 then that's how grbl acts too. You need the button! (in which case attach the feed-hold button too!).

When $14=1 then the grbl software automatically presses a software version of the 'cycle start' for you each time you 'enter' a gcode command line via a serial terminal. This is done to make it more convenient for you to enter commands and see something happen without having to push the button. 


### $15 - Invert step enable, bool

By default, the stepper enable pin is high to disable and low to enable. If your setup needs the opposite, just invert the stepper enable pin by typing `$15=1`. Disable with `$15=0`. (May need a power cycle to load the change.)

### $16 - Hard limits, bool

Hard limit switches are a safety feature to help prevent your machine from traveling way too far off the ends of travel and crashing or breaking something expensive. Basically you wire up some switches (mechanical or optical) near the end of travel of each axes, or where ever you feel like there might be trouble if your program moves too far to where it shouldn't. When the switch triggers, it will immediately stop all motion, shutdown the coolant and spindle (if connected), and go into alarm mode, which forces you to check your machine and reset everything.

To use hard limits with Grbl, the limit pins are held high with an internal pull-up resistor, so all you have to do is wire in a normally-open switch with the pin and ground and enable hard limits with `$16=1`. That's it. (Disable with `$16=0`) If you want a limit for both ends of travel of one axes, just wire in two switches in parallel with the pin and ground, so if either one of them trips, it triggers the hard limit.

Just know, that a hard limit event is considered to be critical event, where steppers immediately stop and will have likely lost steps. Grbl doesn't have any feedback on position, so it can't guarantee it has any idea where it is. So, if a hard limit is triggered, Grbl will go into an infinite loop ALARM mode, giving you a chance to check your machine and forcing you to reset Grbl. Remember it's a purely a safety feature.

If you have issues with the hard limit switch constantly triggering after you reset, a soft-reset will reset Grbl into an alarm state, where you can access the settings and Grbl commands, but all g-codes and startup blocks will be locked out. So you can disable the hard limits setting and then $X unlock the alarm. Or, you can wire in a normally-closed switch in series with ground to all the limit switches to disconnect the switches temporarily so you can have Grbl move your axes off the switches.


### $17 - Homing cycle, bool

Ahh, homing. Something that has been sorely needed in Grbl for a long time. It's now fully supported in v0.8. For those just initiated into CNC, the homing cycle is used to accurately and precisely locate position zero on a machine (aka machine zero) everytime you startup your Grbl between sessions. In other words, you know exactly where you are at any given time, every time. Say you start machining something or are about to start the next step in a job and the power goes out, you re-start Grbl and Grbl has no idea where it is. You're left with the task of figuring out where you are. If you have homing, you always have the machine zero reference point to locate from, so all you have to do is run the homing cycle and resume where you left off.

To set up the homing cycle for Grbl, you need to have limit switches in a fixed position that won't get bumped or moved, or else your reference point gets messed up. Usually they are setup in the farthest point in +x, +y, +z of each axes. Wire your limit switches in with the limit pins and ground, just like with the hard limits, and enable homing. If you're curious, you can use your limit switches for both hard limits AND homing. They play nice with each other. 

By default, Grbl's homing cycle moves the Z-axis positive first to clear the workspace and then moves both the X and Y-axes at the same time in the positive direction. To set up how your homing cycle behaves, there are more Grbl settings down the page describing what they do (and compile-time options as well.)

Also, one more thing to note, when homing is enabled. Grbl will lock out all g-code commands until you perform a homing cycle. Meaning no axes motions, unless the lock is disabled ($X) but more on that later. Most, if not all CNC controllers, do something similar, as it mostly a safety feature to help users from making positioning mistake, which is very easy to do and be saddening when a mistake ruins a part. If you find this annoying or find any weird bugs, please let us know and we'll try to work on it so everyone is happy. :)

NOTE: Check out config.h for more homing options for advanced users. You can disable the homing lockout at startup, configure which axes move first during a homing cycle and in what order, and more.


### $18 - Homing dir invert mask, int:binary

By default, Grbl assumes your homing limit switches are in the positive direction, first moving the z-axis positive, then the x-y axes positive before trying to precisely locate machine zero by going back and forth slowly around the switch. If your machine has a limit switch in the negative direction, the homing direction mask can invert the axes direction. It works just like the invert stepper mask, where all you have to do set the axis direction pins to 1 that you want to invert and that axes will now search for the limit pin in the negative direction.

### $19 - Homing feed, mm/min

The homing cycle first searches for the limit switches at a higher seek rate, and after it finds them, it moves at a slower feed rate to hone into the precise location of machine zero. Homing feed rate is that slower feed rate. Set this to whatever rate value that provides repeatable and precise machine zero locating. 

### $20 - Homing seek, mm/min

Homing seek rate is the homing cycle search rate, or the rate at which it first tries to find the limit switches. Adjust to whatever rate gets to the limit switches in a short enough time without crashing into your limit switches if they come in too fast. This seek rate behaves a little differently than the main stepper driver. Instead of the rate from point to point, it just moves all of the axes at the same individual rate, regardless of how many axes are moving at the same time. So, the XY seek move will seem to move about 41% faster than if you would move it with a G1 command. (You can disable this in config.h if it bothers you. It's there to speed up the homing cycle.)

### $21 - Homing debounce, ms

Whenever a switch triggers, some of them can have electrical/mechanical noise that actually 'bounce' the signal high and low for a few milliseconds before settling in. To solve this, you need to debounce the signal, either by hardware with some kind of signal conditioner or by software with a short delay to let the signal finish bouncing. Grbl performs a short delay only homing when locating machine zero. Set this delay value to whatever your switch needs to get repeatable homing. In most cases, 5-25 milliseconds is fine.

### $22 - Homing pull-off, mm

To play nice with the hard limits feature, where homing can share the same limit switches, the homing cycle will move off the all of the limit switches by this pull-off travel after it completes. In other words, it helps to prevent accidental triggering of the hard limit after a homing cycle. 

The homing seek rate setting controls how quickly the pull-off maneuver moves, like a G1 command.

-------------------------------------
# Grbl Internal Commands:

Technically, the remaining Grbl commands are not configuration commands, but we're going to explain them here for no good reason, other than for completeness.

## The three current state commands: `$G, $#, ?`

Grbl provides three commands to report its current state and where it is. Of the three, one of them is real-time, responding with current position. The other two are not, but respond with how incoming blocks are going to be processed depending on the states set by g-code, settings, or switches. The reason they are not realtime is that Grbl has an internal buffer that stores and plans the buffered motions. The incoming gcode blocks just tack on a new motion at the end of the buffer when it has room. The buffer basically ensures Grbl motion move at the fastest and safest rates possible, constantly re-calculating it real-time as motions finish and new ones come in.

### `$G` - View gcode parser state

This command prints all of the active gcode modes that the parser will interpret any incoming command. These modes include G20/G21 inches/mm mode, G54-G59 active work coordinate system, G0/G1/G2/G3 active motion mode, G17/G18/G19 active plane selection, G90/G91 absolute mode, G93/G94 inverse feed rate mode, M0/M1/M2 program flow, M3/M4/M5 spindle state, M8/M9 coolant state, T tool number, and F active feed rate. It will also list any active `$Sx` Grbl switches, like `$S1` block delete. When called, Grbl will return a line like this:

```
[G0 G54 G17 G21 G90 G94 M0 M5 M9 T0 F500.000]
```

### `$#` - View gcode parameters

G-code parameters generally store the G54-G59 work coordinate offsets and G28/G30 home positions (must not be confused with homing and machine zero. These home positions can be set anywhere in the machine space by the G28.1/G30.1 commands). Most of these parameters are directly written to EEPROM anytime they are changed and are persistent. Meaning that they will remain the same, regardless of power-down, until they are explicitly changed. 

G54-G59 work coordinates can be changed via the `G10 L2 Px` or `G10 L20 Px` command defined by the NIST gcode standard and the EMC2 (linuxcnc.org) standard. G28/G30 home positions can be changed via the `G28.1` and the `G30.1` commands, respectively. Please note that G92 is not persistent or held in EEPROM per g-code standards, and will reset to zero when Grbl is reset.  Please read these g-code standard to understand how they are used.

When `$#` is called, Grbl will respond with the stored offsets from machine coordinates for each system as follows.

```
[G54:4.000,0.000,0.000]
[G55:4.000,6.000,7.000]
[G56:0.000,0.000,0.000]
[G57:0.000,0.000,0.000]
[G58:0.000,0.000,0.000]
[G59:0.000,0.000,0.000]
[G28:1.000,2.000,0.000]
[G30:4.000,6.000,0.000]
[G92:0.000,0.000,0.000]
```

For the most part, these offset are not particularly useful unless you have homing enabled. If you do have homing enabled, these are wonderfully awesome, because once you home, you can always move back to same stored position by using the work coordinate systems within the accuracy of your machine. Or if you are making multiple parts and have a tool path for just one part, all you have to do is setup your work coordinate systems to the location where the next part is going to be made and re-run that same code.

### `?` - Current status

The `?` command immediately returns Grbl's active state and the real-time current position, both in machine coordinates and work coordinates. This may be sent at any time and works asynchronously with all other processes that Grbl is doing. The `$13` Grbl setting determines whether it reports millimeters or inches. When `?` is pressed, Grbl will immediately reply with something like the following:

```
<Idle,MPos:5.529,0.560,7.000,WPos:1.529,-5.440,-0.000>
```

The active states Grbl can be in are: **Idle, Queue, Run, Hold, Home, Alarm, Check** 
- **Idle**:  All systems are go and it's ready for anything.
- **Queue**:  Motion(s) are queued in the planner buffer waiting for a cycle start command to be issued. Certain processes like check g-code mode can't run while something is queued. Reset to clear the queue. 
- **Run**: Indicates a cycle is running.
- **Hold**: A feed hold is in process of executing, or slowing down to a stop. After the hold is complete, Grbl will enter a Queue state, waiting for a cycle start to resume the program.
- **Home**: In the middle of a homing cycle. NOTE: Positions are not updated live during the homing cycle, but they'll be set to [0,0,0] once done.
- **Alarm**: This indicates something has gone wrong or Grbl doesn't know its position. This state locks out all g-code commands, but allows you to interact with Grbl's settings if you need to. '$X' kill alarm lock releases this state and puts Grbl in the Idle state, which will let you move things again. As said before, be cautious of what you are doing after an alarm.
- **Check**: Grbl is in check g-code mode. It will process and respond to all g-code commands, but not motion or turn on anything. Once toggled off with another '$C' command, Grbl will reset itself.


##  Other Commands `$C $X $H` `~ ! Ctrl-X`

### `$C` - Check gcode mode
This toggles the Grbl's gcode parser to take all incoming blocks process them completely, as it would in normal operation, but it does not move any of the axes, ignores dwells, and powers off the spindle and coolant. This is intended as a way to provide the user a way to check how their new g-code program fares with Grbl's parser and monitor for any errors. (And eventually this will also check for soft limit violations.)

When toggled off, Grbl will perform an automatic soft-reset (^X). This is for two purposes. It simplifies the code management a bit. But, it also prevents users from starting a job when their g-code modes are not what they think they are. A system reset always gives the user a fresh, consistent start.

NOTE: Eventually, the check gcode mode could be re-factored to allow for a "program resume" feature in Grbl. This means that Grbl could start a g-code program anywhere. It would internally go through all of the g-code program up to the desired mid-program resume point to set all of the parser states and locations, move to that start point, and begin executing/moving from that point on. For example, say you had to E-stop in the middle of a program because you forgot something or you have the wrong tool in the spindle. Your part is fine and need to restart the program. Right now, you'll have to start the program from the beginning and let it physically move and run up to the point where you e-stopped. If you have a long program, this could take a while. Instead, a "program resume" will just go through the beginning of the program internally, without moving anything, and only begin moving from the resume point on.

### `$X` - Kill alarm lock
Grbl's alarm mode is a state when something has gone critically wrong, like a hard limit or an abort during a cycle, or if Grbl doesn't know its position. By default, if you have homing enabled and power-up the Arduino, Grbl enters the alarm state, because it does not know its position. The alarm mode will lock all g-code blocks until the '$H' homing cycle has been performed. Or if a user needs to override the alarm lock to move their axes off their limit switches, for example, '$X' kill alarm lock will override the locks and allow g-code functions to work again.  

But, tread carefully!! This should only be used in emergency situations. The position has likely been lost, and Grbl may not be where you think it is. So, it's advised to use G91 incremental mode to make short moves. Then, perform a homing cycle or reset immediately afterwards. 

### `$H` - Run homing cycle
This command is the only way to perform the homing cycle in Grbl. Previously, G28 and G30 would automatically start the homing cycle, but this is incorrect according to the g-code standards. Homing is a completely separate command handled by the controller. G28 and G30 only move to a 'home'/pre-defined position that is stored in the g-code parameters, which can be located anywhere in the machine.

TIP: After running a homing cycle, rather jogging manually all the time to a position in the middle of your workspace volume. You can set a G28 or G30 pre-defined position to be your post-homing position, closer to where you'll be machining. To set these, you'll first need to jog your machine to where you would want it to move to after homing. Type G28.1 (or G30.1) to have Grbl store that position. So then after '$H' homing, you could just enter 'G28' (or 'G30') and it'll move there auto-magically. In general, I would just move the XY axis to the center and leave the Z-axis up. This ensures that there isn't a chance the tool in the spindle doesn't catch on anything.

### `~` - Cycle start
This is the cycle start or resume command that can be issued at any time, as it is a real-time command. When Grbl has motions queued in its buffer and is ready to go, the `~` cycle start command will start executing the buffer and Grbl will begin moving the axes. However, by default, auto-cycle start is enabled, so new users will not need this command unless a feed hold is performed. When a feed hold is executed, cycle start will resume the program. Cycle start will only be effective when there are motions in the buffer ready to go and will not work with any other process like homing.

### `!` - Feed hold
The feed hold command will bring the active cycle to a stop via a controlled deceleration, so not to lose position. It is also real-time and may be activated at any time. Once finished or paused, Grbl will wait until a cycle start command is issued to resume to program. Feed hold can only pause a cycle and will not affect homing or any other process.

If you need to stop a cycle mid-program and can't afford losing position, perform a feed hold to have Grbl bring everything to a controlled stop. Once finished, you can then issue a reset. Always try to execute a feed hold whenever the machine is running before hitting reset, except of course if there is some emergency situation.

### `Ctrl-x` - Reset Grbl
This is Grbl's soft reset command. It's real-time and can be sent at any time. As the name implies, it resets Grbl, but in a controlled way, *retains* your machine position, and all done without powering down your Arduino. The only times a soft-reset could lose position is when problems like if the steppers were killed while they were moving. If so, it will report if Grbl's tracking of the machine position has been lost. This is because an uncontrolled deceleration can lead to lost steps, and Grbl has no feedback to how much it lost (this is the problem with steppers in general). Otherwise, Grbl will just re-initialize, run the startup lines, and continue on its merry way. 

Please note that it's recommended to do a soft-reset before starting a job. This guarantees that there aren't any g-code modes active that should be from playing around or setting up your machine. So, your machine will always starts fresh and consistently, and your machine does what you expect it to.


# Other Resources:
* **[DANK](http://dank.bengler.no/-/page/show/5474_configuringgrbl)** _Last updated 2/2011._ Only applies to grbl v0.6.