# Develop STM32 in VS Code

## Generate makefile using STM32CubeMX
- Generate stm32 project using STM32CubeMX
- To make the vscode fully support the autocomplete feature, after generating the project, compilier flags need to be added into the vscode configuration.
    - The flags are in the makefile
    - Add these flags in "Defines" section in "C/C++ Configurations".

## Debug using vscode
- Under "Debug and Run" section, click "create a launch.json file".
- 