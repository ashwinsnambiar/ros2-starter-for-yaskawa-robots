# Installation of motoROS2 package on the SIA20F - FS100 controller:

1. Check the prerequisites:
    - Minimum version of controller system software : **FS4.00**.
    - Controller must have a correctly configured network connection: **LAN and IP addresses**
    - ROS2 version : ***todo: verified only for Humble***
    - Docker or a from-source build of the micro-ROS Agent ***todo: verified only with the docker image***
    - FastDDS as RMW (even when using ROS 2 Galactic)
1. Download the FS100 support MotoROS2 [alpha60 binary](https://github.com/user-attachments/files/16084908/20240703_mr2_fs1_h_9.9.9-FS100-alpha60.zip).
1. Prepare the `motoros2_config.yaml` config file.
1. [Optional] Run the Debug log client from the `tools` folder of the MotoROS2 repository
1. Checking the MotoPlus configuration
1. Installing MotoROS2 out file
1. Verifying installation
1. ***todo***: Confirm if MotoROS init_ros job has  to be removed. Also check if possible to run MotoROS alongside MotoROS2
1. ***todo***: Setup ROS2 workspace

## Checking and updating the system software version
The minimum required software version with the MotoROS2 package support for the FS100 controller as per [#227 (comment) on motoros2 repo](https://github.com/Yaskawa-Global/motoros2/pull/227#issuecomment-2185911647) is **FS4.00**. To check the version of the system software: 

1. touch `{MAIN MENU}` on the pendant keypad

2. touch `[SYSTEM INFO]`→`[Version]`

Look for the version number starting with `FS`.

For detailed upgrade procedure refer the [Yaskawa Manual](https://www.motoman.com/getmedia/C8A2FDB7-9982-478A-A7A2-6E27E4427691/160725-1CD). Page 39 of the manual contains information about how to force upgrade the controller, if the connection to the controller through the pendant is lost. ***There is a correction in the manual. The SW1 rotary switch must be changed to position '5' instead of 'E'.*** Also, see the following [link](https://knowledge.motoman.com/hc/en-us/articles/6178072366359-FS100-CPU-Software-Installation) for reference pictures for the same.

## Configuring the network connection of the controller  
The controller, pendant and the controlling PC must be in the same local network (physically connected via the LAN ports). The IP addresses should have the same network ID (i.e. the first 3 numbers of the address must be the same). Follow [this](https://knowledge.motoman.com/hc/en-us/articles/5753080039703-Setting-Pendant-IP-address-for-FS100) guide to set up the IP addresses on the pendants. In order to go to the `MANAGEMENT MODE` security level mentioned in the guide, go to `[SYSTEM]`→`[SECURITY]` and select `MANAGEMENT MODE` from the drop-down. The default password is **99999999** (eight 9s). Refer following [link](https://www.manualslib.com/manual/1298409/Yaskawa-Fs100.html?page=25#manual) for further clarifications. Ping the IPs of both the pendant and the controller to verify connection. Refer the Yaskawa [network settings](https://knowledge.motoman.com/hc/en-us/articles/4405490891543-Understanding-Network-Settings) webpage and the [network configuration guide](https://github.com/Yaskawa-Global/motoros2/blob/main/doc/network_configuration.md#network-configuration) in the MotoROS2 repo for more details. 

## Downloading the alpha binary of MotoROS2 for FS100 support
As per [comment](https://github.com/Yaskawa-Global/motoros2/pull/227#issuecomment-2205991850), the main contributors of the package have made an alpha binary that supports the FS100 controller. The `zip` file can be extracted to a temporary location. After installation, the extracted files and parent `zip` file are not required (refer [link](https://github.com/Yaskawa-Global/motoros2?tab=readme-ov-file#extracting-the-files)). The binary can be installed following steps similar to the DX200 installation [procedure](https://github.com/Yaskawa-Global/motoros2?tab=readme-ov-file#installation). The ***todo:***[[installation]] instructions are detailed further down in this guide for reference. Read the instructions in the [comment](https://github.com/Yaskawa-Global/motoros2/pull/227#issuecomment-2205991850) and further down the thread for clarifications in the installation process.

## Preparing the ```motoros2_config.yaml``` config file

In the MotoROS2 github repo, a [config template](https://github.com/Yaskawa-Global/motoros2/tree/main/config) is available. All fields have defaults, except the IP address and UDP port number at which MotoROS2 expects the micro-ROS Agent to be reachable. These fields must be set to correct values by users, as otherwise MotoROS2 will not be able to communicate with the ROS 2 node graph.

Edit the template `.yaml` and change the `agent_ip_address` and `agent_port_number` to point to the host which will run the micro-ROS Agent application (the PC, not the robot controller). Review the rest of the configuration file and change values as necessary for your specific deployment.

***todo: add details***

Verify the correctness of the `.yaml` configuration file before copying it to the controller. The instructions to perform the same are available at [this](https://github.com/Yaskawa-Global/motoros2/blob/main/README.md#verifying-yaml-correctness) link.

## Installing the binary on the controller

### Running the debug log client [Optional]

For better understanding the installation process and troubleshoot errors that might pop up, the developer team has requested to capture all output MotoROS2 produces. This can be done automatically by running the [debug log client](https://github.com/Yaskawa-Global/motoros2/blob/a1c9b5b7f9a9e0806dd67ba9f087701d4a821bd6/doc/troubleshooting.md#debug-log-client) before starting the installation (refer [comment](https://github.com/Yaskawa-Global/motoros2/pull/227#issuecomment-2205991850)). It is a simple python script that can be found in the `tools` folder of the repository. It stores the logs and outputs in `.txt` format at the current working directory.

### Checking MotoPlus configuration

Use the following steps to verify MotoPlus has been correctly configured for MotoROS2, and the necessary settings are active:

1. boot the controller while holding `{MAIN MENU}` on the pendant keypad to enter *Maintenance* mode. Alternatively, boot the controller normally. Touch the `Special Mode` button on the pendant screen and select the `Maintenance Mode` from it. Now, connect the FS100 controller
1. upgrade to `MANAGEMENT MODE` security level by touching `[SYSTEM]`→`[SECURITY]` (default password is all 9's)
1. touch `[SYSTEM]`→`[SETUP]` and select `OPTION` FUNCTION
1. move to `MotoPlus FUNC.`, make sure it is set to `USED`. If it isn't, set it to `USED`
1. move cursor down to `MOTOMAN DRIVER` and make sure it is set to `USED`. If it isn't, set it to `USED`

### Installing MotoROS2 out file

Place the `.out` (main binary), `.yaml` (configuration), and `.dat` (optional: contains I/O names and are only required for [custom `INFORM` jobs](https://github.com/Yaskawa-Global/motoros2?tab=readme-ov-file#example-inform-jobs)) files on an external storage device: Compact Flash (CF) and USB sticks (refer [note](#note-2)). Insert the storage device into the robot's programming pendant.


    Note: The Functional Safety Unit (FSU) feature is not available in FS100. So, the following steps can be ignored.

~~If the controller is configured with the Functional Safety Unit (FSU), then `SAVE DATA CRC CHECK FUNC (FSU)` must be temporarily disabled during the installation procedure.~~ 

~~Turn on the robot controller to enter *Normal Operation* mode.~~

~~In *Normal Operation* mode:~~

1. ~~upgrade to `MANAGEMENT MODE` security level by touching `[SYSTEM INFO]`→`[SECURITY]` (default password is all 9's) (Note: in other controllers like YRC1000 and DX200, it has to be upgraded to SAFETY instead. Refer [comment](https://github.com/Yaskawa-Global/motoros2/pull/227#issuecomment-2223512016))~~
1. ~~touch `[SETUP]`→`[FUNCTION ENABLE]`~~
1. ~~navigate to `SAVE DATA CRC CHECK FUNC (FSU)`~~
1. ~~set this feature to `INVALID`~~
1. ~~turn off the robot controller~~

Turn on the robot controller while holding the `{Main Menu}` key on the keypad to enter *Maintenance* mode. You may release the key when you see the Yaskawa logo appear on the screen. Alternatively, boot the controller normally. Touch the `Special Mode` button on the pendant screen and select the `Maintenance Mode` from it. Now, connect the FS100 controller.

In *Maintenance* mode:

1. upgrade to `MANAGEMENT MODE` security level by touching `[SYSTEM]`→`[SECURITY]` (default password is all 9's)
1. touch `[MotoPlus APL.]`→`[Device]` to select `CF:Pendant`, or `USB:Pendant` as the target device
1. touch `[MotoPlus APL.]`→`[LOAD (USER APPLICATION)]` to select and load the `mr2_*_*.out` file
1. touch `[MotoPlus APL]`→`[FILE LIST (Controller)]` and verify that MotoROS2 was properly installed and no other MotoPlus applications are currently loaded on the controller ***........................................................todo............................... delete the MotoRosFS_v193_velocity.out file................................***
1. (YRC1000[micro] & FS100 only): touch `[File]`→`[Initialize]` and select `USER DEFINED FILES`
1. (YRC1000[micro] & FS100 only): select `SRAM RAM DRIVE` and initialize it
1. rotate the pendant key-switch (upper left of pendant) fully counter-clockwise into TEACH mode
1. reboot the robot controller into regular mode

In *Normal Operation* mode:

1. you will get `ALARM 8013 [0] Missing MotoROS2 cfg file`. Touch `[RESET]` to clear the alarm
1. upgrade to `MANAGEMENT MODE` security level by touching `[System Info]`→`[Security]` (default password is all 9's)
1. touch `[PARAMETER]`→`[S2C]` and set the following parameters:
    1. `S2C0541 = 0` ***Note: Initial value - `0`***
    1. `S2C0542 = 0` ***Note: Initial value - `0`***
    1. `S2C1102 = 2` ***Note: Initial value - `0`***
    1. `S2C1104 = 2` ***Note: Initial value - `0`***
    1. `S2C1117 = 1` (DX200 & FS100 only) ***Note: Initial value - `1`***
    1. `S2C1250 = 1` ***Note: Initial value - `0`***
    1. `S2C1402 = 3` ***Note: Initial value - `0`***

If a custom INFORM job will be used:

1. touch `[EX MEMORY]`→`[Load]`
1. cursor to `JOB` and press `[SELECT]`
1. cursor to your job file and press `[SELECT]` then [`ENTER]`

For FS100, to copy the configuration file,

1. power down the FS100 controller
1. copy the `motoros2_config.yaml` file to a USB storage drive
1. insert the USB drive into the `CN106` USB port inside the controller cabinet
1. leave the drive in place, it will load and save it there
1. power up the FS100 controller

Within 30 seconds of loading the configuration file, you should get alarm `8001[10] Speed FB enabled, reboot now`. Reboot again and there should be no alarms.

The USB drive can be removed from the controller cabinet after powering off the controller. Since the FS100 has SRAM, the `.yaml` config will be saved in the controller. Refer [comment](https://github.com/Yaskawa-Global/motoros2/pull/227#issuecomment-2223544170).

If you receive any errors or alarms after rebooting, please refer to the [Troubleshooting](https://github.com/Yaskawa-Global/motoros2/blob/main/README.md#troubleshooting) section for information on how to remedy the issue.

~~If `SAVE DATA CRC CHECK FUNC (FSU)` was disabled at the start of this procedure, then it can now be re-enabled.~~

1. ~~upgrade to `MANAGEMENT MODE` security level by touching `[System Info]`→`[Security]` (default password is all 9's)~~
1. ~~touch `[Setup]`→`[Function Enable]`~~
1. ~~navigate to `SAVE DATA CRC CHECK FUNC (FSU)`~~
1. ~~set this feature to `VALID`~~


> #### Note 1: 
> Please be patient when installing the .out from USB: the FS100 can be very slow when copying the file, and it can appear 'frozen'. Do not interfere and just wait for it to complete the installation (refer [comment](https://github.com/Yaskawa-Global/motoros2/issues/19#issuecomment-1997960174)).

> #### Note 2: 
> The installation process using the compact flash will be much slower than using the USB drive (refer [comment](https://github.com/Yaskawa-Global/motoros2/issues/19#issuecomment-1997995550)). Only particular USB drives seem to be supported on the pendant. We faced an issue of the controller not detecting drives with USB 3.0 and resolved when we used an older USB2.0 drive. A list of recommended drives are mentioned in the Yaskawa FS100 upgrade [manual](https://www.motoman.com/getmedia/C8A2FDB7-9982-478A-A7A2-6E27E4427691/160725-1CD). Surprisingly, the installation completed under 15 seconds during our initial try, contrary to the observations by the Yaskawa team.

> #### Note 3: 
> If you encounter `ERROR 3640: limit of file number`, remove all other `.out` files initially and test MotoROS2. Add each other `.out` files one by one to test compatibility. If ROS was used earlier, the `MotoROSFS100.out` will have to be removed (***.......................................................................................todo check if both works together.......................................................*** But, right now in the system both are present and MotoROS2 is running properly, though it produces an error initially of invalid JOB (`ALARM 8014`) and could not save PARAMETERS file (`ALRAM 8000`)). Refer [comment](https://github.com/Yaskawa-Global/motoros2/pull/227#issuecomment-2223512016) for clarifications.

> #### Note 4: 
> Document installation experience, and log any and all errors and warnings -- whether on the pendant, a terminal or somewhere else. Share with the MotoROS2 developers. Use the [debug log client](#running-the-debug-log-client-optional).

> #### Note 5: 
> After copying the `.yaml` config file and rebooting the controller, the error `ALARM 8003: Config Err: Set S2C1250=1 [14]` was encountered. This happened due to improper setting of the `S2C1250` parameter in the previous step before copying the `.yaml` config file. It was resolved by changing the parameter to the correct value of `1`. On rebooting, the error vanished, and the next step could be continued.

## todo: Configuration troubleshooting 

If network problems arises, go through this [network configuration](https://github.com/Yaskawa-Global/motoros2/blob/main/doc/network_configuration.md#network-configuration) page to troubleshoot the errors.