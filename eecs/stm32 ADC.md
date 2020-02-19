# STM32 ADC
## Using HAL ADC library

### Overview
*Four* Steps need to be done to initialize an ADC module:
1. Define an `ADC_HandleTypeDef` variable.
2. Configure the ADC core using `ADC_HandleTypeDef::Init`.
3. Configure the ADC multimode using `ADC_MultiModeTypeDef`.
4. Configure the ADC channel using `ADC_ChannelConfTypeDef`.

### Details
- `ADC_HanldeTypeDef`: a variable can be regarded as ADC hardware. Basic initialization requires:
    - Instance: Take a ADC base address cast to ADC_TypeDef.
    - Init: Has type ADC_InitTypeDef. Details can be found in the next entry.
- `ADC_HandleTypeDef::Init`: ADC_InitTypeDef, which is used to configure the parameters related to the ADC core. Some examples about what these parameters are about:
    - Clock
    - Scanning / Sampling Mode
    - Trigger
    - DMA
- `ADC_MultiModeTypeDef`: Related to having multiple ADCs work together for multiple ADCs family. For example, configure interleave mode.
- `ADC_ChannelConfTypeDef`: Related to configure the sampling channel of the ADC. Some example parameters are:
    - Channel Number
    - Sampling Cycles
    - Differential or Single Ended