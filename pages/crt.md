# CRT Display

CRT display is controlled by the CRTC integrated circuit.
All signals are digital TTL levels (0-5V), which are then interpreted by the CRT HV driver board.

<figure>
<img src="../assets/incrt.png" width="200" />
<span>Display connector. Computer's monitor is connected here, and is only accessible from inside the computer.</span>
</figure>

## Display port connector (INCRT) pinout

| #  | signal   | color  |
| -- | ------   | -----  |
|  1 | NC       |        |
|  2 | GND      | black  |
|  3 | NC       |        |
|  4 | PIXELS   | brown  |
|  5 | NC       |        |
|  6 | NC       |        |
|  7 | VSYNC    | violet |
|  8 | HSYNC    | gray   |
|  9 | NC       |        |
| 10 | GND      | black  |
| 11 | CONTRAST | green  |
| 12 | CONTRAST | blue   |

## Video signal

| signal          | value         |
| --------------- | ------------- |
| Vertical Sync   | ~20 ms        |
| Horizontal Sync | 53 us         |
| Lines           | 370 lines     |
| Pixel Clock     | 15MHz (XTAL3) |

These signals are not compatible with a common PAL TV-set monitor, neither the voltages nor the timings.
If you want to use an external monitor, some circuit with frame buffering capabilities is needed. See below.

<figure>
<img src="../assets/vsync-1.png" width="400" />
<span>A detail of vertical sync pulse.</span>
</figure>

- [VSYNC](../assets/vsync-0.png)
- [VSYNC detail](../assets/vsync-1.png)
- [HSYNC](../assets/hsync-0.png)
- [HSYNC detail](../assets/hsync-1.png)
- [Back Porch](../assets/backporch.png)

## Related projects

- [ceda2vga](https://git.giomba.it/giomba/ceda2vga): connect the motherboard of your huge Sanco to a small VGA monitor, to avoid it taking up all your desktop space.
