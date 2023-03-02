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

:warning: IDEA

According to the schematic of a similar model ([YD-380](https://retrocmp.de/fdd/yd/Y-E_Data_YD-380_Maint_Sep1983.pdf)), this resistor net may be mandatory only for one drive at a time.
Then, if only drive 1 is used, this component must be installed in the missing spot.

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
