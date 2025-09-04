Wiring
======

Observatories in general present a challenging electrical environment due to a combination of factors:
inconsistent or noisy utility power, the presence of varied and often highly inductive equipment (such as
motors, fans, and dome drives), multiple sources of electrical interference, and significant exposure to
outdoor environmental conditions including humidity, dust, temperature variation, wildlife, and so forth. The
vent gate system at the Vera C. Rubin Auxiliary Telescope (AuxTel) is designed to operate reliably within
this context and incorporates both line-voltage and low-voltage components. Major elements include:

 - a 240 VAC power distribution branch derived from the main electrical panel,
 - four Belimo AFBUP-S actuators (one per vent gate),
 - integrated open and closed limit switches within each actuator,
 - a Schneider Altivar ATC320U30N4C variable frequency drive (VFD) for fan control,
 - a Raspberry Pi 4B single-board computer,
 - and two Sequent Microsystems industrial control HATs providing isolated 24 VDC digital I/O.

Together, these components form the electrical basis for the system's control, sensing, and actuation
functionality.

The components of the vent control system are mounted on an aluminum plate fixed to the wall near vent #2.
This plate functions as the primary control panel for the system and currently houses three variable
frequency drives (VFDs), of which only one is wired for use with the exhaust fan. It also includes a USR
TCP232-410S Modbus-to-Ethernet adapter, a Meanwell 24 VDC power supply, a Raspberry Pi 4B with stacked I/O
hats, four DIN-rail relays (one in use), and a set of terminal blocks for both AC and DC circuits. At
present, only the wiring for vent #2 is fully installed; the remaining three vents are in a state of partial
completion. The panel is mounted in an exposed location, but a metal awning has been installed to provide
partial protection from precipitation. A fully enclosed cabinet is planned as a future upgrade to improve
environmental protection and simplify long-term maintenance.

A new single-phase 240 VAC branch circuit, connected to breaker 15 in the main observatory electrical panel,
supplies power to the control plate. This line terminates at a set of terminal blocks, from which power is
routed to the actuator relay, the active VFD, and the 24 VDC supply. The DC supply powers the Pi, its
attached control board, and the relay coils used for switching the actuator circuit. Low-voltage control
wiring is routed separately from AC lines to reduce electrical noise and improve safety. The wiring is
structured to support modular expansion as the remaining gates are brought online.

The system is equipped with a wall-mounted switch box that provides manual control of the vent actuator via a
two-position rotary switch. The switch applies or removes line voltage to the actuator, causing it to open
when energized and close via spring return when power is removed. For the vent wired to the automation
system, the relay output is connected in parallel with the manual switch, creating a logical OR
configuration: applying power from either the manual switch or the control relay will open the actuator. This
also means that if the vent is opened manually, it cannot be closed via software control until the manual
switch is returned to the off position. Operators should be aware of this interaction when using manual
override in conjunction with automated scripts.

The actuators include two built-in mechanical limit switches that indicate when the vent has reached its
fully open or fully closed position. These switches are electrically isolated and brought out to labeled
terminals within the actuator's wiring box. Six conductors are used per actuator: two commons (one for open,
one for closed) and one signal wire for each of the open, closed, and intermediate (~open, ~closed)
positions. For the wired vent, these terminals are connected to the control panel using a length of OLFLEX
110 9G1 cable, which provides nine individually numbered conductors and a green/yellow protective conductor.
The six relevant wires were terminated at the limit switch blocks in the actuator wiring box and connected at
the panel to isolated digital inputs on the Raspberry Pi I/O board. In practice, only the fully open and
fully closed signals are used for software logic. One of the open limit switches (on the fan vent) is known
to be non-functional, and its signal is currently unavailable. The actuator limit switches are wired to
terminal blocks using a standardized color scheme, shown in the table below.

.. list-table:: Actuator Limit Switch Wiring
   :header-rows: 1
   :widths: 5 15 15

   * - Terminal
     - Wire Color
     - Function
   * - 1
     - Purple
     - Closed COM
   * - 2
     - Red
     - Closed
   * - 3
     - White
     - ~Closed
   * - 4
     - Orange
     - Open COM
   * - 5
     - Brown
     - ~Open
   * - 6
     - Gray
     - Open

Manual control of the exhaust fan is provided through a standalone project box located near the control
panel. This box is connected to the VFD via a dedicated cable and includes two controls: a rotary dial and a
two-position switch labeled "START" and "OFF." A printed label instructs the operator to use the dial to
adjust the VFD's frequency setpoint to 25 Hz. The switch toggles the run
command input to the VFD, enabling or disabling fan operation. This manual interface allows the fan to be
operated independently of the control computer. If computer control of the fan is desired, the control box
must be switched to the OFF position.

The automation controller utilizes a Raspberry Pi 4B, equipped with two stacked Sequent Microsystems I/O
HATs: the "Mega Industrial" card and the "16-Input" card. These HATs communicate with the Raspberry Pi via
the I²C bus and are assigned distinct stack levels to differentiate their I²C addresses.

 * Mega Industrial Card: This HAT provides opto-isolated digital outputs, which are used to control the
   relays actuating the vent gates. In the current configuration, four output channels are designated for
   vent control, corresponding to channels 1 through 4.

 * 16-Input Card: This HAT offers opto-isolated digital inputs, suitable for monitoring signals ranging from
   3V to 24V AC/DC. It's employed to read the open and closed limit switch statuses from each vent actuator.
   The configuration assigns specific input channels to each vent's open and closed limit switches,
   facilitating precise monitoring.

The table below summarizes the I/O channel assignments for each card.

+----------+---------------------------------------------+-------------------------------+
| Channel  | Card                                        | Function                      |
+==========+=============================================+===============================+
| 1        |                                             | Vent 1 actuator control       |
+----------+                                             +-------------------------------+
| 2        |                                             | Vent 2 actuator control       |
+----------+ Mega Industrial (Output)                    +-------------------------------+
| 3        |                                             | Vent 3 actuator control       |
+----------+                                             +-------------------------------+
| 4        |                                             | Vent 4 actuator control       |
+----------+---------------------------------------------+-------------------------------+
| 9        |                                             | Vent 4 open limit             |
+----------+                                             +-------------------------------+
| 10       |                                             | Vent 4 closed limit           |
+----------+                                             +-------------------------------+
| 11       |                                             | Vent 3 open limit             |
+----------+                                             +-------------------------------+
| 12       |                                             | Vent 3 closed limit           |
+----------+ Sixteen LV Digital Inputs (Input)           +-------------------------------+
| 13       |                                             | Vent 2 open limit             |
+----------+                                             +-------------------------------+
| 14       |                                             | Vent 2 closed limit           |
+----------+                                             +-------------------------------+
| 15       |                                             | Vent 1 open limit             |
+----------+                                             +-------------------------------+
| 16       |                                             | Vent 1 closed limit           |
+----------+---------------------------------------------+-------------------------------+

