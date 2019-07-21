### USB -> AXI Debug Bridge

Github:   [http://github.com/ultraembedded/core_usb_bridge](https://github.com/ultraembedded/core_usb_bridge)

This component is a USB (CDC-ACM / USB Serial) to AXI4-Lite bus master bridge.  
This IP enumerates as a full speed (12Mbit/s) USB device which can then be used to read / write to memories / peripherals in your FPGA based SoC.

The PHY / transceiver are built in requiring no additional external hardware other than a USB port connected to the FPGA I/Os.

#### Cloning

This repo contains submodules, and to clone them run the following command;

```
git clone --recursive https://github.com/ultraembedded/core_usb_bridge.git

```

####  Design Files
```
usb_bridge_top.v
├── usb_bridge
│   └── src_v
│       ├── usb_bridge_fifo.v
│       └── usb_bridge.v
├── usb_cdc
│   └── src_v
│       ├── usb_cdc_core.v
│       ├── usb_desc_rom.v
│       ├── usbf_crc16.v
│       ├── usbf_defs.v
│       ├── usbf_device_core.v
│       ├── usbf_sie_ep.v
│       ├── usbf_sie_rx.v
│       └── usbf_sie_tx.v
└── usb_fs_phy
    └── src_v
        ├── usb_fs_phy.v
        └── usb_transceiver.v
```

#### Configuration / Requirements
* Top: usb_bridge_top
* Clock: clk_i - 48MHz
* Reset: rst_i - Asynchronous, active high
* VID/PID can be changed in usb_desc_rom.v

#### Hardware

Connection from your FPGA I/Os can be made simply using a breakout board;
* [https://www.sparkfun.com/products/12035](https://www.sparkfun.com/products/12035)

In order to enumerate as a USB FS device, there must be a pull-up on the D+ pin (but not on the D- pin).  
This can be achieved using the internal pull-ups on some FPGAs (though how reliable this is remains to be seen).

Example constraints (Xilinx Vivado);
```
set_property -dict { PACKAGE_PIN N4    IOSTANDARD LVCMOS33 } [get_ports { usb_dp_io }]; # USB D+
set_property -dict { PACKAGE_PIN P3    IOSTANDARD LVCMOS33 } [get_ports { usb_dn_io }]; # USB D-
set_property PULLUP TRUE [get_ports usb_dp_io]
```

For Xilinx FPGAs, pad pull-ups should be disabled before configuration is completed (PUDC_B).

#### Software
Included python based utils provide peek and poke access, plus binary load / dump support.

Examples:
```
# Read a memory location (0x0)
./sw/peek.py -d /dev/ttyACM0 -a 0x0

# Write a memory word (0x0 = 0x12345678)
./sw/poke.py -d /dev/ttyACM0 -a 0x0 -v 0x12345678
``` 

#### References
* [USB Full Speed PHY](https://github.com/ultraembedded/core_usb_fs_phy)
* [USB CDC Device](https://github.com/ultraembedded/core_usb_cdc)