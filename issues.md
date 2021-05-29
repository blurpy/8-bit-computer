# Issues

I ran into many issues trying to get the 8-bit computer to work. This is my documentation of the issues and the fixes.


## LEDs without resistors

I started by following the videos step by step, and when it came to testing the first register I couldn't get it to work. The red LEDs on the register would be very faint and randomly on/off, and the yellow LEDs on the temporary bus would not light up at all.

The booklet that came with the kit suggested adding resistors to the LEDs, but there wasn't any room on the breadboard for that. I found a good tip in this post on reddit: [What I Have Learned (a master list of what to do and what not to do)](https://www.reddit.com/r/beneater/comments/dskbug/what_i_have_learned_a_master_list_of_what_to_do/)

I soldered resistors to the LEDs as suggested, and the register started working like in the video.

![LEDs with resistors](resources/leds_with_resistors.jpg)

I grew tired of soldering resistors to all the LEDs, so I went for LEDs with resistors built-in instead:

* [Kingbright L-7113ID-5V - 5v Red LED](https://no.rs-online.com/web/p/leds/8609709/)
* [Kingbright L-7113GD-5V - 5v Green LED](https://no.rs-online.com/web/p/leds/8609696/)
* [Kingbright L-7113YD-5V - 5v Yellow LED](https://no.rs-online.com/web/p/leds/8609712/)
* [VCC LTH5MM12VFR4600 - 12v Clear Blue LED](https://www.digikey.no/product-detail/en/visual-communications-company-vcc/LTH5MM12VFR4600/LTH5MM12VFR4600-ND/6691221)

The red, green and yellow LEDs were directly replaceable with the 5v ones from Kingbright, but I could not find any of the blue kind. Most of the blue LEDs are in positions with room for a resistor on the breadboard, so I kept the original ones. I soldered resistors only on the 4 used in the instruction register, seen in the picture above.

The blue were a bit bright with the 220Ω resistors, so I went with 1kΩ resistors on those.

I used the clear blue 12v LEDs for the ALU carry and zero bits (not the flags). They are way too bright on 12v, but fine on 5v.


## Single stepping the clock leads to occasional double step

Video showcasing the glitch:

[![YouTube video of double step glitch](resources/yt-double-step-glitch-thumb.png)](https://www.youtube.com/watch?v=wE0VCkTokac "Click to play")

Notice the very thin dip about 2/3 of the way into the button click. This is enough for it to register as 2 clock ticks:

![Oscilloscope screenshot of double step glitch](resources/clock_double_step.png)

The problem is in the monostable 555 timer circuit that is supposed to handle debouncing. The size of the capacitor used on pin 6 decides how long the debounce lasts, and the capacitor used by default in the kit is 0.1μF (104). This works fine usually, but not every time. Replacing it with a 0.33μF (334) capacitor makes the debounce much more reliable:

![Oscilloscope screenshot of double step glitch](resources/clock_double_step_cap.jpg)


## Long single steps leads to spurious clock ticks

Video showcasing the glitch:

[![YouTube video of double step glitch](resources/yt-spurious-step-glitch-thumb.png)](https://www.youtube.com/watch?v=cVWIPq9-fGs "Click to play")

Notice the amount of high frequency noise in the signal as the single step button is pressed:

![Oscilloscope screenshot of spurious ticks glitch](resources/clock_spurious_step_lm555cn.png)

This is related to the same monostable 555 timer circuit as in the previous issue. But for some reason the signal is clean for a short period when first pressing the button, but pretty soon it gets very noisy. I fixed it by replacing the monostable timer, a Fairchild LM555CN, with a Texas Instruments NE555P. 

The same test looks much better afterwards:

![Oscilloscope screenshot of spurious ticks fix](resources/clock_spurious_step_ne555p.png)

And the clock only ticks once when keeping the button pressed.


## RAM resonance glitch

Video showcasing the glitch:

[![YouTube video of RAM resonance glitch](resources/yt-ram-resonance-glitch-thumb.png)](https://www.youtube.com/watch?v=abie2o01HV0 "Click to play")

Everything seemed to work fine when building the RAM module and testing it in isolation. However, when I started connecting the other modules the whole thing started behaving erratic.  

The problem stems from the clock signal that goes into the NAND-gate on the right, through the RC-circuit. The capacitor creates some resonance that travels back through the clock wires and disturbs everything else connected to the same clock, leading to registers latching out of phase with the actual clock.

This shows the original circuit:

![RAM NAND original circuit](resources/ram_resonance_pre_fix.jpg)

And this shows the fixed circuit. The NAND-gate is used to isolate the clock signal by running it through the inverter 2 times before being sent to the RC-circuit.

![RAM NAND fixed circuit](resources/ram_resonance_post_fix.jpg)

More about this issue on Reddit: [What I Have Learned](https://www.reddit.com/r/beneater/comments/dskbug/what_i_have_learned_a_master_list_of_what_to_do/).


## RAM mode switch changes memory content

Video showcasing the glitch:

[![YouTube video of RAM mode switch glitch](resources/yt-ram-mode-glitch-thumb.png)](https://www.youtube.com/watch?v=y0mx79ixhco "Click to play")

The problem happens when pressing the mode switch on the RAM module for choosing between run mode and programming mode. Sometimes when pressing the switch the RAM content in the current memory address is changed to something random.

The reason can be seen on the oscilloscope:

![Oscilloscope screenshot of RAM mode switch pressed](resources/ram_mode_switch_press.png)

The oscilloscope probe is connected to pin 12 of the 74LS157, that goes to pin 3 of the 74189, which is the active-low write-enable pin. The signal should be high when it's not writing to the RAM, but sometimes there is a dip in the signal when pressing the mode switch, making it write whatever it can see on the inputs at the moment. The switch probably has a debounce problem. Putting a 104 capacitor (highlighted) on pin 12 to VCC solves the issue. The signal is not affected anymore when pressing the mode switch after that.

![RAM mode switch capacitor fix](resources/ram_mode_switch_cap.jpg)

Note that I'm not using the mode switch that came with the kit.


## Noise on the reset line

Video showcasing the glitch:

[![YouTube video of reset glitch](resources/yt-reset-noise-glitch-thumb.png)](https://www.youtube.com/watch?v=n3ou3BL5uEU "Click to play")

The video shows the program for counting back and forth between 0 and 255, and when it's supposed to go from 126 to 127 it goes from 126 to 1 instead.

Looking at the reset line connected to the 74LS173 flags register on pin 15 (CLR) we can see quite a bit of noise, including a large spike on the right side at 1.84v. The spike is enough to cause the A register to reset to 0 at the last step of the ADD instruction in the video, when it's about to move 127 from the ALU to the bus. Since the A register turns 0, and the B register is 1, the result of the ADD is 1 which is stored in the A register and displayed shortly after as part of the OUT instruction.

I'm guessing the noise spikes on the reset line are related to power. In this example the ALU has 7 LEDs enabled, the A register has 6. When trying to enable 7 LEDs on the bus it causes a sudden power increase and creates noise that resets the A register.

![Oscilloscope screenshot of reset noise](resources/reset_line_noise.png)

Putting a 104 capacitor (highlighted) on the reset line to ground on the flags register removes the noise and solves the issue.

![Reset noise capacitor fix](resources/reset_line_cap.jpg)
