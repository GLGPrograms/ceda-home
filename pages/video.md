# Display

Sanco's display is driven by the CRTC, a common catode ray tube controller for Z80-based computers.
See [CRT](crt.md) for a detailed hardware description.

From software's point of view, Sanco's video memory is divided in two banked segments.

- bank 7 out/disabled: character video memory
- bank 7 in/enabled: attribute video memory

Bank 7's name comes from the corresponding GPIO bit that is set/reset in order to perform the bank switch.

At boot, bank 7 is disabled (eg. character video memory is enabled).

## Character video memory

Character video memory is located at 0xD000 and is 0x1000 bytes long (4096).

It contains the ASCII representation of the actual characters to be displayed.
So, if location 0xD000 contains 0x41, then, an 'A' will be displayed at the top left of the display.

Only 80x25 (= 2000) bytes are visibile in a frame.

At reset, CRTC base address points at 0xD000.

When using the display print routines (print hex, print string, ...), every time that the visible video buffer is finished, data is still appended in the video memory, and then the CRTC base pointer is updated accordingly, to exploit hardware accelerated scrolling.

Since 4096 is not an integer multiple of 2000, CRCT base pointer is continuosly shifted to point to the correct memory location.

When CRTC reads a frame buffer from video memory, it automatically wraps around from 0xDFFF to 0xD000. This also should be taken into account.

This allows easy hardware accelerated scrolling back and forth.

This is similar to the way the CRTC works in many other Z80 based computers too (AFAIK).

## Attribute video memory

Attribute video memory is located at 0xD000 and is 0x1000 bytes long (4096).

It contains the flag attributes for each correspondent displayed character in the character video memory.

The address space of this memory corresponds to the address space of the character video memory.

In order to enable access to attribute video memory, bank 7 should be enabled by setting bit 7 of PORTB in uPD8255.

For each displayed character, an attribute octet can be populated.

Attribute octet:
```
+---+---+---+---+---+---+---+---+
| 7 |   index   | 3 | 2 | 1 | 0 |
+---+---+---+---+---+---+---+---+
```

| bit | attribute          | description      |
| --- | ------------------ | ---------------- |
| 0   | invert             |                  |
| 1   | blink              |                  |
| 2   | cursor?            | TODO             |
| 3   | horizontal stretch |                  |
| 4-6 | attribute index    | (see below)      |
| 7   | custom attribute   | to EXT connector |

Attribute index (attribute octet, bits [6:4]):

| value | description                                                  |
| ----- | ------------------------------------------------------------ |
| `000` | plain / no attribute                                         |
| `001` | underline (13-th segment of the gliph)                       |
| `010` | blinking underline                                           |
| `011` | overline                                                     |
| `100` | hide                                                         |
| `101` | underline and overline (0-th and 13-th segment)              |
| `110` | vertical stretch (upper half, segment 0-6, don't show 7)     |
| `111` | vertical stretch (lower half, segment 7-13, don't show rest) |

Attribute index is used to index the 28L22 ROM in J12, which contains driving signals for the blinking, underlining and show hardware circuit.

(Not clear what CRTC RA4 does, yet: I expect it to always be 0)

### Horizontal stretch
Horizontal stretch attribute makes each character 2x wider.

This is accomplished commanding the _chip enable_ pin of the pixel shift register at half the pixel clock (74LS166 in J14).

Thus, in horizontal stretch mode, characters in even columns always overwrite the character in the corresponding odd columns to their right.

### Vertical stretch
Vertical stretch makes each character (almost) 2x taller.

This is accomplished indexing the char ROM (2732) via the glue logic ROM (28L22): for each char row address R, the glue logic passes R/2 to the char ROM.

In order to display the full character, it has to be placed twice, at the same column but in two different (adjacent) rows on the character video RAM matrix.
Then, in the attribute video RAM matrix, 0x60 must be written for the upper half, and 0x70 for the lower half.

For unclear reasons, the rows 14-15 of each char are discarded.
