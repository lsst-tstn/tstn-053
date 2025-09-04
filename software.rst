Software
========

At the observatory system level, Rubin software components communicate using the SAL (Service Abstraction Layer)
middleware layer, which transports commands, telemetry, and events via Apache Kafka. Commandable SAL
Components (CSCs) run on central control machines (e.g., ``yagan`` in the case of the summit) and
publish or receive messages over the Kafka network.  Observatory scripts interact with CSCs by sending SAL
commands and consuming telemetry, forming the primary mechanism for the control system.

The vent control system is managed by the ``ATBuilding`` CSC, which serves as the software interface between
SAL and the physical control system. The ``ATBuilding`` CSC communicates directly with the Raspberry Pi
controller via HTTP on port 5000, issuing commands to control the vents and querying the Pi for status
updates. The communication protocol used for communication between the Raspberry Pi and CSC closely resembles
and reuses code from elsewhere in Rubins EAS infrastructure. This design decouples the SAL layer from the
hardware I/O implementation and allows the Raspberry Pi to operate as an independent and reusable control
node within the observatory infrastructure.

The vent control software running on the Raspberry Pi is structured around two primary Python classes:
``Controller`` and ``Dispatcher``. The ``Controller`` class is responsible for direct interaction with the
hardware, including reading limit switch states and activating the relay outputs via the Sequent Microsystems
I/O HATs. It uses the ``smbus3`` library to communicate with the I²C-based input and output boards. The
``Dispatcher`` class handles TCP communication with the ``ATBuilding`` CSC, exposing a simple protocol over
port 5000 for issuing commands and querying system status. The fan VFD is accessed over Modbus TCP using the
``pymodbus`` library.

As is standard practice in Rubin Observatory software, the application is implemented using Python's
``asyncio`` framework to manage concurrent tasks and event-driven I/O.  The ``Dispatcher`` class on the
Raspberry Pi implements a lightweight, line-oriented TCP protocol for communication
with the ``ATBuilding`` CSC. The protocol is plain text, with each command terminated by a carriage return (``\r``).
Commands are sent as single lines in the format:

::

    command <arg1> <arg2> <arg3> ...

Responses are returned as JSON-encoded strings, one per line, with the following template format:

::

    {
        "command": "<command name>",
        "error": 0 or 1,
        "return_value": <number, optional>,
        "exception_name": "<exception type string>",
        "message": "<optional message>",
        "traceback": "<string>",
    }

The ``return_value`` field should be expected only for successful responses to commands that return a result.
The ``error`` field is set to ``1`` in the case of failure, and ``exception_name`` and ``message`` are
populated with diagnostic details.

The following commands are supported:

.. list-table:: Supported Commands
   :header-rows: 1
   :widths: 30 70

   * - Command
     - Argument Types
   * - ``open_vent_gate``
     - Four integers (0, 1, 2, or 3, corresponding to the vent number. If controlling a subset of vents, pad
       the arguments with -1.)
   * - ``close_vent_gate``
     - Four integers (same format as ``open_vent_gate``)
   * - ``get_fan_drive_max_frequency``
     - (none)
   * - ``reset_extraction_fan_drive``
     - (none)
   * - ``set_extraction_fan_drive_freq``
     - One float (target frequency in Hz)
   * - ``set_extraction_fan_manual_control_mode``
     - One boolean (``true`` or ``false``)
   * - ``start_extraction_fan``
     - (none)
   * - ``stop_extraction_fan``
     - (none)
   * - ``ping``
     - (none)

In addition to responses to commands, the Raspberry Pi may asynchronously send JSON-formatted **events** and
**telemetry** messages at any time. These share the same outer structure as command responses, but use
specific command names and populate the ``data`` field. These messages map directly to SAL events and
telemetry:

.. list-table:: Asynchronous Messages
   :header-rows: 1
   :widths: 40 60

   * - Command
     - Data Format
   * - ``evt_vent_gate_state``
     - Current state of each vent gate
   * - ``evt_extraction_fan_drive_fault_code``
     - Most recent fault code reported by the fan drive
   * - ``evt_extraction_fan_drive_state``
     - Current fan drive state
   * - ``telemetry``
     - Dictionary with telemetry fields such as ``tel_extraction_fan`` and ``tel_drive_voltage``

These asynchronous messages allow the Pi to push updates in real time, and (attempt to) maintain 1:1 
correspondence with the SAL telemetry and event interfaces defined in the XML schema. This approach
deviates from the usual policy of ESS device protocols of "don't speak unless spoken to," but the
extra handling involved appears to be minimal, and has the advantage of significantly reducing the
"chattiness" of the protocol.

