.. image:: https://img.shields.io/badge/tstn--053-lsst.io-brightgreen.svg
   :target: https://tstn-053.lsst.io
.. image:: https://github.com/lsst-tstn/tstn-053/workflows/CI/badge.svg
   :target: https://github.com/lsst-tstn/tstn-053/actions/

#########################
Auxiliary Telescope Vents
#########################

TSTN-053
========

The Vera C. Rubin Auxiliary Telescope (AuxTel) dome vent system is designed to regulate airflow within the
dome environment, reducing thermal gradients and supporting stable observing conditions. A system has been
designed and constructed to provide automated control and telemetry for the dome vents.

The mechanical system consists of four Greenheck EAD-635 vent gates, each operated by fail-safe spring-return
actuators with integrated open and closed limit switches. Electrical control is provided through relay
circuits wired in parallel with existing manual switches, enabling both automated and local operation.

A Raspberry Pi 4B equipped with a Sequent Microsystems industrial control HAT serves as the controller,
providing 24 VDC digital I/O for actuator control and limit switch monitoring. Control logic and state
reporting are handled in software on the Pi, with Modbus used for integrated fan control.

This technical note describes the mechanical layout, electrical and wiring design, control software
architecture, and the integration of the system into the Rubin Observatory Control System (OCS).
Communication is mediated by a CSC (Component Software Controller) implemented with the Service Abstraction
Layer (SAL), supporting telemetry, scripted control, and future system expansion.

**Links:**

- Publication URL: https://tstn-053.lsst.io
- Alternative editions: https://tstn-053.lsst.io/v
- GitHub repository: https://github.com/lsst-tstn/tstn-053
- Build system: https://github.com/lsst-tstn/tstn-053/actions/


Build this technical note
=========================

You can clone this repository and build the technote locally if your system has Python 3.11 or later:

.. code-block:: bash

   git clone https://github.com/lsst-tstn/tstn-053
   cd tstn-053
   make init
   make html

Repeat the ``make html`` command to rebuild the technote after making changes.
If you need to delete any intermediate files for a clean build, run ``make clean``.

The built technote is located at ``_build/html/index.html``.

Publishing changes to the web
=============================

This technote is published to https://tstn-053.lsst.io whenever you push changes to the ``main`` branch on GitHub.
When you push changes to a another branch, a preview of the technote is published to https://tstn-053.lsst.io/v.

Editing this technical note
===========================

The main content of this technote is in ``index.rst`` (a reStructuredText file).
Metadata and configuration is in the ``technote.toml`` file.
For guidance on creating content and information about specifying metadata and configuration, see the Documenteer documentation: https://documenteer.lsst.io/technotes.
