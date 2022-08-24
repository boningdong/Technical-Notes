# RTOS Notes
## Project I
- osDelay:
    - osDelay will block the current task for the delay.
    - is implemented using vTaskDelay(const TickType_t xTicksToDelay), which delays specific ticks.
    - osDelay actually delays milliseconds, which is calculated by ticks = millisec / portTICK_PERIOD_MS.
    - portTICK_PERIOD_MS is how many ms per tick (ms/tick).
    - note this is not precise.
- vTaskDelay:
    - the RTOS specific function to achieve osDelay.
    - under the default settings, calling vTaskDelay(x) will delay for x ms because, x is the tick numbers and in default freeRTOS setting, the default tick is 1ms.
    - note this is not precise.

## Project II - Single Periodic Task with an Accurate Delay
- osDelayUntil / vTaskDelayUntil (*pxPreviousWakeTime, xTimeIncrement):
    - provide a way to delay precisely.
    - need to enable this in the RTOS config file. (Enable this from Cube if using STM32CubeMX)
    - DelayUntil operation:
        1. Load the TaskTimeStamp with the current (starting) tick value (TaskTimeStamp = xTaskGetTickCount()); 
        2. Execute the application code.
        3. Call the DelayUntil function (osDelayUntil(&TaskTimeStamp, 2000);
    - the idea of vTaskDelayUntil is to unblock the current thread at a specific time calculated by (pxPreviousWakeTime + xTimeIncrement).
    - the pxPreviousWakeTime will be updated to the latest value when the thread is unblocked.
    - note: the pxPreviousWakeTime should be initialized before first calling the vTaskDelayUntil function.
    - note: the only to implement periodic tasking is through using osDelayUntil.

## Project III - Multiple Periodic Tasks

