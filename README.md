# DTTS V2

This repo houses the hardware design and associated documentation for the SUAS Dynamic Thrust Test Stand(DTTS) V2.
The new board is designed to be more intuitive to use and more robust against failures like over voltage, shorts, etc.
The V2 board also integrates all sensors and ICs in a single solution.


## System Diagram
```mermaid
graph LR;
LC[<b>Load Cell</b></br>Uses a Wheatstone Bridge to provide a differential voltage signal.];
CS[<b>Current Sensor</b></br>Current sensing is implemented with a shunt resistor and differential amp.];
VS[<b>Voltage Sensor</b></br>Voltage sensing circuit is implemented with a digi-pot and supports auto-trimming.];
MC[<b>ESP32 S3 WROOM 1</b></br>Controls esc for the motor, collects measurements, and communcates them over WiFi.];

GUI[<b>Front End UI</b></br>A front end GUI collects measurements and makes user interaction simple.];

LC --> MC;
CS --> MC;
VS --> MC;

GUI <== WiFi ==> MC;
```

## Working Specs
- Battery Voltage $\leq$ 50 V.
- Max ESC Current $\leq$ 90 A.
- Using 6 AWG wire for battery.
- Must at minimum measure battery voltage, batter to ESC current, and Motor Thrust
- All circuitry must run off of a single battery -- the one plugged into the ESC
- Preferable to have auto-trimming and gain calibration on voltage sense circuity.

## Component Selection (Draft!)

### Power Supply
There are some different approaches to this. If we were using a seperate battery for this, then we could have just used a linear regulator, but if we want to use the motor battery, this voltage may range from 12 to 60 V. So we probably need to use a buck converter. We could also use a hybrid design (i.e. 12-60V input down to 4.5V with a buck converter and 4.5V to 3.3V with an LDO). This may complicate things a little, but it's a very good choice in terms of noise and is also fairly efficient.

1. [Diodes Incorporated AP66200](https://www.diodes.com/assets/Datasheets/AP66200.pdf) Buck controller.
2. [TI LMR51625](https://www.ti.com/lit/ds/symlink/lmr51625.pdf?ts=1742888310253&ref_url=https%253A%252F%252Fwww.ti.com%252Fsitesearch%252Fen-us%252Fdocs%252Funiversalsearch.tsp%253FlangPref%253Den-US%2526nr%253D3%2526searchTerm%253DLMR51625XDDCR) Buck Controller (preferred).
  a. Also, if using TI ICs for power conversion, check out [Webench Designer](https://webench.ti.com/power-designer/)

1. [Toshiba CUS10S30 Schottkey Diodes](https://toshiba.semicon-storage.com/info/CUS10S30_datasheet_en_20140407.pdf?did=14077&prodName=CUS10S30) for signal over-voltage protection.\
  a. [Rohm RB520CM-60 Schottkey Diode](https://fscdn.rohm.com/en/products/databook/datasheet/discrete/diode/schottky_barrier/rb520cm-60t2r-e.pdf) Alternative to (1) with higher reverse voltage
