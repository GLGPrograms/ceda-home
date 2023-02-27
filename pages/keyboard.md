# Keyboard

<figure>
<img src="../assets/keyboard-whole.jpg" >
</figure>


The Sanco uses a 98 keys keyboard connected through an 8 pin DIN connector. 

- [Protocol](#protocol)
- [Connector](#connector)
- [Key map](#key-map)
- [Images](#images)


## Protocol

The keyboard comunicate with the computer using UART with TTL logic levels (5v/0v) with a 1200 8N1 configuration.

:warning: In this document all the values will be interpreted as having the least significant byte first. :warning:

### Keyboard -> Computer

<figure>
<img src="../assets/keyboard-keypress-signal-example.png">
</figure>

Everytime a key is pressed two bytes of data are sent to the computer. The first byte represents the key that was pressed. The second byte contains flags for CTRL,ALT,CAPS and SHIFT.
The flag byte is sent ~2ms after the key byte (assuming 1 stop bits). 

#### Key byte

For a complete mapping of the keys and the data that is sent check [here](#key-map).

#### Flag Byte 

The four most significant bits of the flag byte are always the same (0011). The other four bits are used as flags:

|b0|b1|b2|b3|b4|b5|b6|b7|
|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
S|C|A|R|0|0|1|1|

| Flag | Meaning |
|:-:|:-:
S| SHIFT 
C| CAPS 
A| ALT 
R| CTRL 

When the corresponding flag is set to 1 it's considered active

### Computer -> Keyboard

<figure>
<img src="../assets/keyboard-boot-led-signal.png" >
</figure>

The keyboard also receives data from the computer. The data consist of a single byte. 

The only signal that is documented at the moment is the byte (0x12) that is sent after  reset/boot to signal to the keyboard that the light of the BOOT key should light up.

## Connector

The keyboard uses an 8pin DIN connector with the following configuration :

<figure>
<img src="../assets/din8-pinout.png" width=250>
</figure>

> The cable field refers to the cable that connects to the pcb 

Pinout of the **male** connector:
| Pin | Cable | Usage |
|:---:|:-----:|:-----:|
| 3   | Green | Rx/Tx |
| 6   | Red   | Vcc(5V) |
| 8   | White,Black | GND

The other pins are unused.

## Key map

Here they keys are mapped with the corresponding byte that is sent by the keyboard. The keys are listed following the real layout of the keyboard.When the same key is repeated twice on the keypad the keypad one is referred to as {key}_K. 

Also note that some keys are repeated to represent bigger keys (see [here](#images) for reference).

|   |c0|c1|c2|c3|c4|c5|c6|c7|c8|c9|c10|c11|c12|c13|c14|c15|c16|c17|c18|c19|
|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
|r0|BOOT  ```4D``` | F1<br />  ```4E``` | F2  ```4F``` | F3  ```50``` | F4  ```51``` | F5  ```52``` | F6  ```53``` | F7  ```54``` | F8  ```55``` | F9  ```56``` | F10  ```57``` | F11  ```58``` | F12  ```59``` | F13<br /> ```5A``` | F14<br />  ```5B``` | F15<br /> ```5C``` | UP ```3A``` | DOWN ```3B``` | LEFT ```3C``` | RIGHT ```3D``` |
|r1|ESC ```01``` | <<br /> ```02``` | 1<br /> ```03``` | 2 ```04``` | 3 ```05``` | 4 ```06``` | 5<br /> ```07``` | 6 ```08``` | 7 ```09``` | 8 ```0A``` | 9 ```0B``` | 0 ```0C``` | \#<br /> ```0D``` | @<br /> ```0E``` | DEL<br /> ```0F``` | ^<br /> ```10``` | 7_K ```3E``` | 8_K ```3F``` |  9_K ```40``` | CE ```41``` |
|r2|BREAK ```11``` | TAB ```12``` | A ```13``` | Z ```14``` | E ```15``` | R ```16``` | T<br /> ```17``` | Y ```18``` | U ```19``` | I ```1A``` | O ```1B``` | P ```1C``` | \><br /> ```1D``` | . .<br /> ```1E``` |RETURN ```2B``` | }<br /> ```1F``` | 4_K ```42``` | 5_K ```43``` | 6_K ```44``` | -<br /> ```45``` |
r3|CAPS-LOCK ```60``` | SHIFT-LOCK ```65``` | Q ```20``` | S ```21``` | D ```22``` | F ```23``` | G<br /> ```24``` | H ```25``` | J ```26``` | K ```27``` | L ```28``` | M ```29``` | %<br /> ```2A``` | RETURN ```2B``` |RETURN ```2B``` | {<br /> ```2C``` | 1_K<br /> ```46``` | 2_K ```47``` | 3_K<br /> ```48``` | ENTER ```4C``` |
r4|CAN ```2D``` | LEFT-SHIFT ```63``` | LEFT-SHIFT ```63``` | W ```2E``` | X ```2F``` | C ```30``` | V<br /> ```31``` | B<br /> ```32``` | N ```33``` | / ```34``` | . ```35``` | \- ```36``` | \+ ```37``` | RIGHT-SHIFT ```64``` | RIGHT-SHIFT ```64``` | LINE FEED ```38``` || ._K ```49``` | 0_K ```4A``` | 00 ```4B``` | ENTER ```4C``` |
r5||CTRL ```61``` ||||| SPACE ```39``` ||||| ALT ```62``` |



## Images

<figure>
<img src="../assets/keyboard-whole.jpg">
</figure>


<figure>
<img src="../assets/keyboard-pcb-back.jpg">
</figure>

<figure>
<img src="../assets/keyboard-pcb-overview.jpg">
</figure>

<figure>
<img src="../assets/keyboard-pcb-overview-one.jpg">
</figure>

<figure>
<img src="../assets/keyboard-pcb-overview-two.jpg">
</figure>

<figure>
<img src="../assets/keyboard-pcb-overview-three.jpg">
</figure>

<figure>
<img src="../assets/keyboard-pcb-overview-four.jpg">
</figure>