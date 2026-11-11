# SeaWolf IX Dry Run Procedure

> **Draft — not yet verified on SeaWolf IX.** Confirm the vehicle setup and the steps below with the team before operating powered hardware. The commands and behavior may differ on the deployed robot. Stop if a check fails or the vehicle behaves unexpectedly.

This procedure adapts the [SeaWolf VIII dry run procedure](https://github.com/ncsurobotics/ncsurobotics.github.io/blob/f6ab5934bec61bcbda37b0d44e166e11474c8c33/src/pages/dryRun/dry_run.md) using the SW9S source at [70189dd](https://github.com/ncsurobotics/SW9S/tree/70189dd9da7947c03ca5881824a36347782af906). It has not been tested on the vehicle. MEB and MSB tests are omitted pending confirmation of the SeaWolf IX hardware configuration.

## Before starting

- Use the team laptop and have a team member familiar with SeaWolf IX present.
- Secure the vehicle and keep people, loose objects, and cables clear of the thrusters. Identify the hardware disarm/kill control before applying power.
- Confirm the battery configuration, tether and power-on sequence, and safe conditions for running thrusters. The SeaWolf VIII requirement to connect both batteries has not been verified for SeaWolf IX.
- Confirm the deployed SW9S version, Jetson login, approved control-board test tool, expected thruster mapping and directions, and shutdown procedure.

## Connect to the Jetson

1. With the robot disarmed, connect the batteries, power, and tether using the team-approved SeaWolf IX sequence.
2. Connect the tether to the team laptop and wait for the Ethernet connection.
3. The SW9S deployment recipe uses the following Jetson address and account. Confirm that they match the vehicle, then connect:

   ```bash
   ssh sw9@192.168.2.5
   ```

4. Enter the team-provided password if prompted. Verify that the terminal is connected to the Jetson.

## Check the control board

Run the following on the Jetson:

```bash
ls -l /dev/serial/by-id/
```

The checked-in SW9S `config.toml` names an STMicroelectronics Control Board v2 serial device. Compare the actual device path with `cb_path` in the configuration used on the robot. The serial number in the checked-in path may not match the installed board. If the expected device is missing or the paths differ, resolve that with the team before continuing; do not guess a replacement path.

From the directory containing the deployed SW9S configuration, if the `sw9s` executable is available on the path, check the configuration:

```bash
sw9s cfg check config.toml
```

This checks whether the file can be loaded and parsed; it does not verify the hardware connection, motor mapping, or calibration. Use the actual executable and configuration locations confirmed for the robot. Do not generate or overwrite a configuration as part of this check. The repository's example motor matrix is zeroed and must not be assumed to be the vehicle's calibrated configuration.

## Verify arm and kill

With the team member operating the hardware control, verify the physical arm and disarm/kill behavior using the approved SeaWolf IX procedure. Confirm the expected indicators and that disarming stops propulsion before proceeding. The SeaWolf VIII beep sequence and timing have not been confirmed for SeaWolf IX.

**Do not rely on SW9S to detect hardware arm or disarm at the inspected revision.** Its arm handler uses a timed placeholder; its log messages do not prove the physical switch state. The shutdown path attempts to command zero motion, but this is not a verified substitute for the hardware kill control.

## Test thrusters

With the vehicle secured, the area clear, and the hardware kill control immediately accessible, use the team's approved SeaWolf IX individual-thruster test and settings. Test each thruster individually and compare its number, position, and direction against the team's verified mapping. Stop and disarm if a thruster fails to move, moves unexpectedly, clicks, or behaves weakly.

The inspected SW9S checkout does not provide a documented individual-thruster dry-run command. Its `test` mission commands depth and forward motion, and `slow-spin` commands all eight thrusters. Neither is an individual motor test; do not use them as a substitute for the approved test.

## Check cameras

On the Jetson, list video devices if the tool is installed:

```bash
v4l2-ctl --list-devices
```

Compare the results with the cameras actually installed on SeaWolf IX. Confirm each camera produces the expected image using the team's current viewing procedure. The SeaWolf VIII camera names, streaming scripts, and RTSP addresses have not been verified for SeaWolf IX.

## Battery and other hardware checks

Confirm with the team whether SeaWolf IX has a load-balancing board and whether a single-battery test is appropriate. Do not disconnect a battery while the robot is powered unless the team has confirmed that this is safe for the current electrical design. Check any other installed sensors or mechanisms using their approved procedures.

## Disarm and shut down

1. Disarm the robot using the hardware control and confirm the thrusters have stopped.
2. Shut down the Jetson using the team-approved procedure, then turn off vehicle power and disconnect equipment in the approved order.
3. Record any failed checks or unexpected behavior for the team before the next run.
