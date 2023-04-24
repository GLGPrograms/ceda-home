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

For each displayed character, a bit mask can be populated.

| bit | attribute           |
| --- | ------------------- |
| 0   | invert              |
| 1   | blink               |
| 2   |                     |
| 3   | stretch             |
| 4   | underline           |
| 5   | blink and underline |
| 6   | hide (?)            |
| 7   |                     |

### Stretch
Stretch attribute makes each character 2x wider.

This is accomplished commanding the _chip enable_ pin of the pixel shift register IC at half the pixel clock.

Thus, in stretch mode, characters in even columns always overwrite the character in the corresponding odd columns to their right.
