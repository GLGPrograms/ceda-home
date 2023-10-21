# Power supply

On the **original** PSU, wire colors are:

| color  | power |
| ------ | ----- |
| black  | GND   |
| red    | 12V   |
| yellow | 5V    |
| purple | -12V  |

On the power connector on the motherboard there is a key between position 7 and 8, to avoid inserting the plug backwards.

| #    | 8   | EMPTY | 7   | 6    | 5    | 4   | 3   | 2   | 1   |
| ---- | --- | ----- | --- | ---- | ---- | --- | --- | --- | --- |
| type | NC  | -     | NC  | +12V | -12V | +5V | +5V | GND | GND |


<figure>
<img src="../assets/psu-connector.jpeg" width="400px" />
> A modern ATX PSU can be used to power the Sanco, just swap yellow and red wires, as shown in the picture.
</figure>

There is not any label that provides maximum current information.
All we can say is that -12V is limited by its linear regulator, which can provide up to 1.5A.

## Circuit analysis

> **Alert!**
> We do not have any official information regarding the power supply, all that follows is the result of empirical analysis.
> An unofficial schematic will be available soon.

![Block diagram of the ceda power supply](../assets/psu-block-diagram.png)

- The supply is designed to work both 220VAC and 100VAC line inputs, but a wire jumper must be manually switched between two positions.
There is a silk screen note that guides for the correct jumper positioning.
- A soft start circuit is implemented.
At the startup, the input current is limited by a power resistor.
When the downstream 5V reaches a targeted value, the resistor is shorted and the supply can deliver its full power.
In our supply, the soft start circuit was partially operating since the switch component was exploded.
- 5V is obtained using a [flyback converter](https://en.wikipedia.org/wiki/Flyback_converter).
The transformer primary is fed with the rectified line voltage.
The transformer primary switch is implemented with two power NPN transistors controlled by a PWM control IC.
The power transistors are controlled through two smaller transformers to ensure a full insulation.
The used PWM control IC is a IR9494 and it is powered through a +18V supply. This service supply, obtained through a common 7818 linear regulator, is for internal usage only and it is not carried outside.
The output voltage value can be fine tuned through VR2 potentiometer.
- 12V is obtained using a [buck converter](https://en.wikipedia.org/wiki/Buck_converter).
The buck input voltage come from another winding of the 5V flyback transformer.
The PWM control IC used for the buck switch is a MC1723CL (ceramic package).
The output voltage value can be fine tuned through VR1 potentiometer.
Since the converter input is bonded to 5V flyback regulator, a variation on VR2 slightly changes 12V too.
- -12V is obtained through a 7812 (sic!), which can provide up to 1.5A.
That's not a typo, it's really a 7812 instead of a 7912. The GND/VOUT couple is just swapped to fullfil the same purpose.
The 7812 input is another winding of the flyback transformer.
