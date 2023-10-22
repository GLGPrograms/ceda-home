# CRT Display

CRT display is controlled by the CRTC integrated circuit.
All signals are digital TTL levels (0-5V), which are then interpreted by the CRT HV driver board.

<figure>
<img src="../assets/incrt.png" width="200" />
<span>Display connector. Computer's monitor is connected here, and is only accessible from inside the computer.</span>
</figure>

## Display port connector (INCRT) pinout

| #   | signal   | color  |
| --- | -------- | ------ |
| 1   | NC       |        |
| 2   | GND      | black  |
| 3   | NC       |        |
| 4   | PIXELS   | brown  |
| 5   | NC       |        |
| 6   | NC       |        |
| 7   | VSYNC    | violet |
| 8   | HSYNC    | gray   |
| 9   | NC       |        |
| 10  | GND      | black  |
| 11  | CONTRAST | green  |
| 12  | CONTRAST | blue   |

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

## Memory access
Being the real-time chip which handles the image on the screen, the CRTC has higher priority in accessing the video RAM, and can temporarily halt the Z80 CPU when they both try to access it.

Example performance test code:
```asm
_performance_test:
    in  a,($A0)             ; toggle CRTC /CS
                            ; used just to measure the spike
                            ; and determine the cycle loop
                            ; with an oscilloscope

    ld  de,$0100            ; repeat cycle 256 times
loop:
    ld  a,($2000)           ; read from normal memory or video memory
                            ; takes 13 cycles for normal memory
                            ; takes ~15 cycles for video memory

    dec de                  ; 6
    ld  a,d                 ; 4
    or  e                   ; 4
    jp  nz,loop             ; 10

    jp  _performance_test
```

Given the Z80 running at 4MHz:
- loop takes 2.37 ms when reading from `$2000` (a normal address in memory).
- loop takes 2.50 ms when reading from `$d000` (an address in video memory).

## Related projects

- [ceda2vga](https://git.giomba.it/giomba/ceda2vga): connect the motherboard of your huge Sanco to a small VGA monitor, to avoid it taking up all your desktop space.
