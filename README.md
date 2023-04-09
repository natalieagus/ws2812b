## WS2812B Driver

WS2812B is a intelligent control LED light source that the control circuit and RGB chip are integrated in a package of 5050 components. You The data transfer protocol use single **NZR** communication mode. This driver is written in [Lucid](https://alchitry.com/lucid) and is meant to be run on [Alchitry Au](https://www.sparkfun.com/products/16527) Boards + [Alchitry Br](https://www.sparkfun.com/products/16524) prototype element board.

## Demo

Upon compilation, connect the `DIN` pin of WS2812B to Br pin `C49`, as defined in the constraint file:

```
pin outled C49;
```

You should observe **three** LEDs in the strip lit up: one blue, one red, and one green as defined in `au_top.luc`:

```
  const LEDCOLOR = {hFF0000, h00FF00, h0000FF}; // blue for the third LED, red for the second LED, green for the first LED (nearest to the connector)
```

## Usage

The file `ws2812b_writer.luc` receives 24 bit color input, and 1 bit flag signal to activate or deactivate this LED strip. Each color is encoded in 24bits of data. WS2812B expects the first 8 bits sent to be G, then R (next 8), then B (following next 8).

It outputs `pixel_address`, which is the address of the current LED that should be receiving the 24 bit input color. It should ideally be connected to a ROM which receives `pixel_address` as address and outputs 24 bits of value for as long as `pixel_address` is set constant.
