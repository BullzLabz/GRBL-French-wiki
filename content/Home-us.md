
_Welcome to the grbl wiki! Please feel free to modify these pages to help keep grbl up-to-date!_

### A propos de Grbl

Grbl est un logiciel gratuit, open source, les logiciels de haute performance pour contrôler le mouvement des machines qui se déplacent, qui font des choses, ou qui font bouger les choses, et se déroulera sur un Arduino droite. Si le mouvement de café était une industrie, Grbl serait la norme de l'industrie.

La plupart des imprimantes 3D open source ont Grbl dans leurs cœurs. Il a été adapté pour être utilisé dans des centaines de projets, y compris la découpe au laser, des écrivains automatiques à main, les foreurs de trous, peintres graffiti et machines de tirage excentriques. Grâce à sa performance, la simplicité et les exigences matérielles frugal Grbl a grandi dans un petit phénomène open source.

En 2009, [Simen Svale Skogsrud] (http://bengler.no/grbl) a honoré la communauté open-source en écrivant et en libérant les premières versions de Grbl à tous [inspirés par l'Arduino Gcode Interprète par Mike Ellery] (http://www.contraptor.org/grbl-gcode-interpreter).
Depuis 2011, Grbl est un projet open-source qui va de l'avant mené par une communauté active sous la direction pragmatique de Sungeun K. Jeon Ph.D. (@chamnit).

-----

### Supporteurs officiel du Grbl CNC Project

![Supporteurs officiel](https://dl.dropboxusercontent.com/u/2221997/Contributors.png)

_Grbl est un projet open-source et alimentée par le temps libre de nos administrateurs et utilisateurs intrépides altruistes. Si vous souhaitez faire un don, tous les profits seront utilisés pour aider à garder ce projet durable et à financer l'équipement. Merci!_

[![Donate](https://www.paypalobjects.com/en_US/i/btn/btn_donate_LG.gif)](https://www.paypal.com/cgi-bin/webscr?cmd=_s-xclick&hosted_button_id=EBQWAWQAAT878)

-----

### Qui peu utiliser Grbl

Les bricoleurs qui font du fraisage et ont besoin d'une belle finition, simple contrôleur pour leur système qui se déroulera l'omniprésent Arduino Uno. Les gens qui détestent encombrer leur espace avec Legacy PC-tours juste pour le port parallèle. Bricoleurs qui ont besoin d'un contrôleur écrit en rangé, C modulaire comme base pour leur projet.

### Super fonctionalitées

Grbl est prêt pour la production de produit final. Nous l'utilisons pour tous nos fraisage, l'exécuter à partir de nos ordinateurs portables utilisant des interfaces graphiques grands écrits par l'utilisateur ou avec un simple script de la console (inclus) pour écouter le G-code. Il est écrit en C, optimisé pour utiliser toutes les fonctionnalités intelligentes de puces ATmega328P de l'Arduino pour atteindre un timing précis et le fonctionnement asynchrone. Il est capable de maintenir plus de taux d'étape 30kHz et délivre un flux propre libre, gigue des impulsions de commande.

Grbl est concu pour des machines comprtant 3 axes. Aucun axes en rotation (pas encore) - simplement les axes X, Y et Z.

Le code G interprète implémente un sous-ensemble de la RS274 / NGC standard NIST et est testé avec la sortie d'un certain nombre de CAM-outils sans aucun problème. Linéaire, circulaire et hélicoïdale mouvement sont entièrement pris en charge.

* Supported G-Codes in **v0.9i**
 * **G38.3, G38.4, G38.5:** _Probing_
 * **G40:** _Cutter Radius Compensation Modes_
 * **G61:** _Path Control Modes_
 * **G91.1:** _Arc IJK Distance Modes_
* Supported G-Codes in **v0.9h**
 * **G38.2:** _Probing_
 * **G43.1, G49:** _Dynamic Tool Length Offsets_
* Supported G-Codes in **v0.8** (and **v0.9**)
 * **G0, G1:** _Linear Motions_
 * **G2, G3:** _Arc and Helical Motions_
 * **G4:** _Dwell_
 * **G10 L2, G10 L20:** _Set Work Coordinate Offsets_
 * **G17, G18, G19:** _Plane Selection_
 * **G20, G21:** _Units_
 * **G28, G30:** _Go to Pre-Defined Position_
 * **G28.1, G30.1:** _Set Pre-Defined Position_
 * **G53:** _Move in Absolute Coordinates_
 * **G54, G55, G56, G57, G58, G59:** _Work Coordinate Systems_
 * **G80:** _Motion Mode Cancel_
 * **G90, G91:** _Distance Modes_
 * **G92:** _Coordinate Offset_
 * **G92.1:** _Clear Coordinate System Offsets_
 * **G93, G94:** _Feedrate Modes_
 * **M0, M2, M30:** _Program Pause and End_
 * **M3, M4, M5:** _Spindle Control_
 * **M8, M9:** _Coolant Control_

Most configuration options can be set at runtime and are saved in eeprom between sessions and even retained between different versions of Grbl as you upgrade the firmware. For descriptions of these G-codes, we follow the [NIST G-code guidelines](http://citeseerx.ist.psu.edu/viewdoc/summary?doi=10.1.1.15.7813) and LinuxCNC.org also provides great documentation for these as well. [(G-codes)](http://www.linuxcnc.org/docs/2.5/html/gcode/gcode.html) [(M-codes)](http://www.linuxcnc.org/docs/2.5/html/gcode/m-code.html) [(Other codes)] (http://www.linuxcnc.org/docs/2.5/html/gcode/other-code.html) and the [Shapeoko wiki](http://www.shapeoko.com/wiki/index.php/G-Code) attempts to list all codes supported by Grbl with appropriate commentary.

###Acceleration management

In the early days, Arduino-based CNC controllers did not have acceleration planning and couldn't run at full speed without some kind of easing. Grbl’s constant acceleration-management with look ahead planner solved this issue and has been replicated everywhere in the micro controller CNC world, from Marlin to TinyG. Grbl intentionally uses a simpler constant acceleration model, which is more than adequate for home CNC use. Because of this, we were able to invest our time optimizing our planning algorithms and making sure motions are solid and reliable. When the installation of all the feature sets we think are critical are complete and no longer requires us to modify our planner to accommodate them, we intend to research and implement more-advanced motion control algorithms, which are usually reserved for machines only with very high feed rates (i.e. pick-and-place) or in production environments. Lastly, here's a [link](http://onehossshay.wordpress.com/2011/09/24/improving_grbl_cornering_algorithm/) describing the basis of our high speed cornering algorithm so motions ease into the fastest feed rates and brake before sharp corners for fast yet jerk free operation.

###Limitations by design

We have limited G-code-support by design. This keeps the Grbl source code simple, lightweight, and flexible, as we continue to develop, improve, and maintain stability with each new feature. Grbl supports all the common operations encountered in output from CAM-tools, but leave some human G-coders frustrated. No variables, no tool databases, no functions, no canned cycles, no arithmetic and no control structures. Just the basic machine operations and capabilities. Anything more complex, we think interfaces can handle those quite easily and translate them for Grbl.

### New features in v0.9!
Has it really be a year and a half since the last version? Oh my how time flies.. Anyhow, this is a **HUGE** update! Stability and performance was honed in and sharpened to make Grbl production quality. Here's a summary of the new changes:

* **IMPORTANT:**
 * **Default serial baudrate is now 115200! (Up from 9600)**
 * **Z-Axis limit input on D11 has swapped with spindle enable D12 to support variable spindle PWM output.**
 * **No QUEUE state: Queue was removed due to it being redundant. Holds now suspend Grbl and only allow realtime commands. Cycle start resumes, and reset exits.**
* **_NEW_ Super Smooth Stepper Algorithm:**  Complete overhaul of the handling of the stepper driver to simplify and reduce task time per ISR tick. Much smoother operation with the new Adaptive Multi-Axis Step Smoothing (AMASS) algorithm which does what its name implies (see stepper.c source for details). Users should immediately see significant improvements in how their machines move and overall performance!
* **Stability and Robustness Updates:** Grbl's overall stability has been focused on for this version. The planner and step-execution interface has been completely re-written for robustness and incorruptibility by the introduction of an intermediate step segment buffer that "checks-out" steps from the planner buffer in real-time. This means we can now fearlessly drive Grbl to it's highest limits. Combined with the new stepper algorithm and planner optimizations, this translated to **5x to 10x** overall performance increases in our testing! Also, stability and robustness tests have been reported to easily take 1.4 million (yes, **million**) line G-code programs like a champ!
* **(x4)+ Faster Planner:** Planning computations improved four-fold or more by optimizing end-to-end operations, which included streamlining the computations and introducing a planner pointer to locate un-improvable portions of the buffer and not waste cycles recomputing them.
* **Variable Spindle Speed Output:** Enables a hardware PWM output for 'S' G-code commands. **NOTE:** This feature requires a pin swap with the Z-limit D11 pin and spindle enable D12 pin to access the hardware PWM on pin D12. The Z-limit pin, now on D12, should work just as it did before.
* **Compile-able via Arduino IDE!:** Grbl's source code may now be downloaded and altered, and then be compiled and flashed directly through the Arduino IDE, which should work on all platforms. See the Wiki for details on how to do it.
* **G-Code Parser Overhaul:** Completely re-written from the ground-up for 100%-compliance* to the G-code standard. (* Parts of the NIST standard are a bit out-dated and arbitrary, so we altered some minor things to make more sense. Differences are outlined in the source code.) We also took steps to allow us to break up the G-code parser into distinct separate tasks, which is key for some future development ideas and improvements.
* **Independent Acceleration and Velocity Settings:** Each axis may be defined with unique acceleration and velocity parameters and Grbl will automagically calculate the maximum acceleration and velocity through a path depending on the direction traveled.
* **Soft Limits:** Checks if any motion command exceeds workspace limits before executing it, and alarms out, if detected.
* **Probing:** The G38.2, G38.3, G38.4, & G38.5 straight probe G-code commands are now supported and connected through the A5 pin.
* **Tool Length Offsets:** Probing doesn't make sense without tool length offsets (TLO), so we added it! The G43.1 dynamic TLO (described by linuxcnc.org) and G49 TLO cancel commands are now supported. G43.1 dynamic TLO works like the normal G43 TLO (NOT SUPPORTED) but requires an additional axis word with the offset value attached. We did this so Grbl does not have to track and maintain a tool offset database in its memory. Perhaps in the future, we will support a tool database, but not for this version.
* **Improved Arc Performance:** The larger the arc radius, the faster Grbl will trace it! We are now defining arcs in terms of arc chordal tolerance, rather than a fixed segment length. This automatically scales the arc segment length such that maximum radial error of the segment from the true arc is never more than the chordal tolerance value of a super-accurate default of 0.002 mm.
* **New Grbl SIMULATOR! (by @jgeisler and @ashelly):** A completely independent wrapper of the Grbl main source code that may be compiled as an executable on a computer. No Arduino required. Simply simulates the responses of Grbl as if it was on an Arduino.
* **CPU Pin Mapping:** In an effort for Grbl to be compatible with other AVR architectures, such as the 1280 or 2560, a new cpu_map.h pin configuration file has been created to allow Grbl to be compiled for them. This is currently user supported, so your mileage may vary. If you run across a bug, please let us know or better, send us a fix! Thanks in advance!
* **Configurable Real-time Status Reporting:** Users can now customize the type of real-time data Grbl reports back when they issue a '?' status report. This includes data such as: machine position, work position, planner buffer usage, serial RX buffer usage.
* **Updated Homing Routine:** Sets workspace volume in all negative space regardless of limit switch position. Common on pro CNCs. But, the behavior may be changed by a compile-time option though. Now tied directly into the main planner and stepper modules to reduce flash space and allow maximum speeds during seeking.
* **CoreXY Support:** Grbl now supports CoreXY kinematics on an introductory-level. Most functions have been verified to work, but there may be bugs here or there. Please report any problems you find!
* **Safety Door Support:** Safety door switches are now supported. Grbl will force a feed hold, shutdown the spindle and coolant, and wait until the door switch has closed and the user has issued a resume. Upon resuming, the spindle and coolant will re-energize after a configurable delay and continue. Useful for OEMs that require this feature.
* **Full Limit and Control Pin Configurability:** Limits and control pins operation can now be interpreted by Grbl however you'd like, with the internal pull-up resistors enabled or disabled, or reading a high or low as a trigger. This should cover all wiring and NO or NC switch scenarios.
* **Optional Limit Pin Sharing:** Limit switches can be combined to share the same pins to free up precious I/O pins for other purposes. When combined, users must adjust the homing cycle mask in config.h to not home the axes on a shared pin at the same time. Don't worry; hard limits and the homing cycle still work just like they did before.
* **Additional Compile-Time Feature Options:** Limit/control pin state reporting, line number tracking, real-time feed rate reporting, and more.


### New features in v0.8!
A lot has happened since the v0.7. We're pushing real hard to create a simple, yet powerful CNC controller for the venerable Arduino. Here's a list of the new things that have come to v0.8.

* **Multi-Tasking Run-time Commands:** Feed hold with controlled deceleration for no loss of location, Resume after feed hold, Reset, and Status Reporting.
* **Advanced Homing Cycle:** Lots of configuration options for different types of machines from which axes to move when to their search directions. Limit switches may also be used as hard limits as well.
* **Persistent Coordinate System Data:** Work Coordinate Systems (G54-G59) and Pre-Defined Positions (G28,G30) are held in EEPROM, so they'll always be set as you last set them.
* **Check G-Code Mode:** Sets up Grbl to run through your program without moving anything, so you can check whether or not you have any errors that Grbl may not like.
* **Improved Feedback:** Reports real-time position, what Grbl is doing, the G-code parser state, and stored coordinate offset values.
* **Startup blocks:** Auto-magically runs user G-code blocks at startup or reset. Can be used to set your defaults.
* **Pin-outs:** Cycle start, feed hold, and abort are now pinned-out to the A0, A1, and A2 pins. Just connect a normally-open switch to the pin and ground. That's it!
* More Robust G-Code Parser with Error Checking Feedback.
* And much more!


### Slated for v1.0 Future Development
Grbl is constantly under development with new features being added all the time. Here's a short list of upcoming features set for the next version, but click this [link](https://github.com/grbl/grbl/wiki/Development-Path-and-Future-Needs) to read up the details.

* **Overrides:** Real-time adjustment of the feedrates, spindle speed, and coolant during a cycle. We've solved it and just need to figure out some small details before installing it.
* **Jogging:** Set up an easy interface for users to proper jogging.
* **Backlash Compensation:** Not sure it'll make it into the Uno version, but definitely when we support a bigger controller.

***
_Grbl v0.8 (and prior) is distributed under the MIT-License and was developed by Simen Svale Skogsrud, Sungeun K. Jeon Ph.D., and Jens Geisler._

_Grbl v0.9 is distributed under the GPLv3 license and is developed by Sungeun K. Jeon Ph.D.. See [Licensing](https://github.com/grbl/grbl/wiki/Licensing) for more details._
***

## Help
* **[F.A.Q.](https://github.com/grbl/grbl/wiki/Frequently-Asked-Questions)**

* **[Licensing](https://github.com/grbl/grbl/wiki/Licensing)**

* **[Compiling Grbl](https://github.com/grbl/grbl/wiki/Compiling-Grbl)**

* **[Flashing Grbl to an Arduino](https://github.com/grbl/grbl/wiki/Flashing-Grbl-to-an-Arduino)**

* **[Connecting Grbl](https://github.com/grbl/grbl/wiki/Connecting-Grbl)**

* **[Configuring Grbl v0.9](https://github.com/grbl/grbl/wiki/Configuring-Grbl-v0.9)**

* **[Configuring Grbl v0.8](https://github.com/grbl/grbl/wiki/Configuring-Grbl-v0.8)**

* **[Configuring Grbl v0.7](https://github.com/grbl/grbl/wiki/Configuring-Grbl-v0.7)**

* **[Using Grbl](https://github.com/grbl/grbl/wiki/Using-Grbl)**

* **[Interfacing with Grbl](https://github.com/grbl/grbl/wiki/Interfacing-with-Grbl)**

* **[G-Code Examples](https://github.com/grbl/grbl/wiki/G-Code-Examples)**

* **[Known Bugs](https://github.com/grbl/grbl/wiki/Known-Bugs)**