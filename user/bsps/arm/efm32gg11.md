% SPDX-License-Identifier: CC-BY-SA-4.0

% Copyright (C) 2026 Allan N. Hessenflow

# efm32gg11 (Silicon Laboratories EFM32GG11 Family)

This BSP supports the
[Silicon Laboratories EFM32GG11 Giant Gecko Starter Kit](https://silabs.com/development-tools/mcu/32-bit/efm32gg11-starter-kit).
There are configuration options to adapt to other boards based on
any part in the EFM32GG11 family.

## Processor Variant

The processor type can be selected using `EFM32GG11_DEVICE`. The possible
values are `EFM32GG11B110`, `EFM32GG11B120`, `EFM32GG11B310`, `EFM32GG11B320`,
`EFM32GG11B420`, `EFM32GG11B510`, `EFM32GG11B520`, `EFM32GG11B820`, and
`EFM32GG11B840`. The default is `EFM32GG11B820`.

## Oscillators

The oscillators can be configured with the following configuration options:

- HFRCO:
  - `EFM32GG11_HFRCO_FREQ`: If non-zero, the HFRCO will be configured for the
    nearest pre-calibrated frequency. Units are Hertz.
- HFXO:
  - `EFM32GG11_HFXO_FREQ`: If non-zero, the HFXO will be enabled. Units are
    Hertz.
  - `EFM32GG11_HFXO_TUNING`: Controls the capacitance on the HFXO pins. The
    units are device dependent and in the range 0 - 511; see the processor
    reference manual and datasheet for details (register CMU_HFXOSTARTUPCTRL,
    field CTUNE).
  - `EFM32GG11_HFXO_TYPE`: This can be one of `XTAL`, `ACBUFEXTCLK`,
    `DCBUFEXTCLK`, `DIGEXTCLK`.
- LFRCO:
  If the LFXO is not enabled then the LFRCO will be configured for 32768Hz.
- LFXO:
  - `EFM32GG11_LFXO_FREQ`: If non-zero the LFXO will be enabled. Units are
    Hertz.
  - `EFM32GG11_LFXO_GAIN`: Controls the gain of the LFXO, in the range 0 - 3.
    See the processor reference manual for details (register CMU_LFXOCTRL,
    field GAIN).
  - `EFM32GG11_LFXO_TUNING`: Controls the capacitance on the LFXO pins. The
    units are device dependent and in the range 0 - 127. See the processor
    reference manual and datasheet for details (register CMU_LFXOCTRL, field
    TUNING).
  - `EFM32GG11_LFXO_TYPE`: This can be one of `XTAL`, `BUFEXTCLK`, `DIGEXTCLK`.

## Memory

This BSP supports being linked to place code in internal flash or internal
SRAM. It defaults to flash and is controlled by setting
`EFM32GG11_DEFAULT_LINKCMDS` to `linkcmds.intflash` or `linkcmds.intsram`.

## Clock Driver

The clock driver uses the `ARMv7-M Systick`.

## Console Driver

The console driver supports the on-chip UARTs and USARTs. They are
configured with the following options:

- `BSP_CONSOLE_BAUD`: controls the initial baud rate.
- `BSP_CONSOLE_USE_INTERRUPTS`: controls whether this driver is interrupt
  driven or polled.
- `EFM32GG11_CONSOLE_VCOM_ENABLE`: The EFM32GG11 Giant Gecko Starter Kit uses a
  GPIO pin to control connection of the serial port to the on-board jlink.
  This option enables that connection and defaults to `true`.
- `EFM32GG11_CONSOLE_TYPE`, `EFM32GG11_TTYSx_TYPE`: can be `UART` or `USART`.
- `EFM32GG11_CONSOLE_DEVICE_INDEX`, `EFM32GG11_TTYSx_DEVICE_INDEX`: which
  on-chip UART or USART to use.
- `EFM32GG11_CONSOLE_CTS_LOC`, `EFM32GG11_CONSOLE_RTS_LOC`,
  `EFM32GG11_CONSOLE_TX_LOC`, `EFM32GG11_CONSOLE_RX_LOC`,
  `EFM32GG11_TTYSx_CTS_LOC`, `EFM32GG11_TTYSx_RTS_LOC`,
  `EFM32GG11_TTYSx_TX_LOC`, `EFM32GG11_TTYSx_RX_LOC`: select the alternate
  function number for the CTS, RTS, TX, and RX pins. Set to -1 to disable the
  pin. See the processor data sheet for details (Pin Definitions:GPIO
  Functionality Table and Pin Definitions:Alternate Functionality Overview).
- `EFM32GG11_CONSOLE_CS_LOC`, `EFM32GG11_TTYSx_CS_LOC`: select the alternate
  function number for the CS pin (normally only used in specialized
  applications such as RS-485). Set to -1 to disable this pin. See the
  processor data sheet for details (Pin Definitions:GPIO Functionality Table
  and Pin Definitions:Alternate Functionality Overview).
- `EFM32GG11_CONSOLE_CTRL`, `EFM32GG11_TTYSx_CTRL`: extra control register bits
  to set for specialized applications such as RS-485. See the processor
  reference manual for details, register USART_CTRL.
- `EFM32GG11_CONSOLE_TIMING`, `EFM32GG11_TTYSx_TIMING`: modify timing for
  specialized applications such as RS-485. See the processor reference manual
  for details, register USART_TIMING.

## I2C Bus Driver

The I2C driver is configured using the following options:

- `EFM32GG11_I2C_x_INDEX`: which on-chip I2C module to use.
- `EFM32GG11_I2C_x_SCL_LOC`, `EFM32GG11_I2C_x_SDA_LOC`: select the alternate
  function numbers for the SCL and SDA pins. See the processor data sheet for
  details (Pin Definitions:GPIO Functionality Table and Pin
  Definitions:Alternate Functionality Overview).

I2C bus drivers are registered by the efm32gg11_i2c_register() function. See
[\<bsp/i2c.h>](https://gitlab.rtems.org/rtems/rtos/rtems/-/blob/main/bsps/arm/efm32gg11/include/bsp/i2c.h).

## SPI Bus Driver

The SPI driver is configured using the following options:

- `EFM32GG11_SPI_x_INDEX`: which on-chip USART module to use.
- `EFM32GG11_SPI_x_CLK_LOC`, `EFM32GG11_SPI_x_RX_LOC`,
  `EFM32GG11_SPI_x_TX_LOC`: select the alternate function numbers for the CLK,
  RX (MISO), and TX (MOSI) pins. See the processor data sheet for details (Pin
  Definitions:GPIO Functionality Table and Pin Definitions:Alternate
  Functionality Overview).
- `EFM32GG11_SPI_x_CS_PINS`: select GPIO pins to use for chip selects. This is
  an array of entries in the form `{port, bit, polarity, delay}`. `port` is 0
  for GPIOA, 1 for GPIOB, etc. `bit` is which bit in that GPIO port (0 - 15).
  `polarity` `true` means active high. `delay` is the minimum time after
  asserting chip select before transmitting/receiving data in bit times
  (0 - 255).
- `EFM32GG11_SPI_x_RX_DMA_CHANNEL`, `EFM32GG11_SPI_x_TX_DMA_CHANNEL`: select
  which DMA channels to use.

SPI bus drivers are registered by the efm32gg11_spi_register() function. See
[\<bsp/spi.h>](https://gitlab.rtems.org/rtems/rtos/rtems/-/blob/main/bsps/arm/efm32gg11/include/bsp/spi.h).

## TRNG Driver

A basic TRNG driver is included with the only interface being the posix
function `getentropy()`.

## GPIO Support

Functions are provided to configure and control GPIO pins. Functions are also
provided to register interrupt handlers and configure interrupts. See
[\<bsp/efm32gg_gpio.h>](https://gitlab.rtems.org/rtems/rtos/rtems/-/blob/main/bsps/arm/efm32gg11/include/bsp/efm32gg11_gpio.h).

## DMA Support

Functions are provided for DMA clients to register their own interrupt handlers
and enable/disable interrupts for their own channels. See
[\<bsp/efm32gg_dma.h>](https://gitlab.rtems.org/rtems/rtos/rtems/-/blob/main/bsps/arm/efm32gg11/include/bsp/efm32gg11_dma.h).
