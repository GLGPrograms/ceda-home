# Sanco 8003

![Our Sanco 8003 after restoration, running our demo program](./assets/ceda-better.jpg)

[Sanco 8003](https://www.old-computers.com/museum/computer.asp?st=1&c=1069) is a Z80 based French personal computer, from year 1982.

Aim of this page is to collect as much information as possible about this computer, and to become a centralized reference hub for this computer.

Project codename is *ceda* (see below).

:warning: We make our best effort to document what we find, but everything that you read here must be taken with extreme care! You may harm your computer or harm yourself if you are not aware of what you are doing, or if there are some errors in this document. :warning:

## License

The documentation in this repository is release under the terms of Creative Commons 4.0 Attribution (SPDX: CC-BY-4.0).
We are open to external contributions. If you contribute, you implicitly release your work under the mentioned license. Your authorship will be guaranteed by git.

## Resources

A collection of resources we managed to put together.

:warning: Please note that this project is currently work-in-progress and majority of the following resources are not complete (or at their infancy stage).

- In this documentation repository:
  - [Power supply](pages/psu.md)
  - [Motherboard](#motherboard)
  - [ROM](#rom)
  - [Memory map](#memory-map)
  - [CRT Display](./pages/crt.md)
  - [Video display](./pages/video.md)
  - [Floppy drive](pages/floppy-drive.md)
  - [Keyboard](pages/keyboard.md)
  - [External resources](pages/external-resources.md)
- Other repositories:
  - [ceda-cemu](https://github.com/GLGPrograms/ceda-cemu): actually you don't have a Sanco, but you want to try it anyway on your modern PC with an emulator.
  - Hardware adapters:
    - [ceda2vga](https://git.giomba.it/giomba/ceda2vga): connect the motherboard of your huge Sanco to a small VGA monitor, to avoid it taking up all your desktop space!
    - [Keyboard protocol](https://github.com/RetroNewbie/Sanco_8000): you don't have a keyboard, but you want to build your one: now you can!
    - [ceda-ps2-keyboard](https://github.com/GLGPrograms/ceda-ps2-keyboard/): you don't have a keyboard, and don't want to build your own adapter: now you can use ours!
  - Utilities:
    - [ceda-utils](https://github.com/GLGPrograms/ceda-utils): you have a Sanco, but no software disk? Or you need some basic util? Have a look here.
    - [RCX62/TRX62](https://github.com/RetroNewbie/Sanco-8000-serial): you have a Sanco with a CP/M disk, but no other software? Transfer it with this Python script.
  - Documentation
    - [System ROM disassembly](https://github.com/GLGPrograms/ceda-rom-disassembly): what is the I/O routine doing? How to read the keyboard directly? How are peripherals configured?
    - [Schematics (KiCAD)](https://github.com/GLGPrograms/ceda-schematics): your Sanco is not working and you don't know where to look. Start troubleshooting the known voltages and signals.
    - [CP/M disk](https://github.com/GLGPrograms/ceda-cpm): learn what's inside the operating system disk, or create a custom copy.
  - Mods
    - [CGE](https://github.com/GLGPrograms/ceda-img): character generator extended ROM: ASCII chars on a green phosphor CRT are fun, but intermixed text and images is better!

## How it started

We got one in working conditions, but we have no operating system disk, no keyboard, and we known nothing about this computer: it just turns on and shows *V1.01* on its CRT display.

<figure>
<img src="./assets/ceda.jpg" width="400" />
<span>Our Sanco 8003 at its first power on</span>
</figure>

We gave it the codename CEDA, as our device came with a shiny proud sticker saying "CEDA", which probably just stands for *Centro Elaborazione Dati* (Datacenter, in Italian).

## Components

Central unit is composed by the following components.

- power supply
- sound speaker
- floppy disk drive (x2)
- green CRT display
- CRT driver board
- computer motherboard
- peripheral connectors daughterboard

External connectors:

- standard C13/C14 power connector
- 8pin DIN for keyboard
- DB25 communication port (supposedly a serial port)
- DB25 printer port (supposedly a standard parallel port)
- 5pin DIN PRNT port

## Motherboard

<figure>
<img src="./assets/sanco-mobo-picture.jpeg" width="200px" />
<span>Motherboard of the Sanco 8003</span>
</figure>

Motherboard is organized as a grid, whose rows are identified by letters, and columns by numbers.
In each cell of the grid, an IC can be located (sometimes one IC spawns more cells, sometimes cells only have passive or analog components).

Note that row identification letters don't follow strict alphabetical order.

| column 1      | column 2                               |
| ------------- | -------------------------------------- |
| motherboard   | Logic Systems Int'l inc. FPX-290       |
| daughterboard | Logic Systems Int'l inc. FPX-290-CN-01 |

Some documentation recovered from direct inspection:

- Motherboard and daughterboard ICs and their locations:
[PDF](./assets/sanco-mobo.pdf)

### Schematics

- [Motherboard](https://github.com/GLGPrograms/ceda-schematics) (work in progress :hourglass:)

### Default settings

As we found on our working computer.

| Location | Type         | Name      | Description  | Default value                |
| -------- | ------------ | --------- | ------------ | ---------------------------- |
| A12      | DIP switch   |           |              | 0x8F (bit 12345678)          |
| B15      | jumper       | KB SEL    |              | shorted on PCB               |
| C13      | jumper (3x2) | SIO clock | serial clock | [PNG](./assets/sioclock.png) |

## ROM

| Name          | Version | Type     | Position    | Size      | MD5 hash                         | Binary                         |
| ------------- | ------- | -------- | ----------- | --------- | -------------------------------- | ------------------------------ |
| Software ROM  | V1.01   | 2764     | H4-H5-H6    | 8kBytes   | f33347faf867be9eea28d928c7341f0e | [BIN](./assets/v1.01_rom.bin)  |
| Character ROM | CGV7.2  | 2732     | H12-H13-H14 | 4kBytes   | 0bf03ecb77e3c6d220a354fe2c95fccd | [BIN](./assets/cgv7.2_rom.bin) |
| Glue ROM      |         | TBP28L22 | J12         | 256 bytes | 69cf560a785ba0cd01427626949cc0a0 | [BIN](./assets/28l22.bin)      |

### BIOS ROM decoding

- [BIOS ROM Disassembly](https://github.com/GLGPrograms/ceda-rom-disassembly) (work in progress :hourglass:)

### Character set ROM decoding

Each character is 8x16 pixels. There are even placeholders for ASCII control codes. :open_mouth:

<figure>
<img src="./assets/char-bitmap.png" width="200" >
<span>Format of one character in the ROM.</span>
</figure>

<figure>
<img src="./assets/character-rom-image.png" width="200" >
<span>Full decode of CGV character ROM.</span>
</figure>

### Glue ROM decoding

We suspect that this ROM is used as glue logic to mask or switch certain parts of the address space.

- bit 0-3 -> bit 0-3 char ROM
- bit 4-6 -> TODO
- bit 7 -> char ROM Chip Enable (CE)

## Memory map

### Memory space

| base   | size   | B0 = 0, B7 = 0                            | B0 = 0, B7 = 1                   | B0 = 1      |
| ------ | ------ | ----------------------------------------- | -------------------------------- | ----------- |
| 0x0000 | 0xB000 | dynamic RAM                               | dynamic RAM                      | dynamic RAM |
| 0xB000 | 0x1000 | Alternate RAM chip (J4-J5-J6)             | Alternate RAM chip (J4-J5-J6)    | dynamic RAM |
| 0xC000 | 0x1000 | Software ROM                              | Software ROM                     | dynamic RAM |
| 0xD000 | 0x0800 | Video Frame Buffer (visible 2000 at time) | Video Attributes Buffer          | dynamic RAM |
| 0xD800 | 0x0800 | Video Frame Buffer (mirror)               | Video Attributes Buffer (mirror) | dynamic RAM |
| 0xE000 | 0x1000 | dynamic RAM                               | dynamic RAM                      | dynamic RAM |

The system implements a bank switching system to be able to fully access both the whole 64kB dynamic RAM space and the other memory ICs.
The bank switching is handled through port B of `uPD8255`:

- Through B0 we can choose to access the whole address space to dynamic RAM (1) or to map some spots to the other ICs (0), see the table above,
- Through B7 we can choose if video memory should be mapped to video frame buffer (0) or video attributes buffer (1).

### I/O

| address | size  | device                       |
| ------- | ----- | ---------------------------- |
| 0x80    | 4     | uPD8255 (PIO/GPIO)           |
| 0xA0    | 2     | CRTC internal registers      |
| 0xB0    | 4     | SIO/2 Serial Peripheral      |
| 0xC0    |       | FDC - floppy disk controller |
| 0xD6    |       |                              |
| 0xDA    | 1 (2) | Speaker                      |
| 0xDC    |       |                              |
| 0xDE    |       |                              |
| 0xE0    | 4     | CTC Timer                    |

## Boot sequence

At boot, Z80 executes the instruction located at memory address `0x0`.

There is a circuit that forces the BIOS ROM to be _chip enabled_ until the I/O address space is accessed for the first time (see schematics), so that the instructions sequence is the following:

Since ROM is mapped in `0xC000`-`0xCFFF` address space, an appropriate circuit must keeps the ROM addressed during boot phase.
A simpled form of this circuit is represented in circuit below.

![Simplified ROM CS](assets/simplified-rom-cs.png)

At startup or whenever the system is resetted (`RESET`), an SR latch keeps ROM enabled until an IO access is done (`IO_REQ`).
Looking at the code, we can see that the first performed instruction is a `jp $c030`, which moves the execution inside the ROM address space.
Then, the first IO write (`out`) is done to program the `uPD8255` IO interface:

```
jp      $c030       ;[0000] c3 30 c0
ld      a,$89       ;[c030] 3e 89
out     ($83),a     ;[c032] d3 83
```

From that line, BIOS ROM cannot be addressed at `$0000` anymore and only answers at `$C000`.

Please note that actual circuit works in inverted form, refer to the schematics for more details (see 74LS00 in L8, 74LS08 in L3 and 74LS138 in L2).

## Contribute

We don't know much about this computer.
The Internet doesn't either.

Feel free to [contact us](https://retrofficina.glgprograms.it/doku.php?id=contatti), have a look at these [resources](#resources), submit a pull request or git send-email a patch!
