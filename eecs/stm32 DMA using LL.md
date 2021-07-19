# STM32 DMA using LL Library

## Introduction
This document records the general procedures to use STM32 DMAs.
Several key points:
- DMA is used for moving data from one address to another address. (The addresses can be memory address or peripheral addresses), which off-loads the CPU utilization.
- DMAs use HCLK.
- DMA has many channels.
- Each channel is a like a pipe connecting two addresses. The channels are like basic units to move data from one address to another one.
- To configure the DMA, is actually to configure the DMA channels. (Ex. To transfer from SPI to memory, only one channel need to be configured to move data from SPI to a specific memory address)


## Procedures
### Part 1. Initialization
1. Enable DMA clock
2. Enable DMAMUX clock (depends on the controller being used)
3. Initialize the DMA (actually the DMA channel):
  3.1 Transfer Direction
  3.2 Mode: either circular or normal (fix address)
  3.3 Priority: used for channel arbitration
  3.4 Peripheral Request: Which triggers the DMA to request the DMA (start the transfer)
  3.5 Transfer Addresses, Data Size, Increment Mode
4. Enable Interrupt in NVIC   
### Part 2. Before transmission
5. Configure Transfer Addresses, Data Size, Increment Mode (If needed):
  5.1 Disable the channel being configured first
  5.2 Set the corresponding configurations
6. Enable the corresponding DMA interrupt flag
7. Enable the DMA channel
8. Configure and enable the peripheral if the request (trigger) is from the peripheral

## Code Examples:
The example is for slave mode SPI transfer using the STM32 LL library.

``` C
// Part 1. Initialization
void dma_init() {
  // 1. Enable DMA clock
  __HAL_RCC_DMA1_CLK_ENABLE();
  __HAL_RCC_DMA2_CLK_ENABLE();

  // 2. Enable DMAMUX clock
  // DMAMUX is used for building the connection between the DMA channel 
  // and the request source (the trigger for the DMA channel to start)
  __HAL_RCC_DMAMUX1_CLK_ENABLE();

  // 3. Initialize the DMA
  LL_DMA_InitTypeDef cfg;
  cfg.Direction = LL_DMA_DIRECTION_MEMORY_TO_PERIPH;
  cfg.Mode = LL_DMA_MODE_NORMAL;
  cfg.Priority = LL_DMA_PRIORITY_HIGH;
  cfg.PeriphRequest = LL_DMAMUX_REQ_SPI3_TX;
  cfg.PeriphOrM2MSrcAddress = LL_SPI_DMA_GetRegAddr(SPI_HOST);
  cfg.PeriphOrM2MSrcDataSize = LL_DMA_MDATAALIGN_BYTE;
  cfg.PeriphOrM2MSrcIncMode = LL_DMA_MEMORY_NOINCREMENT;
  LL_DMA_Init(DMA2, LL_DMA_CHANNEL_3, &cfg);

  // 4. Enable the NVIC interrupt for DMA
  HAL_NVIC_SetPriority(DMA2_Channel3_IRQn, 3, 3);
  HAL_NVIC_EnableIRQ(DMA2_Channel3_IRQn);
}

// Part 2. Initialization
// This part is only used for showing the idea
// The spi related code is being omitted
void spi_before_transfer(void* address, uint32_t nbyte) {
  // 5. Configure Transfer Addresses, Data Size, Increment Mode (If needed):
  // Here we need to configure the other part of the address (instead of
  // doing that directly in the dma_init function) because the address where
  // the data will be read from will be determined when this function is called
  // make sure DMA is off
  if (LL_DMA_IsEnabledChannel(DMA2, LL_DMA_CHANNEL_3))
    LL_DMA_DisableChannel(DMA2, LL_DMA_CHANNEL_3);  
  
  // config DMA source address and data length
  LL_DMA_SetMemoryAddress(DMA2, LL_DMA_CHANNEL_3, (uint32_t) address);
  LL_DMA_SetMemoryIncMode(DMA2, LL_DMA_CHANNEL_3, LL_DMA_MEMORY_INCREMENT);
  LL_DMA_SetMemorySize(DMA2, LL_DMA_CHANNEL_3, memory_alignment);
  LL_DMA_SetDataLength(DMA2, LL_DMA_CHANNEL_3, data_count);

  // 6. Enable the corresponding DMA interrupt flag
  LL_DMA_EnableIT_HT(DMA2, LL_DMA_CHANNEL_3);
  LL_DMA_EnableIT_TC(DMA2, LL_DMA_CHANNEL_3);
  LL_DMA_EnableIT_TE(DMA2, LL_DMA_CHANNEL_3);
  
  // 7. Enable the DMA channel
  LL_DMA_EnableChannel(DMA2, LL_DMA_CHANNEL_3);

  // 8. Configure and enable the peripheral if the request (trigger) is from the peripheral
}

```



