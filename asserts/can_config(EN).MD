#Agilex CAN Module Configuration Instructions

##Install CAN Tools

To configure the CAN module, first install the necessary tools:

```shell
sudo apt update && sudo apt install can-utils ethtool
```

These tools are used to configure the CAN module.

Issue: ip: command not found
If running the bash script returns ip: command not found, install the ip command with:

```shell
sudo apt-get install iproute2
```

## Quick Usage

### Enabling the CAN Module
Set Shell Script Parameters

#### Single Robotic Arm

If your PC is connected to only one USB-to-CAN module, use the can_activate.sh script. Run the command:

```shell
bash can_activate.sh can0 1000000
```
#### Multiple USB-to-CAN Modules
If multiple USB-to-CAN modules are connected:

Disconnect all CAN modules from the PC.

Connect only the module linked to the robotic arm to the PC.

Run the following command to identify the module:

```shell
sudo ethtool -i can0 | grep bus
```
Record the bus-info value (e.g., 1-2:1.0).

Note: The first connected module is usually assigned can0. If no CAN device is detected, use the script bash find_all_can_port.sh to find the CAN name corresponding to the USB address.

#### Activate the CAN Device
- Assuming the recorded bus-info is 1-2:1.0, execute the following command:

```shell
bash can_activate.sh can_piper 1000000 "1-2:1.0"
```

Explanation: The device connected to the USB port with bus-info 1-2:1.0 is renamed to can_piper, set to a baud rate of 1000000, and activated.

Verify Activation
Run ifconfig to check if can_piper exists. If it does, the CAN module is successfully configured.

Activating Multiple CAN Modules
Use the can_config.sh script. Set the EXPECTED_CAN_COUNT parameter in the script to the desired number of CAN modules to activate (e.g., 2).

Identify Each CAN Module:

Insert one CAN module into the PC, then execute:

```shell
sudo ethtool -i can0 | grep bus
```

Record the bus-info value (e.g., 1-2:1.0).

Insert the next CAN module into a different USB port, then execute:

```shell
sudo ethtool -i can1 | grep bus
```
Note: The first connected module is usually assigned can0, the second can1. Use bash find_all_can_port.sh if needed to identify USB-to-CAN module names.

Assume the recorded bus-info values are 1-2:1.0 and 1-4:1.0.

Update Script Parameters:

Replace the parameters inside USB_PORTS in the script:

```
USB_PORTS["1-9:1.0"]="can_left:1000000"
```
with:
```
USB_PORTS["1-2:1.0"]="can_left:1000000"
```

Similarly, update for the second module:

```
USB_PORTS["1-5:1.0"]="can_right:1000000"
```
with:
```
USB_PORTS["1-4:1.0"]="can_right:1000000"
```
Explanation: The device connected to the USB port with bus-info 1-2:1.0 is renamed to can_left with a baud rate of 1000000 and activated. Similarly, 1-4:1.0 is renamed to can_right.

Activate and Verify:

Run the script:

```
bash can_config.sh
```
Check the terminal output for activation success.

Use ifconfig to verify if can_left and can_right are present. If they are, the CAN modules are successfully configured.
