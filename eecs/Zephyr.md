
## Important Build System Variables
- ZEPHYR_BASE
	- usually no need to set this. `find_package(Zephyr)` will automatically set this.
	- [ ] How `find_package` actually finds it though?
- BOARD
	- can be supported boards or ported boards.
- CONF_FILE
	- the name of **one or more** Kconfig configuration fragments files.
- DTC_OVERLAY_FILE
### Three ways to set those variables
- use `west`
- use `environment variables`
- use `set(<variable> <value>)` statement in the `CMakeList.txt`
### Board Value
- cmake cache board value
- cmake command line (either through `west` or `cmake`)
- `environment variable` 
- set `BOARD` value in `CMakeLists.txt`
