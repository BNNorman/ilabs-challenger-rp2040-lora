# ilabs-challenger-rp2040-lora

## intro

I have only tested the LoRa section of this board.

When I first got this board I tried to install pico-lorawan (Sandeep Mistry) from https://github.com/ArmDeveloperEcosystem/lorawan-library-for-pico but couldn't get it to work till I found that the board uses spi1 not the default spi0 and that the file src/boards/rp2040/spi-board.c has a bug preventing the use of spi1.

Sandeep has been informed but, as of writing, the suggested change hasn't apeared in his code.

To get the pico-lorawan code to work you need to edit SpiInit(..) in src/boards/rp2040/spi-board.c and add one line of code as follows:-

```
void SpiInit( Spi_t *obj, SpiId_t spiId, PinNames mosi, PinNames miso, PinNames sclk, PinNames nss )
{
    obj->SpiId=spiId;   //<----- add this line
    spi_init((spiId == 0) ? spi0 : spi1, 10 * 1000 * 1000);
    spi_set_format((spiId == 0) ? spi0 : spi1, 8, SPI_CPOL_0, SPI_CPHA_0, SPI_MSB_FIRST);
    gpio_set_function(mosi, GPIO_FUNC_SPI);
    gpio_set_function(miso, GPIO_FUNC_SPI);
    gpio_set_function(sclk, GPIO_FUNC_SPI);
}


```


## Firmware Configuration information

I have converted the board schematic to a JPG since not everyone has Eagle nor wants to install it. From the schematic you can see that the Radio pins are labelled LORA_xxx and that these pins are not available on the breakout connectors.

```
Pin     GPIO  SX1276
LORA_CS   9   NSS
LORA_SCK  10  SCK
LORA_SDO  11  MOSI
LORA_SDI  12  MISO
LORA_RST  13  RST
LORA_DIO0 14  DIO0
LORA_DIO1 15  DIO1
LORA_DIO2 18  DIO2  Not used
```

In the examples folder locate the main.c for the example you are working with and add the pins being used as below:-

```
// pin configuration for SX12xx radio module
const struct lorawan_sx12xx_settings sx12xx_settings = {
    .spi = {
        .inst = spi1,
        .mosi = 11,
        .miso = 12,
        .sck  = 10,
        .nss  = 9
    },
    .reset = 13,
    .dio0  = 14,
    .dio1  = 25
};
```














