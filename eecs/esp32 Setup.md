# ESP32 Development Environment Setup
## Tools download & Installation
- Download the installer esp-idf-tools-setup from ESP official website.
- Install it to your computer.
    - Note: Select the master/branch version to download.

## Setup
- The installer should already generate a command line shortcut, which already include the idf path. Moving the esp-idf folder to other place will make this shortcut to be invalid. So, in order to use the idy.py to build and program the controller, two steps are needed:
    1. Navigate to the esp-idf folder using **cmd**, then run .\export.bat. This will add the locations of the esp tools to current terminal's PATH.
    
    2. Type idf.py, it should pop up the software usage information.

    3. Start using idf.py to compile and debug.
