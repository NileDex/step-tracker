# Step tracker

I have a 40 mm x 40 mm, 2-layer PCB design (KiCad) for a small battery-powered
motion-sensing wearable. The current design uses an STM32F103C8T6, but I need
Wi-Fi so the board can host a small HTML dashboard (step count and motion data)
that I open from my phone. Please redesign the schematic around the
ESP32-C3-MINI-1-H4 module.

KEEP (reuse as-is):
- LSM6DS3TR-C IMU on I2C with 4.7k pull-ups on SDA/SCL. INT1 must go to an
  RTC-capable GPIO (GPIO0-5) so a step or motion event can wake the ESP32-C3
  from deep sleep.
- USB-C connector with 5.1k pull-downs on CC1 and CC2.
- MCP73831 single-cell LiPo charger, STAT pin routed to a GPIO, 2-pin JST
  battery connector.
- AP2112K-3.3 LDO (add a 10-22 uF capacitor close to the module for Wi-Fi
  transmit bursts).

REMOVE:
- STM32F103C8T6, the 8 MHz crystal and its load caps, and the SWD test points
  (flashing and debugging will be over native USB).

ADD / CHANGE:
- ESP32-C3-MINI-1-H4 with 100 nF + 10 uF decoupling on 3V3.
- EN pin: 10k pull-up to 3V3 plus 1 uF to GND (reset/power-on delay), and a
  reset button.
- GPIO9 boot pin: 10k pull-up plus a BOOT button to GND.
- USB-C D+/D- wired directly to GPIO19 (D+) and GPIO18 (D-) for native USB
  flashing and serial. Add series resistors or ESD protection if appropriate.
- Check all strapping pins (GPIO2, GPIO8, GPIO9) so nothing on the board
  interferes with boot.
- Pick the I2C pins and avoid the USB and strapping pins.

PCB / LAYOUT NOTES:
- Place the module at the board edge with the antenna end facing outward or
  overhanging the edge.
- Keep the antenna keepout area completely free of copper, ground pour, traces
  and components on all layers.
- Keep the LiPo connector and battery away from the antenna end.
- Keep the 40 x 40 mm outline and 3 fiducials; components may go on both sides.

OUTPUT:
1. A summary of the pin assignments (ESP32-C3 GPIO -> net).
2. An updated schematic or netlist.
3. A list of changes to the BOM (removed and added parts).
4. Any design risks or mistakes you notice (power budget, strapping pins,
   charge current for my battery, antenna placement).

Battery capacity: [fill in mAh]. Charge current should be set for that battery
via the MCP73831 PROG resistor.
