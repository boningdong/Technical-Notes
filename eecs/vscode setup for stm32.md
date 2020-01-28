# Develop STM32 in VS Code

## Tools needed
- Make
- GNU Arm Embedded Toolchain
- OpenOCD
- VS Code & cortex-debug plugin.

## Generate makefile using STM32CubeMX
- Generate stm32 project using STM32CubeMX
- To make the vscode fully support the autocomplete feature, after generating the project, compilier flags need to be added into the vscode configuration.
    - The flags are in the makefile
    - Add these flags in "Defines" section in "C/C++ Configurations".

## Debug using vscode
- Make sure "cortex-debug" vscode plugin and "openocd" are installed.
- Under "Debug and Run" section, click "create a launch.json file".
- Add a cortex-debug configuration like this:
    ```javascript
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
        ]
    }
    ```
    - configurations can be found under openocd/scripts/ folder:
        - Windows: mysys64/mingw64/share/openocd/scripts/
        - Mac: openocd/scripts/
