## WS2812B Driver

WS2812B is a intelligent control LED light source that the control circuit and RGB chip are integrated in a package of 5050 components. You The data transfer protocol use single **NZR** communication mode. This driver is written in [Lucid](https://alchitry.com/lucid) and is meant to be run on [Alchitry Au](https://www.sparkfun.com/products/16527) Boards + [Alchitry Br](https://www.sparkfun.com/products/16524) prototype element board.

## Demo

Upon compilation, connect the `DIN` pin of WS2812B to Br pin `C49`, as defined in the constraint file:

```
pin outled C49;
```

You should observe **12** LEDs in the strip lit up in the following color:

```
  (connector) WHITE WHITE BLUE WHITE RED WHITE GREEN WHITE WHITE BLUE BLUE BLUE (end of strip)
```

You can use `io_dip` to **color encode** 12 pixels and then by pressiong `io_button[0]` (up button) on io shield to **register** and see the effect on the LED strip. For instance, if your dip switches is put as follows (left to right):

```
11110000 01011010 00001100
```

It will light up the LED in this color:

```
  (connector) GREEN WHITE GREEN GREEN BLUE BLUE RED RED GREEN GREEN WHITE WHITE (end of strip)
```

This is due to this encoding:

```
  // WHITE (11), BLUE (10), RED (01), GREEN (00)
  const LEDCOLOR = {24hFFFFFF, 24hFF0000, 24h00FF00, 24h0000FF};
```

We basically segments the color encoding as dictated by `io_dip` in groups of two bits, e.g:

```
11 11 00 00 01 01 10 10 00 00 11 00
```

and each encoding corresponds to 24-bit color, so we actually have the following being pumped into the writer.

```
24hFFFFFF 24hFFFFFF 24h0000FF 24h0000FF 24h00FF00 24h00FF00  24hFF0000 24hFF0000 24h0000FF 24h000000
```

## Usage

The file `ws2812b_writer.luc` receives 24 bit color input, and 1 bit flag signal to activate or deactivate this LED strip. Each color is encoded in 24bits of data. WS2812B expects the first 8 bits sent to be G, then R (next 8), then B (following next 8).

It outputs `pixel_address`, which is the address of the current LED that should be receiving the 24 bit input color. It should ideally be connected to a ROM which receives `pixel_address` as address and outputs 24 bits of value for as long as `pixel_address` is set constant.

It also outputs `reset` flag, which is `1` when the writer is at the `RESET` state. When `reset == 1`, we change the `color` input to the writer so that it can display a fresh new set of value to to the LEDs.
