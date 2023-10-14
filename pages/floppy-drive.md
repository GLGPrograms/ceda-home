# Floppy drive

CEDA uses two 5¼" floppy drive unit.
Both drives are manufactured by YE-DATA, model YD-480.

Floppy drives are both controlled by a uPD765A Single/Double Density Floppy-Disk Controller, which can handle up to 4 drives.
The ROM code seems able to address all 4 drives, although not physically present.

## Drive board

<figure>
<img src="../assets/floppy-drive-board.jpg" width="400" />
</figure>

Each drive has few jumpers to configure its behavior, as reported in following table

| Jumper name | Jumper behavior                            | is shorted?                                    |
| ----------- | ------------------------------------------ | ---------------------------------------------- |
| DS0 - DS3   | Drive select as drive 0 - 3                | DS0 shorted on drive 0, DS1 shorted on drive 1 |
| HS          | Drive enabled by the "drive select" signal | shorted                                        |
| HM          | Drive enabled by the "motor on" signal     | not shorted                                    |
| MX          | DS is ignored, drive is always selected    | not shorted                                    |

Drive 1 has a missing resistor net in socket MT (component value on drive 0: `760-3-R150`).

> **Warning** IDEA
> According to the schematic of a similar model ([YD-380](https://retrocmp.de/fdd/yd/Y-E_Data_YD-380_Maint_Sep1983.pdf)), this resistor net may be mandatory only for one drive at a time.
> Then, if only drive 1 is used, this component must be installed in the missing spot.

## Accessing by software

> **Note**
> Motor control:
> I'm quite sure that drive motor can be manually turned on and off, since I accidentally did this.
> But the correct procedure is not yet documented nor even understood.

BIOS ROM provides an one-man-band routine to interface with the floppy drives.
Routine is located at address `fdc_rwfs = $c19d`.
The name was given by us during reverse engineering process and was inspired by Apple's RWTS routine, which has a similar behavior.

`fdc_rwfs` routine performs different operations depending on the arguments, passed from a, bc, de, hl registers.
The routine changes the content of three memory blocks.

First block is the **descriptor of operation** and has a fixed location at `$ffb7` and size of 9 bytes.
This area is mostly populated by the data passed as argument (arg reg column).

| Arg reg | Offset | Bit   | Description                                                                              |
| ------- | ------ | ----- | ---------------------------------------------------------------------------------------- |
| -       | 0      | [3:0] | Bitfield of already accessed drives. Initialized by BIOS to 0                            |
| a       | 1      | [1:0] | `SSF`: Sector size factor. See [below](#bytes-per-sector) for conversion to actual bytes |
| c       | 2      | [2]   | Head number, i.e. which side of a double side floppy must be used                        |
| c       | 2      | [1:0] | Addressed drive (0 to 3)                                                                 |
| b       | 3      | [7:4] | [Operation command](#operation-command): read, write, seek, format, recalibrate          |
| b       | 3      | [3:0] | `SB`: sector burst. See [below](#bytes-per-sector)                                       |
| e       | 4      | [7]   | `SBE`: sector burst enable, see [below](#bytes-per-sector)                               |
| e       | 4      | [6:0] | Sector (record) number, 0 to 25                                                          |
| d       | 5      | -     | Track (cylinder) number, 0 to 76                                                         |
| hl      | 6-7    | -     | Address of the data buffer for read/write/format operations                              |
| -       | 8      | -     | Number of retry for read/write/format operations                                         |

Second block is the **data buffer**, which will contain the actual data read or written from the floppy.
A particular usage is made when format operation is started.
In this case, the data buffer must contain a list of *id fields*, which describe the physical sector order upon a track.

A single **id field** is 4 byte long:

| Offset | Description                |
| ------ | -------------------------- |
| 0      | track number, 0 to 76      |
| 1      | side number, 0 to 1        |
| 2      | sector number, **1 to 26** |
| 3      | sector size factor         |

Last block is a **return block**, which has a fixed location at `$ffc0` and size of 7 bytes.
It contains the return value from `fdc_rwfs` routine.

| Offset | Description               |
| ------ | ------------------------- |
| 0      | Status Register 0         |
| 1      | Status Register 1         |
| 2      | Status Register 2         |
| 3      | Accessed track number     |
| 4      | Accessed head (i.e. side) |
| 5      | Accessed sector           |
| 6      | Number of bytes moved     |

See the datasheet for explanation of Status Register content.

### Operation command

Floppy drive controller can handle a wide set of operations, described in its datasheet.
But only a few of them are actually exposed:

| Code | Operation                          |
| ---- | ---------------------------------- |
| 0    | (recalibrate) move head to track 0 |
| 4    | read sector                        |
| 8    | write sector                       |
| 2    | seek (move to track)               |
| f    | format track                       |

### Bytes per sector

The only value written to the FDC chip is the `SSF` value, and that's enough for him.
To convert `SSF` in sector size and viceversa, refer to the following equation:

$$ bps = 0x80 << SSF $$

Hence, each sector may be 128, 256, 512 or 1024 bytes long.
`fdc_rwfs` routine allows to read multiple sectors at once by using `SB` and `SBE` parameters.
Then, if `SBE = 1`, the actual number of bytes read will be:

| SSF | sector size | bytes read       |
| --- | ----------- | ---------------- |
| 0   | 128         | 128 + 256 * SB   |
| 1   | 256         | 256 + 256 * SB   |
| 2   | 512         | 256 + 256 * SB   |
| 3   | 1024        | 1024 + 1024 * SB |

> **Note**
> Hypothesis:
> To operate with sector size = 512, SB must be at least 1.

## Usage with x86 PC

One of the drive was tested successfully with a generic x86 computer.
The drive communicates with the PC with a standard 34-pin edge connector.
Drive is powered through a standard 4-pin molex connector.

Drive was tested as a 5¼ 360KB drive in unit A. Hence, it must be connected after the ribbon cable twist.

<figure>
<img src="../assets/floppy-drive-ribbon.jpg" width="400" />
</figure>

A change in the jumper must be done:

- in DS 0-3 group, only DS 1 must be shorted;
- in HS-HM group, only HM must be shorted;
- MX is unchanged.

<figure>
<img src="../assets/floppy-drive-jumpers.jpg" width="400" />
</figure>

Some tests were done to access drive as B drive, without success.

## References

- [Floppy drives information](https://www.dosdays.co.uk/topics/floppy_drives.php), YD-480 is mentioned;
- [Controller information](https://www.cpcwiki.eu/index.php/765_FDC).
