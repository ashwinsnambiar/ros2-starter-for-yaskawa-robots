# Installation of motoROS2 package on the SIA20F - FS100 controller:

1. Check the prerequisites:
    - Minimum version of controller system software : **FS4.00**.
    - Controller must have a correctly configured network connection: **LAN and IP addresses**
    - ROS2 version : ***todo: verified only for Humble ................................................................................................................................... ........................................................................***
    - Docker or a from-source build of the micro-ROS Agent ***todo: check .........................................***
    - FastDDS as RMW (even when using ROS 2 Galactic) ***todo: check .....................................................***
2. Download the FS100 support MotoROS2 alpha binary.
3. Prepare the `motoros2_config.yaml` config file.
4. [Optional] Run the Debug log client from the `tools` folder of the MotoROS2 repository
5. Checking the MotoPlus configuration

## Checking and updating the system software version
The minimum required software version with the MotoROS2 package support for the FS100 controller as per [#227 (comment) on motoros2 repo](https://github.com/Yaskawa-Global/motoros2/pull/227#issuecomment-2185911647) is **FS4.00**. To check the version of the system software: 

1. touch `{MAIN MENU}` on the pendant keypad

2. touch `[SYSTEM INFO]`→`[Version]`

Look for the version number starting with `FS`.

For detailed upgrade procedure refer the [Yaskawa Manual](https://www.motoman.com/getmedia/C8A2FDB7-9982-478A-A7A2-6E27E4427691/160725-1CD). Page 39 of the manual contains information about how to force upgrade the controller, if the connection to the controller through the pendant is lost. ***There is a correction in the manual. The SW1 rotary switch must be changed to position '5' instead of 'E'.*** Also, see the following [link](https://knowledge.motoman.com/hc/en-us/articles/6178072366359-FS100-CPU-Software-Installation) for reference pictures for the same.

## Configuring the network connection of the controller  
The controller, pendant and the controlling PC must be in the same local network (physically connected via the LAN ports). The IP addresses should have the same network ID (i.e. the first 3 numbers of the address must be the same). Follow [this](https://knowledge.motoman.com/hc/en-us/articles/5753080039703-Setting-Pendant-IP-address-for-FS100) guide to set up the IP addresses on the pendants. In order to go to the `MANAGEMENT` security level mentioned in the guide, go to `[SYSTEM]`→`[SECURITY]` and select `MANAGEMENT` mode from the drop-down. The default password is **99999999** (eight 9s). Refer following [link](https://www.manualslib.com/manual/1298409/Yaskawa-Fs100.html?page=25#manual) for further clarifications. Ping the IPs of both the pendant and the controller to verify connection. Refer the Yaskawa [network settings](https://knowledge.motoman.com/hc/en-us/articles/4405490891543-Understanding-Network-Settings) webpage and the [network configuration guide](https://github.com/Yaskawa-Global/motoros2/blob/main/doc/network_configuration.md#network-configuration) in the MotoROS2 repo for more details. 

## Downloading the alpha binary of MotoROS2 for FS100 support
As per [comment](https://github.com/Yaskawa-Global/motoros2/pull/227#issuecomment-2205991850), the main contributors of the package have made an alpha binary that supports the FS100 controller. The `zip` file can be extracted to a temporary location. After installation, the extracted files and parent `zip` file are not required (refer [link](https://github.com/Yaskawa-Global/motoros2?tab=readme-ov-file#extracting-the-files)). The binary can be installed following steps similar to the DX200 installation [procedure](https://github.com/Yaskawa-Global/motoros2?tab=readme-ov-file#installation). The ***todo:***[[installation]] instructions are detailed further down in this guide for reference. Read the instructions in the [comment](https://github.com/Yaskawa-Global/motoros2/pull/227#issuecomment-2205991850) and further down the thread for clarifications in the installation process.

## Preparing the ```motoros2_config.yaml``` config file
***............................................................................................................................todo...........................................check the network port connected to the robot controller.... see if it is indeed 8888 .... the microros agent is run with 8888 port flag***
In the MotoROS2 github repo, a [config template](https://github.com/Yaskawa-Global/motoros2/tree/main/config) is available. All fields have defaults, except the IP address and UDP port number at which MotoROS2 expects the micro-ROS Agent to be reachable. These fields must be set to correct values by users, as otherwise MotoROS2 will not be able to communicate with the ROS 2 node graph.

Edit the template `.yaml` and change the `agent_ip_address` and `agent_port_number` to point to the host which will run the micro-ROS Agent application (the PC, not the robot controller). Review the rest of the configuration file and change values as necessary for your specific deployment.

Verify the correctness of the `.yaml` configuration file before copying it to the controller. The instructions to perform the same are available at [this](https://github.com/Yaskawa-Global/motoros2/blob/main/README.md#verifying-yaml-correctness) link.

***...............todo.........................................................................delete the venv_yamllint venv***

## Installing the binary on the controller

### Running the debug log client [Optional]

For better understanding the installation process and troubleshoot errors that might pop up, the developer team has requested to capture all output MotoROS2 produces. This can be done automatically by running the [debug log client](https://github.com/Yaskawa-Global/motoros2/blob/a1c9b5b7f9a9e0806dd67ba9f087701d4a821bd6/doc/troubleshooting.md#debug-log-client) before starting the installation (refer [comment](https://github.com/Yaskawa-Global/motoros2/pull/227#issuecomment-2205991850)). It is a simple python script that can be found in the `tools` folder of the repository. It stores the logs and outputs in `.txt` format at the current working directory.

### Checking MotoPlus configuration

Use the following steps to verify MotoPlus has been correctly configured for MotoROS2, and the necessary settings are active:

***..........todo......confirm all button names.......................................................***
1. boot the controller while holding `{MAIN MENU}` on the pendant keypad to enter *Maintenance* mode. Alternatively, boot the controller normally. Touch the `Special Mode` button on the pendant screen and select the `Maintenance mode.......` from it. Now, connect the FS100 controller........     ***.... todo............................................................................................***
1. upgrade to `MANAGEMENT` security level by touching `[System Info]`→`[Security]` (default password is all 9's)
1. touch `[System Info]`→`[Setup]` and select `OPTION` FUNCTION
1. move to `MotoPlus FUNC.`, make sure it is set to `USED`. If it isn't, set it to `USED`
1. move cursor down to `MOTOMAN DRIVER` and make sure it is set to `USED`. If it isn't, set it to `USED`

### Installing MotoROS2 out file

Place the `.out` (main binary), `.yaml` (configuration), and `.dat` (optional: contains I/O names and are only required for [custom `INFORM` jobs](https://github.com/Yaskawa-Global/motoros2?tab=readme-ov-file#example-inform-jobs)) files on an external storage device: Compact Flash (CF) and USB sticks (refer [note](#note-2)). Insert the storage device into the robot's programming pendant.

If the controller is configured with the Functional Safety Unit (FSU), then `SAVE DATA CRC CHECK FUNC (FSU)` must be temporarily disabled during the installation procedure.

Turn on the robot controller to enter *Normal Operation* mode.

In *Normal Operation* mode:

1. upgrade to `MANAGEMENT` security level by touching `[System Info]`→`[Security]` (default password is all 9's) (Note: in other controllers like YRC1000 and DX200, it has to be upgraded to SAFETY instead. Refer [comment](https://github.com/Yaskawa-Global/motoros2/pull/227#issuecomment-2223512016))
1. touch `[Setup]`→`[Function Enable]`
1. navigate to `SAVE DATA CRC CHECK FUNC (FSU)`
1. set this feature to `INVALID`
1. turn off the robot controller

Turn on the robot controller while holding the `{Main Menu}` key on the keypad to enter *Maintenance* mode. You may release the key when you see the Yaskawa logo appear on the screen. Alternatively, boot the controller normally. Touch the `Special Mode` button on the pendant screen and select the `Maintenance mode.......` from it. Now, connect the FS100 controller........     ***.... todo............................................................................................***

In *Maintenance* mode:

1. upgrade to `MANAGEMENT` security level by touching `[System Info]`→`[Security]` (default password is all 9's)
1. touch `[MotoPlus APL]`→`[Device]` to select CF, or USB memory type
1. touch `[MotoPlus APL]`→`[Load (User App)]` to select and load the `mr2_*_*.out` file
1. touch `[MotoPlus APL]`→`[File List]` and verify that MotoROS2 was properly installed and no other MotoPlus applications are currently loaded on the controller
1. ***....................todo check if applicable for fs100 since it has the sdram..................................*** (YRC1000[micro] only): touch `[File]`→`[Initialize]` and select `USER DEFINED FILES`
1.  ***....................todo check if applicable for fs100 since it has the sdram..................................*** (YRC1000[micro] only): select `SRAM RAM DRIVE` and initialize it
1. rotate the pendant key-switch (upper left of pendant) fully counter-clockwise into TEACH mode
1. reboot the robot controller into regular mode

In *Normal Operation* mode:

1. you will get alarm `8013 [0] Missing MotoROS2 cfg file`. Touch `[RESET]` to clear the alarm
1. upgrade to `MANAGEMENT` security level by touching `[System Info]`→`[Security]` (default password is all 9's)
1. touch `[PARAMETER]`→`[S2C]` and set the following parameters:
    1. `S2C541 = 0`
    1. `S2C542 = 0`
    1. `S2C1102 = 2`
    1. `S2C1104 = 2`
    1. `S2C1117 = 1` (DX200 only) /........check todo
    1. `S2C1250 = 1`
    1. `S2C1402 = 3`

If a custom INFORM job will be used:

1. touch `[EX MEMORY]`→`[Load]`
1. cursor to `JOB` and press `[SELECT]`
1. cursor to your job file and press `[SELECT]` then [`ENTER]`

For FS100, to copy the configuration file,

1. power down the FS100 controller
1. copy the `motoros2_config.yaml` file to a USB storage drive
1. insert the USB drive into the `CN106` USB port inside the controller cabinet
1. might not need to leave the drive in place as the FS100 has SRAM, it will load and save it there
1. power up the FS100 controller

    ***.....todo.....check ...remocve after*** Note: on DX200, the USB stick used to store motoros2_config.yaml can't be removed, it must remain inserted into the USB port labelled CN106, or at least as long as MotoROS2 is installed on the controller. Without the USB stick in CN106, MotoROS2 would not be able to load its configuration and alarms will be raised on each controller (re)boot.

Within 30 seconds of loading the configuration file, you should get alarm `8001[10] Speed FB enabled, reboot now`. Reboot again and there should be no alarms.

If you receive any errors or alarms after rebooting, please refer to the [Troubleshooting](https://github.com/Yaskawa-Global/motoros2/blob/main/README.md#troubleshooting) section for information on how to remedy the issue.

If `SAVE DATA CRC CHECK FUNC (FSU)` was disabled at the start of this procedure, then it can now be re-enabled.

1. upgrade to `MANAGEMENT` security level by touching `[System Info]`→`[Security]` (default password is all 9's)
1. touch `[Setup]`→`[Function Enable]`
1. navigate to `SAVE DATA CRC CHECK FUNC (FSU)`
1. set this feature to `VALID`


> #### Note 1: 
> Please be patient when installing the .out from USB: the FS100 can be very slow when copying the file, and it can appear 'frozen'. Do not interfere and just wait for it to complete the installation (refer [comment](https://github.com/Yaskawa-Global/motoros2/issues/19#issuecomment-1997960174)).

> #### Note 2: 
> The installation process using the compact flash will be much slower than using the USB drive (refer [comment](https://github.com/Yaskawa-Global/motoros2/issues/19#issuecomment-1997995550)). Only particular USB drives seem to be supported on the pendant. We faced an issue of the controller not detecting drives with USB 3.0 and resolved when we used an older USB2.0 drive. A list of recommended drives are mentioned in the Yaskawa FS100 upgrade [manual](https://www.motoman.com/getmedia/C8A2FDB7-9982-478A-A7A2-6E27E4427691/160725-1CD). 

> #### Note 3: 
> If you encounter `ERROR 3640: limit of file number`, remove all other `.out` files initially and test MotoROS2. Add each other `.out` files one by one to test compatibility. If ROS was used earlier, the `MotoROSFS100.out` will have to be removed (***.......................................................................................todo check if both works together.......................................................*** can also check if both). Refer [comment](https://github.com/Yaskawa-Global/motoros2/pull/227#issuecomment-2223512016) for clarifications.

> #### Note 4: 
> Document installation experience, and log any and all errors and warnings -- whether on the pendant, a terminal or somewhere else. Share with the MotoROS2 developers. Use the [debug log client](#running-the-debug-log-client-optional).

## Conn

If network problems arises, go through this [network configuration](https://github.com/Yaskawa-Global/motoros2/blob/main/doc/network_configuration.md#network-configuration) page to troubleshoot the errors.