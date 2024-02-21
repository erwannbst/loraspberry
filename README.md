# Overview

This is a python interface to the [Semtech SX1276/7/8/9](http://www.semtech.com/wireless-rf/rf-transceivers/) 
long range, low power transceiver family.

This project is forked from [https://github.com/mayeranalytics/pySX127x](https://github.com/mayeranalytics/pySX127x)


# Hardware

The transceiver module is a SX1276 based Modtronix [inAir9B](https://web.archive.org/web/20200926024317/https://modtronix.com/inair9.html). 
Pinout on a Raspberry 3 Model B

| Proto board pin | RaspPi GPIO | Direction |
|:----------------|:-----------:|:---------:|
| inAir9B DIO0    | GPIO 22     |    IN     |
| inAir9B DIO1    | GPIO 23     |    IN     |
| inAir9B DIO2    | GPIO 24     |    IN     |
| inAir9B DIO3    | GPIO 25     |    IN     |
| inAir9b Reset   | GPIO ?      |    OUT    |
| LED             | GPIO 18     |    OUT    |
| Switch          | GPIO 4      |    IN     |


# Installation

Make sure SPI is activated on you RaspberryPi: [SPI](https://www.raspberrypi.org/documentation/hardware/raspberrypi/spi/README.md)
**pySX127x** requires these two python packages:
* [RPi.GPIO](https://pypi.python.org/pypi/RPi.GPIO") for accessing the GPIOs, it should be already installed on
  a standard Raspian Linux image
* [spidev](https://pypi.python.org/pypi/spidev) for controlling SPI

In order to install spidev download the source code and run setup.py manually:
```bash
wget https://pypi.python.org/packages/source/s/spidev/spidev-3.1.tar.gz
tar xfvz  spidev-3.1.tar.gz
cd spidev-3.1
sudo python setup.py install
```



# Class Reference

The interface to the SX127x LoRa modem is implemented in the class `SX127x.LoRa.LoRa`.
The most important modem configuration parameters are:
 
| Function         | Description                                 |
|------------------|---------------------------------------------|
| set_mode         | Change OpMode, use the constants.MODE class |
| set_freq         | Set the frequency                           |
| set_bw           | Set the bandwidth 7.8kHz ... 500kHz         |
| set_coding_rate  | Set the coding rate 4/5, 4/6, 4/7, 4/8      |
| | |
| @todo            |                              |

Most set_* functions have a mirror get_* function, but beware that the getter return types do not necessarily match 
the setter input types.

### Register naming convention
The register addresses are defined in class `SX127x.constants.REG` and we use a specific naming convention which 
is best illustrated by a few examples:

| Register | Modem | Semtech doc.      | pySX127x                   |
|----------|-------|-------------------| ---------------------------|
| 0x0E     | LoRa  | RegFifoTxBaseAddr | REG.LORA.FIFO_TX_BASE_ADDR |
| 0x0E     | FSK   | RegRssiCOnfig     | REG.FSK.RSSI_CONFIG        |
| 0x1D     | LoRa  | RegModemConfig1   | REG.LORA.MODEM_CONFIG_1    |
| etc.     |       |                   |                            |

### Hardware
Hardware related definition and initialisation are located in `SX127x.board_config.BOARD`.
If you use a SBC other than the Raspberry Pi you'll have to adapt the BOARD class.


# Script references

### Continuous receiver `rx_cont.py`
The SX127x is put in RXCONT mode and continuously waits for transmissions. Upon a successful read the
payload and the irq flags are printed to screen.
```
usage: rx_cont.py [-h] [--ocp OCP] [--sf SF] [--freq FREQ] [--bw BW]
                  [--cr CODING_RATE] [--preamble PREAMBLE]

Continous LoRa receiver

optional arguments:
  -h, --help            show this help message and exit
  --ocp OCP, -c OCP     Over current protection in mA (45 .. 240 mA)
  --sf SF, -s SF        Spreading factor (6...12). Default is 7.
  --freq FREQ, -f FREQ  Frequency
  --bw BW, -b BW        Bandwidth (one of BW7_8 BW10_4 BW15_6 BW20_8 BW31_25
                        BW41_7 BW62_5 BW125 BW250 BW500). Default is BW125.
  --cr CODING_RATE, -r CODING_RATE
                        Coding rate (one of CR4_5 CR4_6 CR4_7 CR4_8). Default
                        is CR4_5.
  --preamble PREAMBLE, -p PREAMBLE
                        Preamble length. Default is 8.
```

### Simple LoRa beacon `tx_beacon.py`
A small payload is transmitted in regular intervals.
```
usage: tx_beacon.py [-h] [--ocp OCP] [--sf SF] [--freq FREQ] [--bw BW]
                    [--cr CODING_RATE] [--preamble PREAMBLE] [--single]
                    [--wait WAIT]

A simple LoRa beacon

optional arguments:
  -h, --help            show this help message and exit
  --ocp OCP, -c OCP     Over current protection in mA (45 .. 240 mA)
  --sf SF, -s SF        Spreading factor (6...12). Default is 7.
  --freq FREQ, -f FREQ  Frequency
  --bw BW, -b BW        Bandwidth (one of BW7_8 BW10_4 BW15_6 BW20_8 BW31_25
                        BW41_7 BW62_5 BW125 BW250 BW500). Default is BW125.
  --cr CODING_RATE, -r CODING_RATE
                        Coding rate (one of CR4_5 CR4_6 CR4_7 CR4_8). Default
                        is CR4_5.
  --preamble PREAMBLE, -p PREAMBLE
                        Preamble length. Default is 8.
  --single, -S          Single transmission
  --wait WAIT, -w WAIT  Waiting time between transmissions (default is 0s)
```