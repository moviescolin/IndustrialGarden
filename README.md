# Industrial Garden
An electroacoustic instrument with analog circuitry and digital signal processing via Teensy 4.1 and Audio Shield.
Inspired by Folktek's Luminist Garden and subsequent variations, this code is used on a similar-in-concept project.
# Hardware Elements
A wooden enclosure has two piezo discs mounted inside, in varying diameters, which output signal through an analog path before encountering the Teensy. Guitar strings are affixed in two bundles to a grounding bar with allows a unqiue sound to be picked up by the piezos as well.
The analog circuitry is an operational amplifier buffer, provided by the MCP6002 IC, and a FET-based boost to increase the signal strength. This circuit goes into the Teensy for processing, and then out through the second channel of the MCP6002 as an output buffer.
This project utilizes the Teensy 4.1 microcontroller board as well as the Teensy 4.x REV-D Audio Shield for interfacing and processing.
# Software Elements
The Teensy provides an additional boost, noise filtering, a variable low-pass filter (Filter control), a digital delay with a feedback loop ranging from 0 to 2.5 seconds (Rate and F.Back controls), a thick reverb with wet/dry mixing (Reverb control), tap-tempo and mute functions, and CV inputs: 0V to 8V for Gate/Trigger (Rate input) and -5V to +5V for Reverb and Filter (CV1 and CV2 inputs).
# Open-Ended
The project and code are public and the Teensy boards are left unlocked with the idea that if the end user wishes, they can modify or change the code and fine-tune the instrument to their own tastes, including wholly replacing the digital audio effects.
# Warranty
The Industrial Garden is left open-ended for user-modifying, and is thus not covered by any warranty and given AS-IS.
