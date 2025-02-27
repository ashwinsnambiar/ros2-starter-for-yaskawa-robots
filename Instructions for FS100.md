## Installation of motoROS2 package on the SIA20F - FS100 controller:

1. Check the prerequisites:
    - Minimum version of controller system software : **FS4.00**.
    - Controller must have a correctly configured network connection: **LAN and IP addresses**
    - ROS2 version : ***todo: verified only for Humble ................................................................................................................................... ........................................................................***
    - Docker or a from-source build of the micro-ROS Agent ***todo: check .........................................***
    - FastDDS as RMW (even when using ROS 2 Galactic) ***todo: check .....................................................***
2. Download the FS100 support MotoROS2 alpha binary.
3. Prepare the `.yaml` config file.
4. [Optional] Run the Debug log client from the `tools` folder of the MotoROS2 repository

### Checking and updating the system software version
The minimum required software version with the MotoROS2 package support for the FS100 controller as per [#227 (comment) on motoros2 repo](https://github.com/Yaskawa-Global/motoros2/pull/227#issuecomment-2185911647) is **FS4.00**. To check the version of the system software: ***todo: check from .....................................................***

1. touch `{MAIN MENU}` on the pendant keypad

2. touch `[System Info]`→`[Version]`

Look for the version number starting with `FS............` ***todo: check till here .....................................................***

For detailed upgrade procedure refer the [Yaskawa Manual](https://www.motoman.com/getmedia/C8A2FDB7-9982-478A-A7A2-6E27E4427691/160725-1CD). Page 39 of the manual contains information about how to force upgrade the controller, if the connection to the controller through the pendant is lost. ***There is a correction in the manual. The SW1 rotary switch must be changed to position '5' instead of 'E'.*** Also, see the following [link](https://knowledge.motoman.com/hc/en-us/articles/6178072366359-FS100-CPU-Software-Installation) for reference pictures for the same.

### Configuring the network connection of the controller  
The controller, pendant and the controlling PC must be in the same local network (physically connected via the LAN ports). The IP addresses should have the same network ID (i.e. the first 3 numbers of the address must be the same). Follow [this](https://knowledge.motoman.com/hc/en-us/articles/5753080039703-Setting-Pendant-IP-address-for-FS100) guide to set up the IP addresses on the pendants. In order to go to the `MANAGEMENT` security level mentioned in the guide, go to `[SYSTEM]`→`[SECURITY]` and select `MANAGEMENT` mode from the drop-down. The default password is **99999999** (eight 9s). Refer following [link](https://www.manualslib.com/manual/1298409/Yaskawa-Fs100.html?page=25#manual) for further clarifications. Ping the IPs of both the pendant and the controller to verify connection. Refer the Yaskawa [network settings](https://knowledge.motoman.com/hc/en-us/articles/4405490891543-Understanding-Network-Settings) webpage for more details. 

### Downloading the alpha binary of MotoROS2 for FS100 support
As per [comment](https://github.com/Yaskawa-Global/motoros2/pull/227#issuecomment-2205991850), the main contributors of the package have made an alpha binary that supports the FS100 controller. The `zip` file can be extracted to a temporary location. After installation, the extracted files and parent `zip` file are not required (refer [link](https://github.com/Yaskawa-Global/motoros2?tab=readme-ov-file#extracting-the-files)). The binary can be installed following steps similar to the DX200 installation [procedure](https://github.com/Yaskawa-Global/motoros2?tab=readme-ov-file#installation). The ***todo:***[[installation]] instructions are detailed further down in this guide for reference. Read the instructions in the [comment](https://github.com/Yaskawa-Global/motoros2/pull/227#issuecomment-2205991850) and further down the thread for clarifications in the installation process.

### Preparing the ```.yaml``` config file
In the 