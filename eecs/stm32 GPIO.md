# STM32 GPIO Notes
## GPIO Registers
- GPIO_ODR is the output data register.
- GPIO_BSRR register is used to set/reset the GPIO_ODR without locking it.

## Alternative functions
- Alternative features like ADC, DAC, COMP requires configure GPIO to **analog mode**.

## Interrupt
- GPIO interrupt requires configure the GPIO mode to input mode.
- Follow 3 steps to configure the interrupt.
  - Write 1 to the corresponding EXTI mask.
  - Configure the rising edge & falling edge trigger.
  - Enable the correponding EXTI interrupt in NVIC.
  - Code Example:
  ```c++
  // Configure GPIO port
  GPIO_TypeDef *gpioc = GPIOC;
  // Configure as input mode
  gpioc->MODER &= ~(GPIO_MODER_MODE13_0 | GPIO_MODER_MODE13_1);
  // Set to nopull
  gpioc->PUPDR &= ~(GPIO_PUPDR_PUPDR13_0 | GPIO_PUPDR_PUPDR13_1);
  // Configure interrupt mode
  EXTI_TypeDef *exti = EXTI;
  // Disable the mask (mask will mask out the interrupt trigger)
  exti->IMR1 |= EXTI_IMR1_IM13;
  // Configure the rising edge trigger.
  exti->RTSR1 |= EXTI_RTSR1_RT13;
  // Enable the correponding interrupt in NVIC
  HAL_NVIC_SetPriority(EXTI15_10_IRQn, 3, 0);
  HAL_NVIC_EnableIRQ(EXTI15_10_IRQn);
  ```
