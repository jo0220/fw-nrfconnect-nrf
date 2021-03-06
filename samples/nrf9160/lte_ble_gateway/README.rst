.. _lte_sensor_gateway:

nRF9160: LTE Sensor Gateway
###########################

The LTE Sensor Gateway sample demonstrates how to transmit sensor data from an nRF9160 DK to the `nRF Cloud`_.
Unlike the :ref:`nrf_cloud` sample, the sensor data is collected via Bluetooth LE.
Therefore, this sample acts as a gateway between Bluetooth LE and the LTE connection to the nRF Cloud.

Overview
*********

The sample connects via Bluetooth LE to a Thingy:52 that is running the factory pre-loaded application.
It then starts collecting data from two sensors:

 * The flip state of the Thingy:52
 * Simulated GPS position data

The data from both sensors is aggregated in memory.
Flipping the Thingy:52, which causes a change in the flip state to "UPSIDE_DOWN", triggers an alarm that sends the aggregated data over LTE to the `nRF Cloud`_.

Requirements
************

* A `Nordic Thingy:52`_
* The following development board:

  * nRF9160 DK board (PCA10090)

* :ref:`zephyr:bluetooth-hci-uart-sample` must be programmed to the nRF52 board controller on the board.
* :ref:`secure_boot` must be programmed on the board.

  The sample is configured to compile and run as a non-secure application on nRF91's Cortex-M33.
  Therefore, it requires the :ref:`secure_boot` bootloader that prepares the required peripherals to be available for the application.

Building and running
********************

This sample can be found under :file:`samples/lte-gateway/` in the |NCS| folder structure.

The sample is built as a non-secure firmware image, thus with ``CONFIG_TRUSTED_EXECUTION_NONSECURE=y`` set in ``prj.conf``.
It can be programmed independently from the secure boot firmware.

See :ref:`gs_programming` for information about how to build and program the application.
Note that you must program two different applications as described in the following section.

Programming the sample
======================

When you connect the nRF9160 DK board to your computer, three virtual serial ports (USB CDC class) should appear.
The first port is connected to the main controller (nRF9160) on the board, while the second port is connected to the board controller (nRF52840).

Before you program the sample application onto the main controller, you must program the :ref:`zephyr:bluetooth-hci-uart-sample` sample onto the board controller:

1. Put the **SW5** switch (marked debug/prog) in the **NRF52** position to program the board controller.
#. Build the :ref:`zephyr:bluetooth-hci-uart-sample` sample for the nrf52840_pca10090 board and program it.
#. Verify that the sample was programmed successfully by connecting to the second serial port with a terminal emulator (for example, PuTTY) and checking the output.
   See :ref:`putty` for the required settings.

After programming the board controller, you must program the :ref:`secure_boot` sample and the LTE Sensor Gateway sample to the main controller:

1. Put the **SW5** switch (marked debug/prog) in the **NRF91** position to program the main controller.
#. Build the :ref:`secure_boot` sample and program it.
#. Build the LTE Sensor Gateway sample (this sample) and program it.
#. Verify that the sample was programmed successfully by connecting to the first serial port with a terminal emulator (for example, PuTTY) and checking the output.
   See :ref:`putty` for the required settings.

Testing
=======

After programming the sample and all prerequisites to the board, test it by performing the following steps:

1. Power on your Thingy:52 and observe that it starts blinking blue.
#. Open a web browser and navigate to https://nrfcloud.com/.
   Follow the instructions to set up your account and add an LTE device.
   A pattern of switch and button actions is displayed.
#. Power on the board.
#. Observe in the terminal window connected to the first serial port that the board starts up in secure boot and that the application starts.
   This is indicated by output similar to the following lines::

      Secure Boot: prepare to jump to Non-Secure image
      ***** Booting Zephyr OS v1.13.99 *****

#. Observe that "Application started" is printed to the terminal window after the LTE link is established.
   This might take several minutes.
#. Observe that LED 3 starts blinking as the connection to nRF Cloud is established.

   See :ref:`nrf_cloud_user_interface` in the :ref:`nrf_cloud` sample documentation for detailed information about the different LED states used by the sample.
#. The first time you start the sample, pair the device to your account:

   a. Observe that both LED 3 and 4 start blinking, indicating that the pairing procedure has been initiated.
   #. Follow the instructions on `nRF Cloud`_ and enter the displayed pattern.
      In the terminal window, you can see the pattern that you have entered.
   #. If the pattern is entered correctly, the board and your nRF Cloud account are paired and the device reboots.
      If the LEDs start blinking in pairs, check in the terminal window which error occurred.
      The device must be power-cycled to restart the pairing procedure.
   #. After reboot, the board connects to the nRF Cloud, and the pattern disappears from the web page.
#. Observe that LED 4 is turned on to indicate that the connection is established.
#. Observe that the device count on your nRF Cloud dashboard is incremented by one.
#. Make sure that the Thingy:52 has established a connection to the application.
   This is indicated by it blinking green.
#. Flip the Thingy:52 (so that the USB port points upward) to trigger sending the sensor data to the nRF Cloud.
#. Select the device from your device list on nRF Cloud, and observe that sensor data is received from the board.
#. Observe that the data is updated in nRF Cloud.


Dependencies
************

This sample uses the following libraries:

From |NCS|
  * :ref:`lib_nrf_cloud`
  * ``lib/gps_sim``
  * ``lib/bsd_lib``
  * ``lib/sensor_sim``
  * ``lib/dk_buttons_and_leds``
  * ``drivers/lte_link_control``

From Zephyr
  * :ref:`zephyr:bluetooth_api`

In addition, it uses the following samples:

From |NCS|
  * :ref:`secure_boot`

From Zephyr
  * :ref:`zephyr:bluetooth-hci-uart-sample`
