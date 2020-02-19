# Develop STM32 in VS Code

## Tools needed
- Make
- GNU Arm Embedded Toolchain
- OpenOCD (Search OpenOCD for Windows)
- VS Code & cortex-debug plugin.

## Generate makefile using STM32CubeMX
- Generate stm32 project using STM32CubeMX
- To make the vscode fully support the autocomplete feature, after generating the project, compilier flags need to be added into the vscode configuration.
    - The flags are in the makefile
    - Add these flags in "Defines" section in "C/C++ Configurations".

## VSCode C/C++ Configurations for STM32 Project
- Add another configuration
- Note the defines are essential to enable the autocomplete feature.
- Example:
```javascript
{
    "name": "STM32L072",
    "includePath": [
        "${workspaceFolder}/**",
        "Core/Inc/**",
        "Drivers/STM32L0xx_HAL_Driver/Inc/**",
        "Drivers/STM32L0xx_HAL_Driver/Inc/Legacy/**",
        "Drivers/CMSIS/Device/ST/STM32L0xx/Include/**",
        "Drivers/CMSIS/Include/**"
    ],
    "defines": [
        "_DEBUG",
        "UNICODE",
        "_UNICODE",
        "DEBUG",
        "USE_HAL_DRIVER",
        "STM32L072xx"
    ],
    "compilerPath": "C:\\Program Files (x86)\\ARM GNU Tools\\bin\\arm-none-eabi-gcc.exe\",
    "cStandard": "c11",
    "cppStandard": "c++17",
    "intelliSenseMode": "clang-x64"
}
```

## Debug using vscode
- Make sure "cortex-debug" vscode plugin and "openocd" are installed.
- Under "Debug and Run" section, click "create a launch.json file".
- Add a cortex-debug configuration like this:
    ```javascript
    //launch.json file
    {
        "version": "0.2.0",
        "configurations": [
            {
                "type": "cortex-debug",             // type must be cortex-debug
                "request": "launch",
                "servertype": "openocd",            // servertype must be openocd
                "cwd": "${workspaceRoot}",          // current working directory
                "executable": "./build/Vscode.elf", // executable file under build/
                "name": "F429",                     // name of the configuration

                /* Note the order of the following configurations are important.
                debugger config must be prior to the controller config. */

                "configFiles": [                    // configurations used by openocd
                    "interface/stlink-v2-1.cfg",    // debugger type
                    "target/stm32f4x.cfg"           // controller family
                ],
                
                /* Create a preLaunchTask to automate the build process.
                In addition to this file, another file called tasks.json is needed*/
                "preLaunchTask": "build"
            }
        ]
    }
    ```

- configFiles for different debugger and mcu can be found under openocd/scripts/ folder:
    - Windows: mysys64/mingw64/share/openocd/scripts/
    - Mac: openocd/scripts/
    ```javascript
    // tasks.json file
    {
        // See https://go.microsoft.com/fwlink/?LinkId=733558
        // for the documentation about the tasks.json format
        "version": "2.0.0",
        "tasks": [
            {
                "label": "build",
                "type": "shell",
                "command": "make"
            }
        ]
    }
    ```

