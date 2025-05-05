#########################
Auxiliary Telescope Vents
#########################

.. abstract::

   The Vera C. Rubin Auxiliary Telescope (AuxTel) dome vent system is designed to regulate airflow within the
dome environment, reducing thermal gradients and supporting stable observing conditions. A system has been
designed and constructed to provide automated control and telemetry for the dome vents.

The mechanical system consists of four Greenheck EAD-635 vent gates, each operated by fail-safe spring-return
actuators with integrated open and closed limit switches. Electrical control is provided through relay
circuits wired in parallel with existing manual switches, enabling both automated and local operation.

A Raspberry Pi 4B equipped with a Sequent Microsystems industrial control HAT serves as the controller,
providing 24 VDC digital I/O for actuator control and limit switch monitoring. Control logic and state
reporting are handled in software on the Pi, with Modbus used for integrated fan control.

This technical note describes the mechanical layout, electrical and wiring design, control software
architecture, and the integration of the system into the Rubin Observatory Control System (OCS).
Communication is mediated by a CSC (Component Software Controller) implemented with the Service Abstraction
Layer (SAL), supporting telemetry, scripted control, and future system expansion.

Add content here
================

See the `Documenteer documentation <https://documenteer.lsst.io/technotes/index.html>`_ for tips on how to write and configure your new technote.
