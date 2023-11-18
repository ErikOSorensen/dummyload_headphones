# Dummy load for headphones

I have a plan to build a stellar headphone amplifier this winter (2023-24). For
testing it, it would be good to have a dummy load such that it can be tested
under load. Since the load of headphones varies more than speakers (from
impedances around $25\Omega$ to $600\Omega$), it would be good to have some sort
of switchable load. I have been looking into how people build such a load.

The most extreme load I've found on the internet is that of *Neurochrome*, an
industrial load made to directly interface to an Audio Precision analyzer
([Neurochrome's load](https://neurochrome.com/products/headphone-dummy-load)).
This has high precision power resistors but also a possibility to switch in
parallel capacitors to load the amps with 100pF to 10nF, switched in by relays
controlled by a microcontroller. The sense connections in his design is actually
Kelvin coupled -- which means that his design relies on a DB-9 connector to
connect to the amplifier, with special cables such that the voltage sense can be
connected at the amplifier output without having to account for loss in the
cables. 

There are two aspects of the *Neurochrome* dummy load that is more than I am
willing to spring for in my own design. I don't want to allow for "balanced"
connections to headphones, but will focus on the common ground referenced amps
and headphones. I will also aim do the output sense connections at the dummy
load side instead of at the amp output. 

What I do want to borrow is the idea of a micro controller to control selection
of the load. I would like to use a Raspberry pi pico to control a set of relays
to select resistive and capacitive loads. I don't have an audio
precision to interface with. But I have some experience getting
the Pi Pico to interface with a WIZnet w5500 ethernet adapter. 
I would like a REST interface such that the headphone dummy
load can be integrated into an LAN-connected lab context. The
load should be controllable either from the front panel with a rotary encoder or with REST calls to the Pi. This is not necessary under any reasonable interpretation of that term, but it would be fun.

## What values to load the amps with?

The Raspberry Pi Pico has 26 GPIO pins. Three of these would be necessary for
SPI connection to the w5500 ethernet adapter, two would be used for i2c
connection to a display, three would be needed to connect to a rotary encoder
with a switch. That still leaves 18 GPIO pins, more than plenty for driving a
number of relays. I could drive the relays with ULN2003LV chips, which
conveniently interfaces the 3V3 logical output of the Pi Pico with 5V signal
relays. Two such chips would be able to drive 14 relays, which is plenty (both
relays and precision resistors are expensive). 

If I have 5 resistor values (e.g. 25, 50, 100, 300, 600) I can easily cover with
sufficient support by possibly paralleling two of the resistors.  For capacitors, I aim to cover a larger
range of values, and I don't know how useful the paralleling would be. If I set aside a bank of relays for capacitance, I can easily cover the same
range as *Neurochrome* with values 100pF, 200pF, 470pF, 1nF, 2nF, 4.7nF, and 10nF. 

## Connections / interface

I would like:

- A power on/off on the front.
- A stereo 1/4 inch jack for amp input. 
- Two XLR sense outputs for connection to soundcard for analysis.
- Two BNC sense outputs (paralleled to the XLRs).
- USB in the back for power and programming access to the Raspberry Pi Pico.
- Ethernet jack in the back for remote control.
- Rotary encoder in front for manually choosing a load (push switch for choosing adjusting R or C).
- A 16x2 LCD display for a) printing ip-address at boot time and b) one line for chosen R, one line for chosen C (and for indicating if the rotary encoder would change R or C). 

