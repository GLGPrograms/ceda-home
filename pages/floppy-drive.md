# Floppy drive

CEDA uses two 5¼" floppy drive unit.
Both drives are manufactured by YE-DATA, model YD-480.

Each drive has few jumpers to configure its behavior, as reported in following table

| Jumper name | Jumper behavior                            | is shorted?                                    |
| ----------- | ------------------------------------------ | ---------------------------------------------- |
| DS0 - DS3   | Drive select as drive 0 - 3                | DS0 shorted on drive 0, DS1 shorted on drive 1 |
| HS          | Drive enabled by the "drive select" signal | shorted                                        |
| HM          | Drive enabled by the "motor on" signal     | not shorted                                    |
| MX          | DS is ignored, drive is always selected    | not shorted                                    |

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

- https://www.dosdays.co.uk/topics/floppy_drives.php
