# DIY-6-channel-trigger-sequencer
6-channel trigger sequencer
Build a 6-channel trigger sequencer for just 800 yen with SSD1306 and a 0.96-inch OLED display

twenty one
HAGIWO
HAGIWO
February 13, 2021 22:17
While trying my hand at Arduino programming, I made a modular synthesizer 6CH trigger sequencer module, so this is a memo about it.


This is my ninth programming project, which I started in an effort to break away from being a systems engineer who can't write background
code. It's been four months since I first lit up an LED with Arduino. I've made many melodic modules so far, so I wanted to try my hand at a rhythm module next.

In terms of software, I will study bit control functions.
In terms of hardware, I will study OLEDs, which I will be using for the first time.
In terms of instruments, I will study how to build a system using an interface extended by OLEDs.

Image 1

Product specifications
: Eurorack standard 3U 6HP size
Power supply: 37mA in standby mode (at 5V or 12V) 100mA at output (at 5V or 12V)
Can operate on a single 5V power supply. Or can operate on a single 12V power supply.
Output: 6CH (0-5V)
Number of steps: 16 fixed steps

Functions: Equipped with manual mode and auto mode.

1. Manual mode:
Select the STEP with the rotary encoder and select Trigger ON/OFF with the button. Pressing
RESET returns STEP to 1. Pressing
SAVE saves the current pattern. (Only one storage area is available.) Pressing
each CH will mute that pattern.

Image 5

2. Auto Mode:
Automatically plays pre-programmed presets. Multiple
presets can be selected, each with eight patterns and its own dedicated fill-in. Press
the Fill-in button to turn the fill-in on or off. Press the
Repeat button to set the number of repeats of the same pattern (4, 8, 16, 32, or infinite). When Fill-in is on, the fill-in is inserted during the final repeat. (For example, for Repeat 4, 16 steps are repeated three times, followed by one 16-step fill-in.) Press the
SW button to set the number of switches (2, 4, 8, 16, or infinite). When the above repeats are repeated the number of times specified by the SW setting, a random pattern is selected from the eight patterns of the same preset. Pressing
each CH
will mute that pattern. In Auto Mode, you cannot turn specific steps on or off.

Total: Approximately 800 yen
. Breakdown: Arduino compatible 200 yen, rotary encoder 80 yen, OLED 180 yen, panel 150 yen, other 200 yen

The design concepts for my module are as follows:
1. To run on a single +12V power supply (to be incorporated into an existing module)
2. To run on a single +5V power supply (to allow for easy debugging and power from a mobile battery)
3. To not use -12V (to prevent other modules from breaking down due to assembly errors)
4. Inexpensive and with few components
5. To be able to program (my purpose for the DIY module is to study software)
6. To be 6HP (limitations are the source of creativity)

Image 2

I used a library to program
the OLED using the SSD1306. There are sample programs explained on other web pages, so I won't go into detail here.
One thing to note is that some of the sample programs use "non-recommended" operating modes, which slows the OLED display speed down to about 0.5Hz.

Also, some things I noticed when controlling the OLED (unverified):
・The display slows down when the number of characters is increased.
・The Arduino is busy while updating the display, and other signal processing is not possible.
・The I2C communication speed is slow, and the display cannot keep up once the BPM exceeds 200.

I think there is room for improvement with some programming tweaks, but this time I wasn't particularly concerned with a fast display, so I didn't make any improvements.

The drum pattern program was the first to use bit-controlled functions.
For example, a four-beat kick can be expressed in binary as 1000, 1000, 1000, 1000. By switching the bits in each digit, a drum pattern can be constructed.

Image 4

Hardware:
Simple input/output circuit. Since there are seven jacks, there are seven protection circuits. It consumes a large amount of Schottky barrier diodes.
There is a sound (not an electrical sound, but a physical sound) coming from the OLED, so it seems that the power supply is probably very noisy. In this case, the output is 0-5V, so noise is not a concern, but when using CV output, I felt it would be better to check the power supply fluctuations.

The OLED I used is SSD1306, which is very cheap and small and fits into a 6HP module. It can be purchased on Amazon, but it's cheaper to buy it on Aliexpress.

Because the output impedance is relatively small at 470 Ω, the current consumption tends to be high while the gate is ON. I think there is no problem with 1 kΩ or 4.7 kΩ, so if you want to reduce the current consumption, you should use a smaller value.

Image 3

The source code
is rough, but I'm releasing it. I'd appreciate it if you could point out any flaws.
By the way, it's only been four months since I started programming.

Points to note
: The OLED display is updated only when an external clock is input. This is a measure to execute processing between clocks, as the OLED is busy updating. If the BPM is very slow, the display update frequency will also slow down, so be careful.
If the BPM is too fast (over about 200 BPM), the display update cannot keep up with the clock. This can be improved by using a faster microcontroller rather than the Arduino Nano, or an OLED that supports SPI. (There is almost no price difference between an I2C OLED and an SPI OLED)

About the presets
: Presets are expressed in hexadecimal alphabets and can be easily changed.
The source code presets are lame, as I made them up as a beginner at rhythm. Feel free to edit them.
The hexadecimal numbers in the table are broken down as follows:
{CH1,CH2,CH3,CH4,CH5,CH6,CH1,CH2,CH3,CH4,CH5,CH6}, with the first 1-6 being normal presets and the latter 7-12 being fill-in presets.
