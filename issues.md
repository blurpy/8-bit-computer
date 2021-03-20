# Issues

I ran into many issues trying to get the 8-bit computer to work. This is my documentation of the issues and the fixes.


## #1 Single stepping the clock leads to occasional double step

Video showcasing the glitch:

[![YouTube video of double step glitch](resources/yt-double-step-glitch-thumb.png)](https://www.youtube.com/watch?v=wE0VCkTokac "Click to play")

Notice the very thin dip about 2/3 of the way into the button click. This is enough for it to register as 2 clock ticks:

![Oscilloscope screenshot of double step glitch](resources/clock_double_step.png)

The problem is in the monostable 555 timer circuit that is supposed to handle debouncing. The size of the capacitor used on pin 6 decides how long the debounce lasts, and the capacitor used by default in the kit is 0.1μF (104). This works fine usually, but not every time. Replacing it with a 0.33μF (334) capacitor makes the debounce much more reliable:

![Oscilloscope screenshot of double step glitch](resources/clock_double_step_cap.jpg)


## #2 Long single steps leads to spurious clock ticks

Video showcasing the glitch:

[![YouTube video of double step glitch](resources/yt-spurious-step-glitch-thumb.png)](https://www.youtube.com/watch?v=cVWIPq9-fGs "Click to play")

Notice the amount of high frequency noise in the signal as the single step button is pressed:

![Oscilloscope screenshot of spurious ticks glitch](resources/clock_spurious_step_lm555cn.png)

This is related to the same monostable 555 timer circuit as in the previous issue. But for some reason the signal is clean for a short period when first pressing the button, but pretty soon it gets very noisy. I fixed it by replacing the monostable timer, a Fairchild LM555CN, with a Texas Instruments NE555P. 

The same test looks much better afterwards:

![Oscilloscope screenshot of spurious ticks fix](resources/clock_spurious_step_ne555p.png)

And the clock only ticks once when keeping the button pressed.
