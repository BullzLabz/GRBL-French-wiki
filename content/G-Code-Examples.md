_This wiki page is intended to provide example G-code programs to test and proof Grbl. Please feel free to contribute short programs with detailed descriptions of their movements and purpose. As a rule of thumb, keep these example programs short, easy to edit, and generic for testing on a variety of machines with travel and feedrate limits in mind._

## Tips
- It's always good practice to insert a comment header at the beginning of your program to remind you of what your program does, if it's been proven to work, when you wrote it, what tool it's been written for, what size stock you'll need, and where the part origin (beginning point) is located. Most of the time, good CAM programs will automatically do this for you, but if yours doesn't, here's an example.

```    
(DIAMOND, CIR, SQ TEST PROGRAM)
(FEB-08-12, 12:05) 
( *** UNPROVEN PROGRAM *** )
( RUN IN VISE ON PARALLELS )
(Z OFFSET: TOP OF MATERIAL WITH )
( 0.375" MATERIAL ABOVE VISE JAWS )
(X0,Y0,Z0= Center, Center, Top)
(STOCK ORIGIN = X0. Y0. Z.01)
(MATERIAL TYPE= ALUMINUM inch - 6061)
(MATERIAL SIZE= X1.75 Y1.75 Z.5)
(TOOL= 1/4 2-FLUTE HSS END MILL)
```

- Place an initialization block at the beginning of your program to set all of the G-code modes explicitly for your program. These are things such as mm/inch modes, incremental/absolute modes, feedrate modes, plane selection, or work coordinate system.  Even though the defaults of your machine may work with your G-code program now, this doesn't mean that it will later or if you forget it was set differently by a previous program. This can result in a crash. An initialization block typically looks something like this.
```
G17 G20 G90 G94 G54
```

## Examples

### Draw a Circle
This program draws a 1" diameter circle about the origin in the X-Y plane. It should begin by seeking the Z-axis to 0.25", travel to X=-0.5 and Y=0.0, and lower back to Z=0.0. The program will then draw a clockwise circle at a slow feedrate. When finished, it will lift the Z-axis up 0.1" and then seek back to X=0.0, Y=0.0, and Z=0.25 to complete.

```
G17 G20 G90 G94 G54
G0 Z0.25
X-0.5 Y0.
Z0.1
G01 Z0. F5.
G02 X0. Y0.5 I0.5 J0. F2.5
X0.5 Y0. I0. J-0.5
X0. Y-0.5 I-0.5 J0.
X-0.5 Y0. I0. J0.5
G01 Z0.1 F5.
G00 X0. Y0. Z0.25
```