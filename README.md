# GMMK_TKL_ISO
**Reverse Engineering the Glorious GMMK TKL keyboard (ISO layout)**

For this procedure I am using the resources provided in the [dk63 repository by smp4488](https://github.com/smp4488/dk63).

- The GMMK TKL ISO uses the eVision VS11K13A, which is a Sonix SN32F268 rebrand.
- Two support chips that control RGB are used as well, the eVison VS12L03A, a Sonix SLED1734X rebrand.

## Dumping Setup
For dumping the firmware, I am utilizing a ST-Link v2 USB adapter [from Amazon](https://www.amazon.de/dp/B07DK26Q5S/ref=cm_sw_r_tw_dp_4SCNG5612C8DDYAFD5NR) with OpenOCD, Python3 and GDB.

The required SWD pins on the GMMK PCB are:

| Pin       | Testpad           | ST-Link v2 |
| --------- | ----------------- | --------   |
| SWDIO     | J4                | JTMS / SWD |
| SWDCLK    | J3                | JTCK / SWC |
| VCC       | VCC               | 3V3        |
| BOOT      | R5                | GND        |

As I have wired the BOOT pin to GND, it is important to connect all cables before inserting the ST-Link into the USB port, it wouldn't enter the Bootloader otherwise.

## OpenOCD
To debug the MCU, utilize OpenOCD as follows:
`openocd -f stlink.cfg -f vs11k13a.cfg`

This command should connect the MCU to the Debugger and a red light on the ST-Link should start to blink.

## Dumping Firmware
On the SN32F268, a 32KB flash is provided, 30KB of which are the firmware, the other 2KB being the bootloader.

To obtain the stock firmware, we want to dump the 30KB sector, located between 0 and 0x77FF(0x7800).

For this, we will use the `dump-memory.py` script as follows: `python3 dump-memory.py 0 0x7800 firmware.bin --openocd 127.0.0.1:4444 --ldr-gadget 0x1FFF02C4 --reg1 r0 --reg2 r0`
