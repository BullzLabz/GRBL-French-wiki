_This page is intended to be a central place where grbl future development path of features are listed. Feel free to discuss and place any insightful comments into the 'Issues' messaging board._

# Queued for the next Grbl release:

* **Real-time adjustable feedrates**: On Haas CNCs, there is a feature with 5%, 25%, 50%, and 100% real-time feedrate adjustments. This is primarily for testing and proofing G-code programs and to adjust feedrates live, if a cutting tool is chattering and needs to be slightly sped up or down. The implementation has been solved at this point, but just needs to be installed and bugs to be worked out.

* **Other overrides**: Along with feedrate overrides, other overrides will be added, such as coolant, spindle, and rapids overrides.

* **Backlash compensation**: There are many ways of handling backlash, which all have different levels of complexity and drawbacks. The main problem is how to ensure that the CNC keeps moving smoothly as possible through the backlash without compromising accuracy.

* **Jogging**: This is planned as a separate Grbl run-time mode to be run independent of grbl's G-code parser. In production CNC machines, this is the same behavior, where jogging is a special control mode.

* **Other Advanced Controller Features:** Program resume (restart a job midway), block deletes, single block mode, and '%' program mode.

## Other Possibilities
* **More hardware support**: At v1.0, the code should be stabilized enough to begin officially inserting more hardware support into the grbl codebase. A lot of this work is already on-going by some great people, but from a development standpoint, it's difficult to write new code when multiple hardware systems need to be supported for each minute iteration. As this moves forward, the hard work people have already put in will be directly integrated when the time comes.
* **SD-card support**: This may be an important feature in upcoming versions. Having on-board storage will free up the serial port for real-time commands and status reports from no longer having a constant stream of gcode to grbl. This also allows for a more true headless CNC setup. May be integrated as a compile-time configuration option.
* **4th axis capability**: Not high on the priority list, since no one has a 4th axis yet to test it on.
* **Lathe branch**: Not to difficult to port to a lathe, since they are only two axis. Just time and effort needed for this.
* **Synchronized spindle**: Synchronizing the spindle with axes movement would be a great challenge. This is commonly used for mill threading and for advanced machining of complex lathe parts. The main issue here is ensuring that the axes stepper motors do not exceed their acceleration limits, because if they do, steps and location will be lost. Typically a motor encoder is used to detect spindle rotation (steppers don't have enough torque or speed for driving spindles), and synchronizing the spindle motor encoder and axis stepper motor can be difficult to do robustly and universally for varying setups and would require a special motion mode that doesn't use Grbl's acceleration planner. This feature is usually more successful on an ad hoc basis, where motions are tuned for each machine.