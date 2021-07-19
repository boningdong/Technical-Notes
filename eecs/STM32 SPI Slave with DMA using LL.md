# STM32 SPI Slave with DMA using LL Library

This document records how to configure SPI using LL libraries.

## Procedures
### Part 1. Initialization
Assume SPI and SPI DMA are all disabled during initialization
1. Configure the GPIOs as Alternative Function Mode for the SPI bus.
  1.1 Enable the GPIO clocks
  1.2 Configure the required SPI bus pins
2. Enable the SPI clock
3. Configure the SPI:
  3.1 Mode: Master or Slave
  3.2 Direction: Half/Full Duplex, Single Line
  3.3 DataWidth, Bit Order
  3.4 Clock Parameters: Phase & Polarity
  3.5 NSS
  3.6 CRC
4. [DMA] Initialize DMA (refer to another DMA document)
### Part 2. Before Transfer
5. Make sure the SPI is disabled
6. Make sure the SPI DMA is disabled
7. Configure the SPI DMA Parity bit in the SPI register
8. [DMA] Set the memory address where the data will be stored or save to, and enable the DMA interrupt. And at last, enable the DMA channel.
9. Enable the SPI DMA request.
10. Enable the SPI host.

## Code Example
``` C
// Part 1. Initialization:
void spi_init() {
  // 1. Configure the GPIOs as Alternative Function Mode for the SPI bus.
  // Details are ignored
  spi_gpio_init();

  // 2. Enable the SPI clock
  __HAL_RCC_SPI3_CLK_ENABLE();
  
  // 3. Configure the SPI
  ErrorStatus status;
  LL_SPI_InitTypeDef cfg;
  cfg.Mode = LL_SPI_MODE_SLAVE;
  cfg.TransferDirection = LL_SPI_FULL_DUPLEX;
  cfg.DataWidth = LL_SPI_DATAWIDTH_8BIT;
  cfg.ClockPolarity = LL_SPI_POLARITY_LOW;
  cfg.ClockPhase = LL_SPI_PHASE_1EDGE;
  cfg.NSS = LL_SPI_NSS_HARD_INPUT;
  cfg.BitOrder = LL_SPI_MSB_FIRST;
  cfg.CRCCalculation = LL_SPI_CRCCALCULATION_DISABLE;
  status = LL_SPI_Init(SPI_HOST, &cfg);
  assert_param(status == SUCCESS);

  // 4. [DMA] Initialize DMA.
  // Details are ignored
  dma_init();
}

// Part 2. Before Transfer
void spi_transmit(void* address, uint32_t nbyte, uint32_t memory_alignment) {
  uint32_t data_count = nbyte;

  // 5. Make sure the SPI is disabled
  LL_SPI_Disable(SPI_HOST);

  // 6. Make sure the SPI DMA is disabled
  LL_SPI_DisableDMAReq_TX(SPI_HOST);    

  // 7. Configure the SPI DMA Parity bit in the SPI register 
  // This is only necessary when DMA transfer is halfword
  // It is used for dealing with the memory alignment
  if (memory_alignment == LL_DMA_MDATAALIGN_HALFWORD) {
    if ((nbyte & 0x01) == 0) {
      LL_SPI_SetDMAParity_TX(SPI_HOST, LL_SPI_DMA_PARITY_EVEN);
      data_count = nbyte >> 2; 
    } else {
      LL_SPI_SetDMAParity_TX(SPI_HOST, LL_SPI_DMA_PARITY_ODD);
      data_count = nbyte >> 2 + 1;
    }
  }

  // 8. [DMA] Set the memory address where the data will be stored or save to, and enable the DMA interrupt. And at last, enable the DMA channel.
  if (LL_DMA_IsEnabledChannel(DMA2, LL_DMA_CHANNEL_3))
    LL_DMA_DisableChannel(DMA2, LL_DMA_CHANNEL_3);  
  // config DMA source address and data length
  LL_DMA_SetMemoryAddress(DMA2, LL_DMA_CHANNEL_3, (uint32_t) address);
  LL_DMA_SetMemoryIncMode(DMA2, LL_DMA_CHANNEL_3, LL_DMA_MEMORY_INCREMENT);
  LL_DMA_SetMemorySize(DMA2, LL_DMA_CHANNEL_3, memory_alignment);
  LL_DMA_SetDataLength(DMA2, LL_DMA_CHANNEL_3, data_count);
  // config DMA interrupt
  LL_DMA_EnableIT_HT(DMA2, LL_DMA_CHANNEL_3);
  LL_DMA_EnableIT_TC(DMA2, LL_DMA_CHANNEL_3);
  LL_DMA_EnableIT_TE(DMA2, LL_DMA_CHANNEL_3);
  // enable DMA
  LL_DMA_EnableChannel(DMA2, LL_DMA_CHANNEL_3);

  // 9. Enable the SPI DMA request.
  // This line will enable the SPI to send the request signal to DMA to indicate the transfer
  // If the SPI is a receiver, RXNE (RX not empty) will be the signal triggering the DMA transfer
  // If the SPI is a transmitter, TXE (TX not empty) will be the signal triggering the DMA transfer 
  LL_SPI_EnableDMAReq_TX(SPI_HOST);

  // 10. Enable the SPI host.
  LL_SPI_Enable(SPI_HOST);
}
```



