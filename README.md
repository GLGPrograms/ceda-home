# Sanco 8003

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
  - [Power supply](#power-supply)
  - [Motherboard](#motherboard)
  - [ROM](#rom)
  - [Memory map](#memory-map)
  - [CRT Display](./pages/crt.md)
  - [Floppy drive](pages/floppy-drive.md)
  - [Keyboard](pages/keyboard.md)
  - [External resources](pages/external-resources.md)
- Other repositories:
  - [Emulator](https://git.giomba.it/giomba/ceda-emu): actually you don't have a Sanco, but you want to try it anyway on your modern PC.
  - [Keyboard protocol](https://github.com/RetroNewbie/Sanco_8000): you don't have a keyboard, but you want to build your one: now you can!
  - [System ROM disassembly](https://github.com/GLGPrograms/ceda-rom-disassembly): what is the I/O routine doing? How to read the keyboard directly? How are peripherals configured?
  - [Schematics (KiCAD)](https://github.com/GLGPrograms/ceda-schematics): your Sanco is not working and you don't know where to look. Start troubleshooting the known voltages and signals.
  - [VGA Adapter](https://git.giomba.it/giomba/ceda2vga): connect the motherboard of your huge Sanco to a small VGA monitor, to avoid it taking up all your desktop space!

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

## Power supply

On the **original** PSU, wire colors are:

- black -> GND
- red -> 12V
- yellow -> 5V
- purple -> -12V

On the power connector on the motherboard there is a key between position 7 and 8, to avoid inserting the plug backwards.

- 1 -> GND
- 2 -> GND
- 3 -> 5V
- 4 -> 5V
- 5 -> -12V
- 6 -> +12 V
- 7 -> NC
- EMPTY
- 8 -> NC

<figure>
<img src="./assets/psu-connector.jpeg" width="400px" />
<span>A modern ATX PSU can be used to power the Sanco, just swap yellow and red wires, as shown in the picture.</span>
</figure>


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

| base   | size   | description                               |
| ------ | ------ | ----------------------------------------- |
| 0x0000 | 0x2000 | Software ROM                              |
| 0x2000 |        |                                           |
| 0xC000 | 0x2000 | Software ROM                              |
| 0xD000 | 0x1000 | Video Frame Buffer (visible 2000 at time) |
| 0xE000 |        |                                           |

### I/O

| address | size  | device                       |
| ------- | ----- | ---------------------------- |
| 0x81    |       | GPIO - Bank switching?       |
| 0x82    |       | GPIO                         |
| 0x83    |       | GPIO                         |
| 0xA0    | 2     | CRTC internal registers      |
| 0xB0    | 4     | SIO/2 Serial Peripheral      |
| 0xC0    |       | FDC - floppy disk controller |
| 0xD6    |       |                              |
| 0xDA    | 1 (2) | Speaker                      |
| 0xDC    |       |                              |
| 0xDE    |       |                              |
| 0xE0    | 4     | CTC Timer                    |

## Contribute

We don't know much about this computer.
The Internet doesn't either.

Feel free to [contact us](https://retrofficina.glgprograms.it/doku.php?id=contatti), have a look at these [resources](#resources), submit a pull request or git send-email a patch!
